## 第八章 ContenetProvider

### 1、定义

ContentProvider主要用于在不同的应用程序之间实现数据共享的功能，它提供了一套完整的 机制，允许一个程序访问另一个程序中的数据，同时还能保证被访问数据的安全性。

### 2、运行时权限

6.0系统中 引入运行时权限功能。用户不需要在安装软件的时候一次性授权所有申请的权限，而是可以在软件的 使用过程中再对某一项权限申请进行授权。

**`权限分类`**

- 普通权限：不会直接威胁到用户的安全和隐私的权限，系统会自动授权

- 危险权限：可能会触及用户隐私或对设备安全性造成影响的权限，如获取设备联系人信息、定位设备等地理位置等，这部分权限申请，必须用户手动授权。

  [危险权限]()

  > 如果是这张表中的权限，就需要进行运行时权限 处理，否则，只需要在AndroidManifest.xml文件中添加一下权限声明就可以了。

  

| 权限组名             | 权限名                                                       |
| -------------------- | ------------------------------------------------------------ |
| CALENDAR             | READ_CALENDAR <br> WRITE_CALENDAR                            |
| CALL_LOG             | READ_CALL_LOG <br/>WRITE_CALL_LOG <br/>PROCESS_OUTGOING_CALLS |
| CAMERA               | CAMERA                                                       |
| CONTACTS             | READ_CONTACTS<br/> WRITE_CONTACTS <br/>GET_ACCOUNTS          |
| LOCATION             | ACCESS_FINE_LOCATION<br/> ACCESS_COARSE_LOCATION<br/> ACCESS_BACKGROUND_LOCATION |
| MICROPHONE           | RECORD_AUDIO                                                 |
| PHONE                | READ_PHONE_STATE<br/> READ_PHONE_NUMBERS<br/> CALL_PHONE <br/>ANSWER_PHONE_CALLS <br/>ADD_VOICEMAIL <br/>USE_SIP <br/>ACCEPT_HANDOVER |
| SENSORS              | BODY_SENSORS                                                 |
| ACTIVITY_RECOGNITION | ACTIVITY_RECOGNITION                                         |
| SMS                  | AEND_SMS <br/>RECEIVE_SMS <br/>READ_SMS <br/>RECEIVE_WAP_PUSH<br/> RECEIVE_MMS |
| STORAGE              | READ_EXTERNAL_STORAGE <br/>WRITE_EXTERNAL_STORAGE <br/>ACCESS_MEDIA_LOCATION |

`权限申请流程：`

1. 判断用户是否已授权，通过ContextCompat.checkSelfPermission()方法。

   参1：Context，参2：具体的权限名

2. 使用1中的方法的返回值，与PackageManager.PERMISSION_GRANTED比较，相等说明用于已经授权，不等没授权。

3. 如果已经授权，直接执行操作逻辑；如果没有授权，调用ActivityCompat.requestPermission方法申请授权。

   参1：Activity实例；参2：String数组，把要申请的权限名放在数组中；参3：请求吗，唯一值即可。

4. 调用完requestPermission，会弹出权限申请弹框，结果会回调到onRequestPermissionResult方法中，授权的结果会被封装在grantResults参数中，判断授权结果，执行具体的逻辑。

```kotlin
binding.makeCallBtn.setOnClickListener {    if(ContextCompat.checkSelfPermission(this,android.Manifest.permission.CALL_PHONE)!=PackageManager.PERMISSION_GRANTED){
        ActivityCompat.requestPermissions(this, arrayOf(android.Manifest.permission.CALL_PHONE),1)
    } else {
        call()
    }
}

override fun onRequestPermissionsResult(
        requestCode: Int,
        permissions: Array<out String>,
        grantResults: IntArray,
    ) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)
        when(requestCode){
            1 -> {
                if (grantResults.isNotEmpty()&&grantResults[0]==PackageManager.PERMISSION_GRANTED){
                    call()
                } else {
                    Toast.makeText(this,"you denied the permission",Toast.LENGTH_SHORT).show()
                }
            }
        }
    }
```

### 3、访问其他程序中的数据

> ContentProvider用法：
>
> - 使用现有的ContentProvider读取和操作相应程序中数据
> - 创建自己的ContentProvider，给程序的数据提供外部访问接口
>
> 如果一个应用程序通过ContentProvider对数据提供了外部访问的接口，那么其他的应用程序都可以对这部分数据进行访问。

