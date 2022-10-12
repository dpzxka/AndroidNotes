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

