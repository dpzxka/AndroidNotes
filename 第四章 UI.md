# 第四章：UI

## 一、常用控件

![image-20221011143438665](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221011143438665.png)

#### 1、TextView

```xml
<TextView
    android:id="@+id/textViewUI"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="36dp"
    android:gravity="center"
    android:text="this is textView"
    android:textColor="#00ff00"
    android:textSize="20sp"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />
```

> 用android:gravity来指定文字的对齐方式。可选值有top、bottom、start、 end、center等，可以用“|”来同时指定多个值，"center"，效果等同 于"center_vertical|center_horizontal"，表示文字在垂直和水平方向都居中对齐。

#### 2、Button

```xml
<Button
    android:textAllCaps="false"
    android:text="this is button"
    android:id="@+id/buttonUI"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="43dp"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toBottomOf="@+id/textViewUI" />
```

#### 3、EditText

```xml
<EditText
    android:maxLines="2"
    android:id="@+id/editTextUI"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="48dp"
    android:hint="This is a new Edit"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintHorizontal_bias="0.498"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toBottomOf="@+id/buttonUI" />
```

> android:maxLines指定了EditText的最大行数，超过的部分会上下滚动

#### 4、ImageView

```xml
<ImageView
    android:id="@+id/imageViewUI"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="32dp"
    android:src="@drawable/testdemo"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintHorizontal_bias="0.122"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toBottomOf="@+id/editTextUI" />
```

> android:src属性给ImageView指定图片资源。
>
> 还可以动态设置：`imageView.setImageResource(R.drawable.img_2)`

#### 5、ProgressBar

```xml
<!--自定义进度条样式-->
<ProgressBar
    style="?android:attr/progressBarStyleHorizontal"
    android:max="100"
    android:id="@+id/progressBarUI"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintHorizontal_bias="0.0"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toBottomOf="@+id/imageViewUI" />
```

#### 6、AlertDialog

```java
AlertDialog.Builder(this).apply {
    setTitle("This is Dialog")
    setMessage("somethings important")
    setCancelable(false)
    setPositiveButton("Ok"){dialog,which->
        //Toast.makeText(UIWidgetActivity::class.java,"点击了确定按钮",Toast.LENGTH_SHORT)
    }
    setNegativeButton("cancel"){dialog,which->}
    show()
```

## 二、常用布局

#### 1、LinearLayout

> 线性布局
>
> android:orientation属性指定了排列方向是vertical，如果指定的 是horizontal，控件就会在水平方向上排列

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/linear_Btn"
        android:text="LinearLayoutTest"/>
    <Button
        android:id="@+id/relative_Btn"
        android:text="relativeBtn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
</LinearLayout>
```

#### 2、RelativeLayout

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".uidemo.layoutdemo.RelativeLayoutActivity">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="300dp">
        <Button
            android:id="@+id/relativeBtn1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentStart="true"
            android:layout_alignParentTop="true"
            android:text="relativeBtn1"/>
        <Button
            android:id="@+id/relativeBtn2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentEnd="true"
            android:layout_alignParentTop="true"
            android:text="relativeBtn2"/>
        <Button
            android:id="@+id/relativeBtn3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="relativeBtn3"/>
        <Button
            android:id="@+id/relativeBtn4"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentBottom="true"
            android:layout_alignParentStart="true"
            android:text="relativeBtn4"/>
        <Button
            android:id="@+id/relativeBtn6"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentBottom="true"
            android:layout_alignParentEnd="true"
            android:text="relativeBtn5"/>
    </RelativeLayout>
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="300dp">
        <Button
            android:id="@+id/relativeBtn7"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="relativeBtn7"/>
        <Button
            android:id="@+id/relativeBtn8"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_above="@id/relativeBtn7"
            android:layout_toStartOf="@id/relativeBtn7"
            android:text="relativeBtn8"/>
        <Button
            android:id="@+id/relativeBtn9"
            android:layout_width="wrap_content"
            android:layout_above="@id/relativeBtn7"
            android:layout_toEndOf="@id/relativeBtn7"
            android:layout_height="wrap_content"
            android:text="relativeBtn9"/>
        <Button
            android:id="@+id/relativeBtn10"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/relativeBtn7"
            android:layout_toStartOf="@id/relativeBtn7"
            android:text="RelativeBtn10"/>
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="RelativeBtn11"
            android:id="@+id/relativeBtn11"
            android:layout_below="@id/relativeBtn7"
            android:layout_toEndOf="@id/relativeBtn7"/>
    </RelativeLayout>
</LinearLayout>
```

#### 3、FrameLayout

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".uidemo.layoutdemo.FrameLayoutActivity">
    <TextView
        android:id="@+id/textView_frameLayout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="this is textView,you can show somethings what you want"/>
    <Button
        android:id="@+id/btn_frameLayout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="btn"/>