#### 3.1 基本用法

访问ContentProvider中共享的数据，需要借助ContentResolver类，通过Context中的getConentResolver()获取该类的实例。ContentResolver类，提供一系列的操作：

- insert：添加数据
- update：更新数据
- delete：删除数据
- query：查询数据

ContentResolver中的方法，不接收表明参数，使用Uri参数代替，称为内容URI，给ContentProvider的数据建立唯一标识符。

URI组成：

- authority：对不同的应用程序区分，采用包名的方式命名。如com.example.app.provider
- path：对同一应用程序中不同的表做区分，通常添加到authority后面。如应用中两张表table1和table2，path命名：/table1或/table2

内容URI:`com.example.app.provider/table1`或`com.example.app.provider/table2`

标准下发，需要在字符串的头部添加协议声明：

```kotlin
content://com.example.app.provider/table2
content://com.example.app.provider/table1
```

内容URI字符串需要解析成Uri对象才可以作为参数传入：

```kotlin
val uri = Uri.parse("content://com.example.app.provider/table2")
```

使用Uri对象查询table1中的数据：

###### 查询数据

```kotlin
val cursor = contentResolver.query(uri,projection,selection,selectionArgs,sortOrder)
```



| query()方法参数 | 对应SQL部分               | 描述                             |
| --------------- | ------------------------- | -------------------------------- |
| uri             | from table_name           | 指定查询某个应用程序下的某一张表 |
| projection      | select column1, column2   | 指定查询的列名                   |
| selection       | where column = value      | 指定where的约束条件              |
| selectionArgs   | \-                        | 为where中的占位符提供具体的值    |
| sortOrder       | order by column1, column2 | 指定查询结果的排序方式           |

遍历数据：

```kotlin
while (cursor.moveToNext()) {
 val column1 = cursor.getString(cursor.getColumnIndex("column1"))
 val column2 = cursor.getInt(cursor.getColumnIndex("column2"))
}
cursor.close()
```

###### 添加数据

> 将待添加的数据组装到ContentValues中，然后调用ContentResolver的 insert()方法，将Uri和ContentValues作为参数传入

```kotlin
val values = contentValuesOf("column1" to "text","column2" to 1)
contentResolver.insert(uri,values)
```

###### 更新数据

```kotlin
val values = contentValuesOf("column1" to "")
contentResolver.update(uri,values,"column1 = ? and column2 = ?",arrayOf("text","1"))
```

###### 删除数据

```kotlin
contetnResolver.delete(uri,"column2=?",arrayOf("1"))
```

#### 3.2读取联系人数据



```kotlin
class ContactsActivity : AppCompatActivity() {
    lateinit var binding:ActivityContactsBinding
    private val contactsList = ArrayList<String>()
    private lateinit var adapter:ArrayAdapter<String>

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityContactsBinding.inflate(layoutInflater)
        setContentView(binding.root)
        adapter = ArrayAdapter(this,android.R.layout.simple_list_item_1,contactsList)
        binding.contactsView.adapter = adapter
        if (ContextCompat.checkSelfPermission(this,Manifest.permission.READ_CONTACTS)!=PackageManager.PERMISSION_GRANTED){
            ActivityCompat.requestPermissions(this, arrayOf(Manifest.permission.READ_CONTACTS),1)
        } else {
            readContacts()
        }
    }
    @SuppressLint("Range")
    private fun readContacts(){
        //查询联系人信息
        contentResolver.query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI,null,null,null,null)?.apply {
            while(moveToNext()){
                //获取联系人姓名
                val displayName = getString(getColumnIndex(ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME))
                //获取联系人电话
                val number = getString(getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER))
                contactsList.add("$displayName\n$number")
            }
            adapter.notifyDataSetChanged()
            close()
        }
    }

    override fun onRequestPermissionsResult(
        requestCode: Int,
        permissions: Array<out String>,
        grantResults: IntArray,
    ) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)
        when(requestCode){
            1->{
                if (grantResults.isNotEmpty()&&grantResults[0] == PackageManager.PERMISSION_GRANTED){
                    readContacts()
                } else {
                    Toast.makeText(this,"You denied the permission",Toast.LENGTH_SHORT).show()
                }
            }
        }
    }
}


```

权限申请：

```xml
<!--读取联系人权限-->
<uses-permission android:name="android.permission.READ_CONTACTS"/>
```

#### 4、创建ContentProvider

