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

#### 1、创建Contentprovider步骤

想要实现程序共享数据的功能，通过新建类继承ContentProvider的方式来实现。重写其中的6个抽象方法:

- `onCreate`:初始化，通常完成对数据库的创建和升级，返回True表示初始化成功，false表示失败

- `query`：查询数据，查询的结果放在cursor对象返回

  - uri：确定查询的表
  - projectino：查询的列
  - selection、selectionArgs：用于约束查询哪些行
  - sortOrder：对查询结果进行排序

- `insert`：插入数据，添加完成后，返回一个用于表示这条新纪录的URI

  - uri：确定要添加的表
  - values：待添加的数据

- `update`：更新已有的数据，返回受影响的行数

  - uri：确定要更新的表
  - value：要更新的数据
  - selection、selectionArgs：用于约束更新哪些行

- `delete`：要删除的数据

  - uri：确定要删除的表
  - selection、selectionArgs：用于约束删除哪些行

- `getType`:根据传入的内容URI，返回相应的MIME类型

  一个内容URI对应的MIME字符串主要有3部分组成：

  - 必须以vnd开头
  - 如果内容URI以路径结尾，则后接android.cursor.dir/;如果内容URI以id结尾，则后接android.cursor.item/
  - 最后接上vnd.<authority>.<path>

  对于`content://com.example.app.provider/table1`这个内容URI，对应的MIME类型：

  ```
  vnd.android.cursor.dir/vnd.com.example.app.provider.table1
  ```

  对于`content://com.example.app.provider/table1/1`这个内容URI，对应的MIME类型：

  ```
  vnd.android.cursor.item/vnd.com.example.app.provider.table1
  ```

  

```kotlin
class MyProvider:ContentProvider() {
    override fun onCreate(): Boolean {
        TODO("Not yet implemented")
    }

    override fun query(
        uri: Uri,
        projection: Array<out String>?,
        selection: String?,
        selectionArgs: Array<out String>?,
        sortOrder: String?,
    ): Cursor? {
        TODO("Not yet implemented")
    }

    override fun getType(uri: Uri): String? {
        TODO("Not yet implemented")
    }

    override fun insert(uri: Uri, values: ContentValues?): Uri? {
        TODO("Not yet implemented")
    }

    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<out String>?): Int {
        TODO("Not yet implemented")
    }

    override fun update(
        uri: Uri,
        values: ContentValues?,
        selection: String?,
        selectionArgs: Array<out String>?,
    ): Int {
        TODO("Not yet implemented")
    }
}
```

`标准URI：`

> 以路径结尾表示期望访问该表中所有的数据，以id结尾表 示期望访问该表中拥有相应id的数据

形式一：访问com.example.app应用中的table1表中的数据

```kotlin
content://com.example.app.provider/table1
```

形式二：访问com.example.app应用这table1表中id为1的数据。

```kotlin
content://com.example.app.provider/table1/1
```

通过通配符匹配URI的格式，规则：

- ***表示匹配任意长度的任意字符。**
- **\#表示匹配任意长度的数字。**

匹配任意表的URI格式：

```kotlin
content://com.example.app.provider/*
```

匹配table1表中任意一行数据的URI格式：

```
content://com.example.app.provider/table1/#
```

借助UriMathcher类，实现匹配URI功能，提供一个addURI的方法，接收三个参数，分别吧authority、path和自定义代码传入。当调用UriMathcher的match方法时，可以将一个Uri对象传入，返回值是某个能够匹配这个Uri对象所对应的自定义代码，利用这个代码，判断调用方期望访问的是哪个表中的数据。

