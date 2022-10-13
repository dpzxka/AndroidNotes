# Android：Fragment

## 1、定义

```
Activity`界面中的一部分，可理解为模块化的`Activity
```

> 1. `Fragment`不能独立存在，必须嵌入到`Activity`中
>
> 2. `Fragment`具有自己的生命周期，接收它自己的事件，并可以在`Activity`运行时被添加或删除
>
> 3. `Fragment`的生命周期直接受所在的`Activity`的影响。如：当`Activity`暂停时，它拥有的所有`Fragment`们都暂停

## 2、作用

支持动态、灵活的界面设计

> 1. `Fragment`从 `Android 3.0`后引入
> 2. 在低版本`Android 3.0`前使用 `Fragment`，需要采用`android-support-v4.jar`兼容包

## 3、生命周期

#### 1、Fragment状态与回调

一共有四种状态：运行状态、暂停状态、停止状态和销毁状态

1. 运行状态：当一个Fragment所关联的Activity正处于运行状态时，该Fragment也处于运行状态
2. 暂停状态：当一个Activity进入暂停状态时(由于另一个未沾满屏幕的Activity被添加到栈顶)，与它相关联的Fragment就会进入暂停状态。(如对话框弹出式，上已成的界面会进入到暂停状态)
3. 停止状态：当一个Activity进入停止状态时，与它相关联的Fragment就会进入停止状态，或者通过调用FragmentTransaction的remove()、replace()方法将Fragment从Activity中移除，当在事务提交之前调用了addToBackStack方法，此时Fragment也会进入到停止状态。进入停止状态的Fragment对用户来说是完全不可见的，有可能会被系统回收
4. 销毁状态：Fragment依附于Activity而存在，当Activity被销毁时，与它相关联的Fragment就会进入销毁状态。或者通过调用FragmentTransaction的remove()、replace()方法将Fragment从Activity中移除，但在事务提交之前并没有调用addToBackStack方法，这时的Fragment就会进入销毁状态。

#### 2、回调方法

- onAttach()：当Fragment和Activity建立关联时调用。 
- onCreateView()：为Fragment创建视图（加载布局）时调用。 
- onActivityCreated()：确保与Fragment相关联的Activity已经创建完毕时调用。 
- onDestroyView()：当与Fragment关联的视图被移除时调用。 
- onDetach()：当Fragment和Activity解除关联时调用。