</FrameLayout>
```

## 三、自定义控件

1. 定义一个布局文件

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:orientation="horizontal">
   
       <Button
           android:id="@+id/titleBackBtn"
           android:layout_width="60dp"
           android:layout_height="40dp"
           android:text="Back"
           android:layout_margin="1dp"
           android:textColor="@color/white"
           android:textAllCaps="false"
           android:background="@drawable/testdemo3"/>
   
       <TextView
           android:layout_width="0dp"
           android:layout_height="wrap_content"
           android:layout_weight="1"
           android:text="titleText"
           android:gravity="center"
           android:background="#fff000"
           android:textSize="32dp"
           android:id="@+id/titleText"/>
       <Button
           android:layout_width="60dp"
           android:layout_height="40dp"
           android:text="Edit"
           android:textAllCaps="false"
           android:id="@+id/titleEdit"
           android:textColor="@color/white"
           android:background="@drawable/testdemo4"/>
   
   </LinearLayout>
   ```

   > android:layout_margin这个属性，它可以指定控件在上下左右方向上的间距。当然也 可以使用android:layout_marginLeft或android:layout_marginTop等属性来单独指 定控件在某个方向上的间距。

2. 程序布局文件，引用定义的布局

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:orientation="vertical"
       tools:context=".uidemo.UICustomViewsActivity">
   
       <!--配置id方便，viewBinding 引用include 引用布局的 id-->
       <include
           android:id="@+id/titleBar"
           layout="@layout/title"/>
       <!--<com.example.kotlinlearn.uidemo.TitleLayout
           android:layout_width="match_parent"
           android:layout_height="wrap_content"/>-->
   
   </LinearLayout>
   ```

3. 自定义控件的属性

   ```java
   package com.example.kotlinlearn.uidemo
   
   import android.app.Activity
   import android.content.Context
   import android.util.AttributeSet
   import android.view.LayoutInflater
   import android.widget.LinearLayout
   import android.widget.Toast
   import com.example.kotlinlearn.R
   import com.example.kotlinlearn.databinding.TitleBinding
   import java.util.zip.Inflater
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/10 16:44
    * Desc:
    */
   class TitleLayout(context: Context,attrs:AttributeSet):LinearLayout(context,attrs) {
       lateinit var binding:TitleBinding
       init {
           binding = TitleBinding.inflate(LayoutInflater.from(context),this,true)
           binding.titleBackBtn.setOnClickListener {
               val activity = context as Activity //as 类型转换
               activity.finish()
           }
   
           binding.titleEdit.setOnClickListener {
               Toast.makeText(context,"you clicked Edit button",Toast.LENGTH_SHORT).show()
           }
   
       }
   }
   ```

## 四、ListView

1. 布局文件中引入ListView控件

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       tools:context=".uidemo.listviewdemo.ListViewActivity">
       <ListView
           android:id="@+id/listView"
           android:layout_width="match_parent"
           android:layout_height="match_parent"/>
   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

2. 自定义实体类

   ```java
   class Fruit(val name:String, val imageId: Int)
   ```

3. 自定义子项布局

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="60dp">
       <ImageView
           android:id="@+id/fruitImage"
           android:layout_width="40dp"
           android:layout_height="40dp"
           android:layout_gravity="center_vertical"
           android:layout_marginLeft="10dp"/>
   
       <TextView
           android:id="@+id/fruitName"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_gravity="center_vertical"
           android:layout_marginLeft="10dp"/>
   </LinearLayout>
   ```

4. 自定义适配器

   ```java
   package com.example.kotlinlearn.uidemo.listviewdemo
   
   import android.app.Activity
   import android.view.LayoutInflater
   import android.view.View
   import android.view.ViewGroup
   import android.widget.ArrayAdapter
   import android.widget.ImageView
   import android.widget.TextView
   import com.example.kotlinlearn.R
   import com.example.kotlinlearn.databinding.FruitItemBinding
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/11 9:38
    * Desc:
    */
   class FruitAdapter(activity: Activity, private val resourceId:Int, data:List<Fruit>):ArrayAdapter<Fruit>(activity,resourceId,data) {
   
       inner  class ViewHolder(val fruitImage:ImageView,val fruitName:TextView)
   
       override fun getView(position: Int, convertView: View?, parent: ViewGroup): View {
           val view:View
           val viewHolder:ViewHolder
   
           if (convertView == null) {
               view = LayoutInflater.from(context).inflate(resourceId,parent,false)
               val fruitImage:ImageView = view.findViewById(R.id.fruitImage)
               val fruitName:TextView = view.findViewById(R.id.fruitName)
               viewHolder = ViewHolder(fruitImage,fruitName)
               //后调用View的setTag()方法，将ViewHolder对象存储在View中
               view.tag = viewHolder
           } else {
               view = convertView
               //则调用View的getTag()方法，把ViewHolder重新取出
               viewHolder = view.tag as ViewHolder
           }
           val fruit = getItem(position) //获取当前fruit实例
           if (fruit !=null) {
               viewHolder.fruitImage.setImageResource(fruit.imageId)
               viewHolder.fruitName.text = fruit.name
           }
           return view
       }
   }
   ```

   FruitAdapter定义了一个主构造函数，用于将Activity的实例、ListView子项布局的id和数 据源传递进来。