```kotlin
package com.example.kotlinlearn.contentproviderdemo.customcontentprovider

import android.content.ContentProvider
import android.content.ContentValues
import android.content.UriMatcher
import android.database.Cursor
import android.net.Uri

/**
 * Author: Zhangtao
 * Created on 2022/10/21 9:24
 * Desc:
 */
class MyProvider:ContentProvider() {

    private val table1Dir = 0
    private val table1Item = 1
    private val table2Dir = 2
    private val table2Item = 3

    //获得uriMatcher对象
    private val uriMatcher = UriMatcher(UriMatcher.NO_MATCH)

    init {
        //添加匹配的内容格式
        uriMatcher.addURI("com.example.app.provider","table1",table1Dir)
        uriMatcher.addURI("com.example.app.provider","table1/#",table1Item)
        uriMatcher.addURI("com.example.app.provider","table2",table2Dir)
        uriMatcher.addURI("com.example.app.provider","table2/#",table2Item)
    }

    override fun onCreate(): Boolean {
        TODO("Not yet implemented")
    }

    override fun query(
        uri: Uri,
        projection: Array<out String>?,
        selection: String?,
        selectionArgs: Array<out String>?,
        sortOrder: String?,
    ): Cursor? {
        when(uriMatcher.match(uri)){
            table1Dir -> {
                //查询table1表中的所有的数据
            }
            table1Item -> {
                //查询table1表中的单条数据
            }
            table2Dir -> {
                //查询table2表中的所有的数据
            }
            table2Item -> {
                //查询table2表中的单条数据
            }
        }
        return null
    }

    override fun getType(uri: Uri) = when(uriMatcher.match(uri)){
        table1Dir -> "vnd.android.cursor.dir/vnd.com.example.app.provider.table1"
        table1Item -> "vnd.android.cursor.item/vnd.com.example.app.provider.table1"
        table2Dir -> "vnd.android.cursor.dir/vnd.com.example.app.provider.table2"
        table2Item -> "vnd.android.cursor.item/vnd.com.example.app.provider.table2"
        else -> null
    }

    override fun insert(uri: Uri, values: ContentValues?): Uri? {
        TODO("Not yet implemented")
    }

    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<out String>?): Int {
        TODO("Not yet implemented")
    }

    override fun update(
        uri: Uri,
        values: ContentValues?,
        selection: String?,
        selectionArgs: Array<out String>?,
    ): Int {
        TODO("Not yet implemented")
    }
}
```



#### 案例

1、定义Databaseprovider

```kotlin
package com.example.kotlinlearn.contentproviderdemo.customcontentprovider

import android.content.ContentProvider
import android.content.ContentValues
import android.content.UriMatcher
import android.database.Cursor
import android.net.Uri
import com.example.kotlinlearn.databasedemo.sqlitedemo.MyDatabaseHelper

/**访问datebase中的数据
 * authority：com.example.database.provider
 * */
class DatebaseProvider : ContentProvider() {

    private val bookDir = 0
    private val bookItem = 1
    private val categoryDir = 2
    private val categoryItem = 3
    private val authority = "com.example.database.provider"
    private var dbHelper:MyDatabaseHelper? = null

    //获得uriMatcher对象
    /**
     * by lazy代码是kotlin中一种懒加载技术，一开始不会执行，只有当uriMatcher变量首次被调用的时候执行，并将代码中
     * 最后一行代码的返回值赋给uriMatcher。
     * */
    private val uriMatcher by lazy {
        val matcher = UriMatcher(UriMatcher.NO_MATCH)
        matcher.addURI(authority,"book",bookDir)
        matcher.addURI(authority,"book/#",bookItem)
        matcher.addURI(authority,"category",categoryDir)
        matcher.addURI(authority,"category/#",categoryItem)
        matcher
    }

    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<String>?) = dbHelper?.let {
        //删除数据
        val db = it.writableDatabase
        val deleteRows = when(uriMatcher.match(uri)){
            bookDir -> db.delete("Book",selection,selectionArgs)
            bookItem -> {
                val bookId = uri.pathSegments[1]
                db.delete("Book","id=?", arrayOf(bookId))
            }
            categoryDir -> db.delete("Category",selection,selectionArgs)
            categoryItem -> {
                val categoryId = uri.pathSegments[1]
                db.delete("Category","id=?", arrayOf(categoryId))
            }
            else -> 0
        }
        deleteRows
    } ?:0

    override fun getType(uri: Uri) = when(uriMatcher.match(uri)){
        bookDir -> "vnd.android.cursor.dir/vnd.$authority.book"
        bookItem -> "vnd.android.cursor.item/vnd.$authority.book"
        categoryDir -> "vnd.android.cursor.dir/vnd.$authority.category"
        categoryItem -> "vnd.android.cursor.item/vnd.$authority.category"
        else -> null
    }

    override fun insert(uri: Uri, values: ContentValues?) = dbHelper?.let {
        //添加数据
        val db = it.readableDatabase
        val uriReturn = when(uriMatcher.match(uri)){
            bookDir,bookItem -> {
                val newBookId = db.insert("Book",null,values)
                Uri.parse("content://$authority/book/$newBookId")
            }
            categoryItem,categoryDir -> {
                val categoryId = db.insert("Category",null,values)
                Uri.parse("content://$authority/category/$categoryId")
            }
            else -> null
        }
        uriReturn
    }

    override fun onCreate() = context?.let {
        dbHelper = MyDatabaseHelper(it,"BookStore",7)
        true
    } ?:false

    override fun query(
        uri: Uri, projection: Array<String>?, selection: String?,
        selectionArgs: Array<String>?, sortOrder: String?,
    ) = dbHelper?.let {
        //查询数据
        val db = it.readableDatabase
        val cursor = when(uriMatcher.match(uri)){
            bookDir -> {
                db.query("Book",projection,selection,selectionArgs,null,null,sortOrder)
            }
            bookItem -> {
                val bookId = uri.pathSegments[1]
                db.query("Book",projection,"id=?", arrayOf(bookId),null,null,sortOrder)
            }
            categoryDir -> {
                db.query("Category",projection,selection,selectionArgs,null,null,sortOrder)
            }
            categoryItem -> {
                val categoryId = uri.pathSegments[1]
                db.query("Category",projection,"id=?", arrayOf(categoryId),null,null,sortOrder)
            }
            else -> null
        }
        cursor
    }

    override fun update(
        uri: Uri, values: ContentValues?, selection: String?,
        selectionArgs: Array<String>?,
    ) = dbHelper?.let {
        //更新数据
        val db = it.readableDatabase
        val updateRows = when(uriMatcher.match(uri)){
            bookDir -> db.update("Book",values,selection,selectionArgs)
            bookItem -> {
                val bookId = uri.pathSegments[1]
                db.update("Book",values,"id =?", arrayOf(bookId))
            }
            categoryDir -> db.update("Category",values,selection,selectionArgs)
            categoryItem ->{
                val categoryId = uri.pathSegments[1]
                db.update("Category",values,"id=?", arrayOf(categoryId))
            }
            else -> 0
        }
        updateRows
    } ?:0
}
```