![img](https://upload-images.jianshu.io/upload_images/944365-cc85e7626552d866.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

![image-20221012145051797](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221012145051797.png)

## 4、具体使用

- 由于`Fragment`作为`Activity`一部分，所以`Fragment`的使用一般是添加到`Activity`中

- 将`Fragment`添加到`Activity`中一般有2种方法:
  1. 在`Activity`的`layout.xml`布局文件中静态添加
  2. 在`Activity`的`.java`文件中动态添加

**方法1：在`Activity`的`layout.xml`布局文件中静态添加**

activity的布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".fragmentdemo.simplefragment.SimpleFragmentActivity">

    <fragment
        android:id="@+id/leftFrag"
        android:name="com.example.kotlinlearn.fragmentdemo.simplefragment.LeftFragment"
        android:layout_weight="1"
        android:layout_width="0dp"
        android:layout_height="match_parent"/>
    <!--静态加载-->
    <!--<fragment
        android:id="@+id/rightFrag"
        android:name="com.example.kotlinlearn.fragmentdemo.simplefragment.RightFragment"
        android:layout_weight="1"
        android:layout_width="0dp"
        android:layout_height="match_parent"/>-->
    <!--动态加载-->
    <FrameLayout
        android:id="@+id/rightLayout"
        android:layout_weight="1"
        android:layout_width="0dp"
        android:layout_height="match_parent"/>
</LinearLayout>
```

Fragment布局文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/left_fragment_btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:text="leftFragmentBtn"/>
</LinearLayout>
```

Fragment的java文件

```kotlin
package com.example.kotlinlearn.fragmentdemo.simplefragment

import android.app.Activity
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment
import com.example.kotlinlearn.MainActivity
import com.example.kotlinlearn.R

/**
 * Author: Zhangtao
 * Created on 2022/10/12 9:30
 * Desc:
 */
class LeftFragment:Fragment() {
//    lateinit var activity:Activity
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        /*Fragment中调用activity*/
        if (activity !=null ){
            val mainActivity = activity as MainActivity
        }
        return inflater.inflate(R.layout.left_fragment,container,false)
    }
}
```

**方法2：在Activity的.java文件中动态添加**

- 步骤1：在`Activity`的布局文件定义1占位符（`FrameLayout`）
  这样做的好处是：可动态在`Activity`中添加不同的 `Fragment`，更加灵活

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      xmlns:tools="http://schemas.android.com/tools"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      tools:context=".fragmentdemo.simplefragment.SimpleFragmentActivity">
      <FrameLayout
          android:id="@+id/rightLayout"
          android:layout_width="wrap_parent"
          android:layout_height="match_parent"/>
  </LinearLayout>
  ```

  

- 步骤2：设置`Fragment`的布局文件

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      android:orientation="vertical"
      android:layout_width="match_parent"
      android:layout_height="match_parent">
  
      <Button
          android:id="@+id/left_fragment_btn"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:layout_gravity="center_horizontal"
          android:text="leftFragmentBtn"/>
  </LinearLayout>
  ```

  

- 步骤3：在`Activity`的`.java`文件中动态添加`Fragment`

  ```kotlin
  package com.example.kotlinlearn.fragmentdemo.simplefragment
  
  import android.content.Context
  import android.content.Intent
  import androidx.appcompat.app.AppCompatActivity
  import android.os.Bundle
  import android.widget.Button
  import androidx.fragment.app.Fragment
  import com.example.kotlinlearn.R
  import com.example.kotlinlearn.databinding.ActivitySimpleFragmentBinding
  
  class SimpleFragmentActivity : AppCompatActivity() {
      lateinit var binding:ActivitySimpleFragmentBinding
  
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          binding = ActivitySimpleFragmentBinding.inflate(layoutInflater)
          setContentView(binding.root)
          val leftFragmentBtn = findViewById<Button>(R.id.left_fragment_btn)
          leftFragmentBtn.setOnClickListener {
              replaceFragment(AnotherRightFragment())
          }
          /**1、创建待添加的Fragment的实例*/
          replaceFragment(RightFragment())
          /**activity 中获取fragment实例*/
          val fragment = supportFragmentManager.findFragmentById(R.id.leftFrag) as LeftFragment
      }
  
      private fun replaceFragment(fragment: Fragment){
          /*2、获取FragmentManager*/
          val fragmentManger = supportFragmentManager
          /*3、开启事务*/
          val transaction = fragmentManger.beginTransaction()
          /*4、向容器内添加活替换Fragment，一般使用replace，传入容器的id和待添加的Fragment实例*/
          transaction.replace(R.id.rightLayout,fragment)
          //返回栈
          transaction.addToBackStack(null)
          /*5、提交事务*/
          transaction.commit()
      }
  
      companion object{
          fun actionStart(context: Context){
              val intent = Intent(context,SimpleFragmentActivity::class.java)
              context.startActivity(intent)
          }
      }
  }
  ```

  **动态加载布局技巧**（限定符）

  在res目录下，建立layout-限定符名称，放入同名的xml布局文件即可。

   ![image-20221012151941678](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221012151941678.png)

例如：在layout目录下存在布局文件：activity_fragment_qualifier

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".fragmentdemo.qualifierdemo.FragmentQualifierActivity">

    <fragment
        android:id="@+id/leftFragQualifier"
        android:name="com.example.kotlinlearn.fragmentdemo.simplefragment.LeftFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</LinearLayout>
```

​				建立layout-large目录：activity_fragment_qualifier

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".fragmentdemo.qualifierdemo.FragmentQualifierActivity">

    <fragment
        android:id="@+id/leftFragQualifier"
        android:name="com.example.kotlinlearn.fragmentdemo.simplefragment.LeftFragment"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="match_parent"/>
    <fragment
        android:name="com.example.kotlinlearn.fragmentdemo.simplefragment.RightFragment"
        android:layout_width="0dp"
        android:layout_weight="3"
        android:layout_height="match_parent"/>
</LinearLayout>
```

最小限定符：最小宽度限定符允许我们对屏幕的宽度指定一个最小值（以dp为单位），然后以这个最小值为 临界点，屏幕宽度大于这个值的设备就加载一个布局，屏幕宽度小于这个值的设备就加载另一 个布局。

在res目录下新建layout-sw600dp文件夹：当程序运行在屏幕宽度大于等于600 dp的设备上时，会加载layoutsw600dp/activity_main布局，当程序运行在屏幕宽度小于600 dp的设备上时，则仍然加载 默认的layout/activity_main布局

## 5、Fragment与Activity的交互

**在Activiy中获取Fragment**

调用FragmentManager的findFragmentById()方法，可以在Activity中得到相应 Fragment的实例

```kotlin
 /**activity 中获取fragment实例*/
        val fragment = supportFragmentManager.findFragmentById(R.id.leftFrag) as LeftFragment
```

**在Fragment中获取Activity**

```kotlin
/*Fragment中调用activity*/
if (activity !=null ){
    val mainActivity = activity as MainActivity
}
```

## 6、实践

1. 导入依赖库：

   ```groovy
   /*RecycleView依赖库*/
   implementation 'androidx.recyclerview:recyclerview:1.2.1'
   ```

2. 创建新闻实体类：News

   ```kotlin
   class News(val title: String, val content: String)
   ```

3. 创建新闻内容碎片布局：news_content_frag.xml

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <LinearLayout
           android:id="@+id/contentLayout"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           android:orientation="vertical"
           android:visibility="invisible">
   
           <TextView
               android:id="@+id/newsTitle"
               android:layout_width="match_parent"
               android:layout_height="wrap_content"
               android:gravity="center"
               android:padding="10dp"
               android:textSize="20sp"/>
           <View
               android:layout_width="match_parent"
               android:layout_height="1dp"
               android:background="#000"/>
           <TextView
               android:id="@+id/newsContent"
               android:layout_width="match_parent"
               android:layout_height="0dp"
               android:layout_weight="1"
               android:padding="15dp"
               android:textSize="18sp"/>
       </LinearLayout>
       <View
           android:layout_width="1dp"
           android:layout_height="match_parent"
           android:background="#000"
           android:layout_alignParentStart="true"/>
   
   </RelativeLayout>
   ```

4.  创建新闻内容碎片：NewsContentFragment

   ```kotlin
   package com.example.kotlinlearn.fragmentdemo.fragmentbestpractice;
   
   import android.os.Bundle
   import android.view.LayoutInflater
   import android.view.View
   import android.view.ViewGroup
   import androidx.fragment.app.Fragment
   import com.example.kotlinlearn.databinding.NewsContentFragBinding
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/12 16:30
    * Desc:
    */
   class NewsContentFragment: Fragment() {
       private var _binding:NewsContentFragBinding ?= null
       private val binding get() = _binding!!
   
       override fun onCreateView(
           inflater: LayoutInflater,
           container: ViewGroup?,
           savedInstanceState: Bundle?
       ): View {
           _binding = NewsContentFragBinding.inflate(inflater,container,false)
           return binding.root
       }
   
       fun refresh(title:String,content:String){
           binding.contentLayout.visibility = View.VISIBLE
           binding.newsTitle.text = title //刷新新闻标题
           binding.newsContent.text = content //刷新新闻内容
       }
       override fun onDestroyView() {
           super.onDestroyView()
           _binding = null
       }
   }
   ```

5.  创建新闻内容，单页模式下，activity：NewsContentActivity

   ```kotlin
   package com.example.kotlinlearn.fragmentdemo.fragmentbestpractice
   
   import android.content.Context
   import android.content.Intent
   import androidx.appcompat.app.AppCompatActivity
   import android.os.Bundle
   import com.example.kotlinlearn.R
   import com.example.kotlinlearn.databinding.ActivityNewsContentBinding
   import com.example.kotlinlearn.fragmentdemo.simplefragment.LeftFragment
   
   /**
    * 单页模式下使用的新闻内容界面*/
   class NewsContentActivity : AppCompatActivity() {
       lateinit var binding:ActivityNewsContentBinding
   
       companion object{
           fun actionStart(context: Context,title:String,content:String){
               val intent = Intent(context,NewsContentActivity::class.java).apply {
                   putExtra("news_title",title)
                   putExtra("news_content",content)
               }
               context.startActivity(intent)
           }
       }
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           binding = ActivityNewsContentBinding.inflate(layoutInflater)
           setContentView(binding.root)
           val title = intent.getStringExtra("news_title")//获取传入的新闻标题
           val content = intent.getStringExtra("news_content")//获取传入的新闻内容
           if (title !=null && content!=null) {
               val fragment = supportFragmentManager.findFragmentById(R.id.newsContentFrag) as NewsContentFragment
               fragment.refresh(title,content) //刷新NewsContentFragment界面
           }
       }
   }
   ```

6.  创建单页模式下，activity的布局文件：activity_news_content.xml

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       tools:context=".fragmentdemo.fragmentbestpractice.NewsContentActivity">
   
       <fragment
           android:name="com.example.kotlinlearn.fragmentdemo.fragmentbestpractice.NewsContentFragment"
           android:id="@+id/newsContentFrag"
           android:layout_width="match_parent"
           android:layout_height="match_parent"/>
   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

7. 创建新闻标题碎片布局：news_title_frag.xml

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
   
   
       <androidx.recyclerview.widget.RecyclerView
           android:id="@+id/newsTitleRecycleView"
           android:layout_width="match_parent"
           android:layout_height="match_parent"/>
   </LinearLayout>
   ```