5. 活动中引用适配器

   ```java
   package com.example.kotlinlearn.uidemo.listviewdemo
   
   import android.content.Context
   import android.content.Intent
   import androidx.appcompat.app.AppCompatActivity
   import android.os.Bundle
   import android.widget.Toast
   import com.example.kotlinlearn.R
   import com.example.kotlinlearn.databinding.ActivityCustomListViewBinding
   
   class CustomListViewActivity : AppCompatActivity() {
       lateinit var binding:ActivityCustomListViewBinding
       private val fruitList = ArrayList<Fruit>()
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           binding = ActivityCustomListViewBinding.inflate(layoutInflater)
           setContentView(binding.root)
           initFruits()
           val adapter = FruitAdapter(this,R.layout.fruit_item,fruitList)
           binding.customListView.adapter = adapter
           binding.customListView.setOnItemClickListener { parent, view, position, id ->
               val fruit = fruitList[position]
               Toast.makeText(this,fruit.name,Toast.LENGTH_SHORT).show()
           }
       }
       companion object {
           fun actionStart(context: Context){
               val intent = Intent(context,CustomListViewActivity::class.java)
               context.startActivity(intent)
           }
       }
   
       private fun initFruits(){
           /**
            * repeat函数，执行n次代码块内容*/
           repeat(2){
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
               fruitList.add(Fruit("Apple", R.drawable.testdemo4))
           }
       }
   }
   ```

## 五、RecycleView

1. build.gradle中引入插件

   > https://developer.android.google.cn/reference/android/support/v7/widget/RecyclerView

   ```groovy
   implementation 'androidx.recyclerview:recyclerview:1.2.1'
   ```

2. 布局文件中引入ListView控件

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       tools:context=".uidemo.recycleviewdemo.RecycleViewActivity">
   
       <androidx.recyclerview.widget.RecyclerView
           android:id="@+id/recycleView"
           android:layout_width="match_parent"
           android:layout_height="match_parent"/>
   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

3. 自定义实体类

   ```java
   class Fruit(val name:String, val imageId: Int)
   ```

4. 自定义子项布局

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="60dp">
       <ImageView
           android:id="@+id/fruitImage"
           android:layout_width="40dp"
           android:layout_height="40dp"
           android:layout_gravity="center_vertical"
           android:layout_marginLeft="10dp"/>
   
       <TextView
           android:id="@+id/fruitName"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_gravity="center_vertical"
           android:layout_marginLeft="10dp"/>
   </LinearLayout>
   ```

5. 自定义适配器

   ```java
   package com.example.kotlinlearn.uidemo.recycleviewdemo;
   
   import android.view.LayoutInflater
   import android.view.View
   import android.view.ViewGroup
   import android.widget.ImageView
   import android.widget.TextView
   import androidx.recyclerview.widget.RecyclerView
   import com.example.kotlinlearn.R
   import com.example.kotlinlearn.uidemo.listviewdemo.Fruit;
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/11 13:39
    * Desc:
    */
   class FruitRecycleViewAdapter (private val fruitList: List<Fruit>):RecyclerView.Adapter<FruitRecycleViewAdapter.ViewHolder>(){
   
       inner class ViewHolder(view:View):RecyclerView.ViewHolder(view){
           val fruitImage:ImageView = view.findViewById(R.id.fruitImage)
           val fruitName:TextView = view.findViewById(R.id.fruitName)
       }
   
       /**
        * 用于创建ViewHolder实例的*/
       override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
           //val view = LayoutInflater.from(parent.context).inflate(R.layout.fruit_item,parent,false)
           //val view = LayoutInflater.from(parent.context).inflate(R.layout.fruit_item_vertical,parent,false)
           val view = LayoutInflater.from(parent.context).inflate(R.layout.fruit_item_staggeredgrid,parent,false)
           return ViewHolder(view)
       }
   
       /**
        * 用于对RecyclerView子项的数据进行赋值*/
       override fun onBindViewHolder(holder: ViewHolder, position: Int) {
           val fruit = fruitList[position]
           holder.fruitImage.setImageResource(fruit.imageId)
           holder.fruitName.text = fruit.name
       }
   
       /**
        * 记录RecyclerView一共有多少子项，直接返回数据源的长度*/
       override fun getItemCount()=fruitList.size
   }
   
   ```

## 六、实践