2、AndroidManifest.xml文件

```xml
<provider
    android:name=".contentproviderdemo.customcontentprovider.DatebaseProvider"
    android:authorities="com.example.database.provider"
    android:enabled="true"
    android:exported="true"></provider>
```

3、定义数据库

```kotlin
class MyDatabaseHelper(val context: Context,name:String,version:Int): SQLiteOpenHelper(context,name,null,version){

    private val createBook = "create table Book (" +
            " id integer primary key autoincrement," +
            "author text," +
            "price real," +
            "pages integer," +
            "name text," +
            "category_id integer)" //建立关联字段
    private val createCategory = "create table CateGory (" +
            "id integer primary key autoincrement," +
            "category_name text," +
            "category_code integer)"

    //数据表的创建
    override fun onCreate(db: SQLiteDatabase) {
        db.execSQL(createBook)// 在execSQL执行建表语句，保证在数据库创建完成的同时可以成功创建BOOK表。
        db.execSQL(createCategory)
        //跨程序访问时不能使用Toast
        //Toast.makeText(context,"create succeeded",Toast.LENGTH_SHORT).show()
    }

    //更新数据库
    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        //升级数据库的方式，如果是首次安装，if中所有操作都会执行。
        //覆盖安装，只会在原来的基础上，执行最新的if
        if (oldVersion <= 1){
            db.execSQL(createCategory)
        }
        if(oldVersion <= 2) {
            db.execSQL("alter table Book add column category_id integer") //新增关联字段
        }
    }
}
```

4、定义外部访问程序

```kotlin
package com.example.providerdemo

import android.annotation.SuppressLint
import android.net.Uri
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import androidx.core.content.contentValuesOf
import com.example.providerdemo.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {
    lateinit var binding: ActivityMainBinding
    private val authority = "com.example.database.provider"

    var bookId:String? = null

    @SuppressLint("Range")
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        binding.addData.setOnClickListener {
            //添加数据
            val uri = Uri.parse("content://$authority/book")
            val values = contentValuesOf("name" to "A Clash of Kings","author" to "George Martin",
            "pages" to 1040,"price" to 22.85)
            val newUri = contentResolver.insert(uri,values)
            bookId = newUri?.pathSegments?.get(1)
        }
        binding.queryData.setOnClickListener {
            //查询数据
            val uri = Uri.parse("content://$authority/book")
            contentResolver.query(uri,null,null,null,null)?.apply {
                while (moveToNext()){
                    val name = getString(getColumnIndex("name"))
                    val author = getString(getColumnIndex("author"))
                    val pages = getInt(getColumnIndex("pages"))
                    val price = getDouble(getColumnIndex("price"))
                    Log.i("Zhangtao", "book name is : $name,author is : $author, pages is : $pages,price is : $price")
                }
                close()
            }
        }
        binding.updateData.setOnClickListener {
            //更新数据
            bookId?.let {
                val uri = Uri.parse("content://$authority/book/$it")
                val values = contentValuesOf("name" to "A Storm of Swords","pages" to 1216,"price" to 240)
                contentResolver.update(uri,values,null,null)
            }
        }
        binding.deleteData.setOnClickListener {
            //删除数据
            bookId?.let {
                val uri = Uri.parse("content://$authority/book/$it")
                contentResolver.delete(uri,null,null)
            }
        }
    }
}
```