8.  创建RecycleView的子项布局：news_item.xml

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <TextView xmlns:android="http://schemas.android.com/apk/res/android"
       android:id="@+id/newsTitle"
       android:layout_width="match_parent"
       android:layout_height="wrap_content"
       android:maxLines="1"
       android:ellipsize="end"
       android:textSize="18sp"
       android:paddingLeft="10dp"
       android:paddingRight="10dp"
       android:paddingTop="15dp"
       android:paddingBottom="15dp"> 
   
   </TextView>
   ```

9.  创建新闻标题碎片：NewsTitleFragment

   ```kotlin
   package com.example.kotlinlearn.fragmentdemo.fragmentbestpractice
   
   import android.os.Bundle
   import android.view.LayoutInflater
   import android.view.View
   import android.view.ViewGroup
   import android.widget.TextView
   import androidx.fragment.app.Fragment
   import androidx.recyclerview.widget.LinearLayoutManager
   import androidx.recyclerview.widget.RecyclerView
   import com.example.kotlinlearn.R
   import com.example.kotlinlearn.databinding.NewsTitleFragBinding
   import com.example.langue.lateinit.getResultMsg
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/12 16:57
    * Desc:
    */
   class NewsTitleFragment: Fragment() {
       private var isTwoPane = false
       private var _binding: NewsTitleFragBinding? = null
       private val binding get() = _binding!!
   
       override fun onCreateView(
           inflater: LayoutInflater,
           container: ViewGroup?,
           savedInstanceState: Bundle?
       ): View? {
           _binding = NewsTitleFragBinding.inflate(inflater,container,false)
           return binding.root
       }
   
       override fun onActivityCreated(savedInstanceState: Bundle?) {
           super.onActivityCreated(savedInstanceState)
           isTwoPane = activity?.findViewById<View>(R.id.newsContentLayout) != null
   
           //添加适配器
           val layoutManager = LinearLayoutManager(activity)
           binding.newsTitleRecycleView.layoutManager = layoutManager
           val adapter = NewsAdapter(getNews())
           binding.newsTitleRecycleView.adapter = adapter
       }
   
       private fun getNews():List<News>{
           val newsList = ArrayList<News>()
           for (i in 1..50) {
               val news = News("This is news title $i", getRandomLengthString("This is newsContent $i"))
               newsList.add(news)
           }
           return newsList
       }
   
       private fun getRandomLengthString(str:String):String{
           val n = (1..20).random()
           val builder = StringBuilder()
           repeat(n){
               builder.append(str)
           }
           return builder.toString()
       }
   
       override fun onDestroyView() {
           super.onDestroyView()
           _binding = null
       }
   
       inner class NewsAdapter(val newsList:List<News>):RecyclerView.Adapter<NewsAdapter.ViewHolder>(){
           inner class ViewHolder(view:View):RecyclerView.ViewHolder(view){
               val newsTitle: TextView = view.findViewById(R.id.newsTitle)
           }
   
           override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
               val view = LayoutInflater.from(parent.context).inflate(R.layout.news_item,parent,false)
               val holder = ViewHolder(view)
               holder.itemView.setOnClickListener{
                   val news = newsList[holder.adapterPosition]
                   if (isTwoPane){
                       //如果是双页模式，则刷新newsContentFragment中的内容
                       val fragment = activity?.supportFragmentManager?.findFragmentById(R.id.newsContentFrag) as NewsContentFragment
                       fragment.refresh(news.title,news.content)
                   } else {
                       //如果是单页模式，则直接启动NewsContentActivity
                       NewsContentActivity.actionStart(parent.context,news.title,news.content)
                   }
               }
               return holder
           }
   
           override fun onBindViewHolder(holder: ViewHolder, position: Int) {
               val news = newsList[position]
               holder.newsTitle.text = news.title
           }
   
           override fun getItemCount()=newsList.size
       }
   }
   ```

10. 修改主页面，单页模式下加载的布局文件：activity_fragment_best_practice

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".fragmentdemo.fragmentbestpractice.FragmentBestPracticeActivity">
    
        <fragment
            android:id="@+id/newsTitleFrag"
            android:name="com.example.kotlinlearn.fragmentdemo.fragmentbestpractice.NewsTitleFragment"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    </androidx.constraintlayout.widget.ConstraintLayout>
    ```

