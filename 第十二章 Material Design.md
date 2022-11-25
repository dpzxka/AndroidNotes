# 第十二章 Material Design

> Material Design是由Google的设计工程师们基于传统优秀的设计原则，结合丰富的创意和科 学技术所开发的一套全新的界面设计语言，包含了视觉、运动、互动效果等特性。

## 1、ToolBar

不仅继承ActionBar的所有功能，灵活性更高，可以配合其他控件完成Materin Design效果

![image-20221124165910500](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221124165910500.png)

关闭默认ActionBar主题：

```xml
<!--label:指定在Toolbar中显示的文字内容-->
        <activity
            android:label="Fruits"
            android:name=".materialdemo.toolbardemo.ActionBarActivity"
            android:exported="false"
            android:theme="@style/Theme.Design.Light.NoActionBar">
            <meta-data
                android:name="android.app.lib_name"
                android:value="" />
        </activity>
```

在布局文件中，新增toolbar控件

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".materialdemo.toolbardemo.ActionBarActivity">

    <!--
    android:theme，Toolbar单独使用深色主题，ThemeOverlay.AppCompat.Dark.ActionBar
    app:popupTheme属性，单独将弹出的菜单项指定成了浅色主题-->
    <androidx.appcompat.widget.Toolbar
        app:popupTheme="@style/Theme.AppCompat.Light"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        android:background="@color/design_default_color_primary"
        android:id="@+id/toolBar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

设置toolbar

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    binding = ActivityActionBarBinding.inflate(layoutInflater)
    setContentView(binding.root)
    setSupportActionBar(binding.toolBar)
}
```

Toolbar 中的action按钮只会显示图标，菜单中的action按钮只会显示文字

## 2、滑动菜单

使用DrawerLayout作为根布局文件：在布局中允许放入两个直接子控件：第一个子控件是主屏幕显示内容，第二个子控件是滑动菜单中显示的内容【第二个子控件，android:layout_gravity = "start"属性必须指定】

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".materialdemo.DrawerLayoutActivity">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <androidx.appcompat.widget.Toolbar
            app:popupTheme="@style/Theme.AppCompat.Light"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            android:background="@color/design_default_color_primary"
            android:id="@+id/toolBar2"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_constraintTop_toTopOf="parent" />
    </FrameLayout>
    <TextView
        android:textSize="30sp"
        android:text="This is Menu"
        android:background="#FFF"
        android:layout_gravity = "start"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</androidx.drawerlayout.widget.DrawerLayout>
```

添加导航图标

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    binding = ActivityDrawerLayoutBinding.inflate(layoutInflater)
    setContentView(binding.root)
    setSupportActionBar(binding.toolBar2)
    supportActionBar?.let {
        it.setDisplayHomeAsUpEnabled(true)
        it.setHomeAsUpIndicator(R.drawable.ic_baseline_login_24)
    }
}
override fun onOptionsItemSelected(item: MenuItem): Boolean {
        when(item.itemId){
            android.R.id.home -> binding.drawerLayout.openDrawer(GravityCompat.START)
        }
        return true
    }
```

## 3、悬浮按钮和可交互提示

### 1、悬浮按钮：FloatingActionButton

控制悬浮高度：`app:elevation="200dp"`，值越大，投影范围越小，值越小，投影效果越浓

```xml
<FrameLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <androidx.appcompat.widget.Toolbar
        app:popupTheme="@style/Theme.AppCompat.Light"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        android:background="@color/design_default_color_primary"
        android:id="@+id/toolBar2"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        app:layout_constraintTop_toTopOf="parent" />
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fab"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp"
        android:src="@drawable/ic_baseline_email_24"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
</FrameLayout>
```

点击时间处理：

```kotlin
/*点击事件处理*/
binding.fab.setOnClickListener {
    Toast.makeText(this,"FAB clicked",Toast.LENGTH_SHORT).show()
}
```

### 2、交互提示：Snackbar

> Toast作用:告诉用户现在发生了什么事情，但用户只能被动接收这个事情，因为没有什么办法能让用户进行选择。
>
> Snackbar:允许在提示中加入一个可交互按钮，当用户点击按钮的时候，可以执行一些额外的逻辑操作

```kotlin
/*点击事件处理*/
binding.fab.setOnClickListener {
    /*通过make创建Snackbar对象，参1，只要是当前布局的任意一个View都可以，Snackbar会自动查找最外层部分，用与展示提示信息
    * 通过setAction设置一个动作，用于交互*/
    Snackbar.make(binding.toolBar2,"Data deleted",Snackbar.LENGTH_SHORT).setAction("Undo"){
        Toast.makeText(this,"Data restored",Toast.LENGTH_SHORT).show()
    }.show()
}
```

### 3、CoordinatorLayout

CoordinatorLayout加强版FrameLayout，普通情况下同FrameLayout，除此之外，还可以监听所有子控件的各种事件，自动做出最合理的响应。

如果子控件的View中，响应响应的事件，也可以被监听到。如Snackbar

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <androidx.appcompat.widget.Toolbar
        app:popupTheme="@style/Theme.AppCompat.Light"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        android:background="@color/design_default_color_primary"
        android:id="@+id/toolBar2"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        app:layout_constraintTop_toTopOf="parent" />
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        app:elevation="200dp"
        android:id="@+id/fab"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp"
        android:src="@drawable/ic_baseline_email_24"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

```kotlin
binding.fab.setOnClickListener {
    //传入的是CoordinatorLayout子控件toolBar，事件也可以被监听到，不会遮挡悬浮
    Snackbar.make(binding.toolBar2,"Data deleted",Snackbar.LENGTH_SHORT).setAction("Undo"){
        Toast.makeText(this,"Data restored",Toast.LENGTH_SHORT).show()
    }.show()
}
```

## 4、卡片式布局

### 1、MaterialCardView

MaterialCardView用于实现卡片式布局效果，也是一个FrameLayout，只是额外提供了圆角和阴影效果。

`基本用法`：

```xml
<com.google.android.material.card.MaterialCardView
 	android:layout_width="match_parent"
 	android:layout_height="wrap_content"
 	app:cardCornerRadius="4dp"
 	app:elevation="5dp">
 	
    <TextView
 		android:id="@+id/infoText"
 		android:layout_width="match_parent"
 		android:layout_height="wrap_content"/>
</com.google.android.material.card.MaterialCardView>
```

app:cardCornerRadius:卡片圆角弧度

app:elevation：卡片高度

