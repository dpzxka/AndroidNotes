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