11.  通过限定符，控制主页面加载双叶模式下的布局：layout-sw600dp/activity_fragment_best_practice

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".fragmentdemo.fragmentbestpractice.FragmentBestPracticeActivity">
    
        <fragment
            android:id="@+id/newsTitleFrag"
            android:name="com.example.kotlinlearn.fragmentdemo.fragmentbestpractice.NewsTitleFragment"
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="match_parent"/>
        <FrameLayout
            android:id="@+id/newsContentLayout"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="3">
            <fragment
                android:id="@+id/newsContentFrag"
                android:name="com.example.kotlinlearn.fragmentdemo.fragmentbestpractice.NewsContentFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent"/>
        </FrameLayout>
    </LinearLayout>
    ```

12.  主界面Activity：FragmentBestPracticeActivity

    ```kotlin
    package com.example.kotlinlearn.fragmentdemo.fragmentbestpractice
    
    import android.content.Context
    import android.content.Intent
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.example.kotlinlearn.databinding.ActivityFragmentBestPracticeBinding
    
    class FragmentBestPracticeActivity : AppCompatActivity() {
        lateinit var binding:ActivityFragmentBestPracticeBinding
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            binding = ActivityFragmentBestPracticeBinding.inflate(layoutInflater)
            setContentView(binding.root)
        }
        companion object{
            fun actionStart(context: Context){
                val intent = Intent(context,FragmentBestPracticeActivity::class.java)
                context.startActivity(intent)
            }
        }
    }
    ```

