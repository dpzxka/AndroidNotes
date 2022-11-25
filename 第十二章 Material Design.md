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

> 需要设置Material的主题
>
> 需要加上一个属性：android:theme="@style/Theme.MaterialComponents"或"@style/Theme.MaterialComponents.Light"

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

新增Glide插件：

```groovy
/*glide*/
implementation 'com.github.bumptech.glide:glide:4.14.2'
annotationProcessor 'com.github.bumptech.glide:compiler:4.14.2'
```

布局中添加RecycleView控件

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
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycleViewKa"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
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

创建对应实体类

```kotlin
class FruitKa(val name:String,val imageId:Int)
```

创建子项布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView xmlns:android="http://schemas.android.com/apk/res/android"
    android:theme="@style/Theme.MaterialComponents.Light"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="5dp"
    app:cardCornerRadius="4dp">

    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <!-scaleType 指定图片的缩放模式，centerCrop让图片保持原有比例填充满ImageView，并将超出屏幕部分裁剪掉-->
        <ImageView
            android:scaleType="centerCrop"
            android:id="@+id/image_ka"
            android:layout_width="match_parent"
            android:layout_height="100dp"/>
        <TextView
            android:textSize="16sp"
            android:layout_margin="5dp"
            android:layout_gravity="center_horizontal"
            android:id="@+id/name_ka"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
    </LinearLayout>

</com.google.android.material.card.MaterialCardView>
```

创建对应适配器：

```kotlin
class FruitAdapterKa(val context:Context,val fruitList:List<FruitKa>):RecyclerView.Adapter<FruitAdapterKa.ViewHolder>(){
    inner class ViewHolder(view: View):RecyclerView.ViewHolder(view){
        val fruitImage: ImageView = view.findViewById(R.id.image_ka)
        val fruitName:TextView = view.findViewById(R.id.name_ka)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(context).inflate(R.layout.fruit_item_ka,parent,false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val fruit = fruitList[position]
        holder.fruitName.text = fruit.name
        Glide.with(context).load(fruit.imageId).into(holder.fruitImage)
    }

    override fun getItemCount() = fruitList.size
}
```

> Glide:
>
> with：传入Context、Activity或Fragment参数
>
> load：加载图片，URL地址、本地路径、资源ID
>
> into：将图片设置到某个具体的ImageView中

```kotlin
val fruits = mutableListOf(FruitKa("Panan",R.drawable.testdemo),
    FruitKa("Panan",R.drawable.testdemo),
    FruitKa("Panan",R.drawable.testdemo),
    FruitKa("Panan",R.drawable.testdemo),
    FruitKa("Panan",R.drawable.testdemo),
    FruitKa("Panan",R.drawable.testdemo),
    FruitKa("Panan",R.drawable.testdemo),
    FruitKa("Panan",R.drawable.testdemo),
    FruitKa("Panan",R.drawable.testdemo))

val fruitList = ArrayList<FruitKa>()

private fun initFruits(){
    fruitList.clear()
    repeat(10){
        val index = (0 until  fruits.size).random()
        fruitList.add(fruits[index])
    }
}

override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityDrawerLayoutBinding.inflate(layoutInflater)
        setContentView(binding.root)
        initFruits()
        val layoutManager = GridLayoutManager(this,2)//每行展示几列数据
        binding.recycleViewKa.layoutManager = layoutManager
        val adapterKa = FruitAdapterKa(this,fruitList)
        binding.recycleViewKa.adapter = adapterKa
    }
```

### 2、AppBarLayout

AppBarLayout 是一个垂直方向的LinearLayout，内部做了滚动事件的封装。

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <androidx.appcompat.widget.Toolbar
            app:popupTheme="@style/Theme.AppCompat.Light"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            android:background="@color/design_default_color_primary"
            android:id="@+id/toolBar2"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_constraintTop_toTopOf="parent" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        android:id="@+id/recycleViewKa"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
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

将Toolbar放置在AppBarLayout中，在RecyclerView中使用app:layout_behavior属性指定一个布局行为。

AppBarLayout接收到滚动事件的时候，它内部的子控件可以指定如何去响应这些事件，通过设置`app:layout_scrollFlags`

> scroll表示当RecyclerView向上滚动的时候， Toolbar会跟着一起向上滚动并实现隐藏；
>
> enterAlways表示当RecyclerView向下滚动的时候，Toolbar会跟着一起向下滚动并重新显示；
>
> snap表示当Toolbar还没有完全隐藏或显示的时候，会根据当前滚动的距离，自动选择是隐藏还是显示

```kotlin
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <androidx.appcompat.widget.Toolbar
            app:layout_scrollFlags="scroll|enterAlways|snap"
            app:popupTheme="@style/Theme.AppCompat.Light"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            android:background="@color/design_default_color_primary"
            android:id="@+id/toolBar2"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_constraintTop_toTopOf="parent" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        android:id="@+id/recycleViewKa"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
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

## 5、下拉刷新

SwipeRefreshLayout就是用于实现下拉刷新功能的核心类，我们把想要实现下拉刷新功能的 控件放置到SwipeRefreshLayout中，就可以迅速让这个控件支持下拉刷新。

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    ...
    <androidx.swiperefreshlayout.widget.SwipeRefreshLayout
        android:id="@+id/swipeRefresh"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
        
        <androidx.recyclerview.widget.RecyclerView
            app:layout_behavior="@string/appbar_scrolling_view_behavior"
            android:id="@+id/recycleViewKa"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    </androidx.swiperefreshlayout.widget.SwipeRefreshLayout>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

setColorSchemeResources设置刷新进度条颜色

setOnRefreshListener设置刷新监听器，执行刷新逻辑。

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityDrawerLayoutBinding.inflate(layoutInflater)
        setContentView(binding.root)
		//下拉刷新
		binding.swipeRefresh.setColorSchemeResources(com.google.android.material.R.color.design_default_color_primary)
		binding.swipeRefresh.setOnRefreshListener {
	    refreshFruits(adapterKa)
	}

private fun refreshFruits(adapterKa: FruitAdapterKa){
        thread {
            Thread.sleep(2000)
            runOnUiThread {
                initFruits()
                adapterKa.notifyDataSetChanged()
                binding.swipeRefresh.isRefreshing = false//表示刷新事件结束，并隐藏刷新进度
            }
        }
    }
```

## 6、可折叠式标题栏

### 1、CollapsingToolbarLayout

CollapsingToolbarLayout是作用于Toolbar基础之上的布局，不能独立存在，被限定只能作为AppBarLayout的直接子布局来使用。AppBarLayout必须是CoordinatorLayout的子布局。CollapsingToolbarLayout在折叠之后就是一个普通的Toolbar

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".materialdemo.FruitActivity"
    android:fitsSystemWindows="true">

    <!--标题部分-->
    <com.google.android.material.appbar.AppBarLayout
        android:fitsSystemWindows="true"
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="250dp">
        <!--app:contentScrim
                属性用于指定CollapsingToolbarLayout在趋于折叠状态以及折叠之后的背景色
            app:layout_scrollFlags
                scroll表示CollapsingToolbarLayout会随着水果内容详情的滚动一起滚动，
                exitUntilCollapsed表示当CollapsingToolbarLayout随着滚动完成折叠之后就保留在界面上，不再移出屏幕 -->
        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:fitsSystemWindows="true"
            android:id="@+id/collapsingToolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:contentScrim="@color/design_default_color_primary"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">

            <!--app:layout_collapseMode:指定当前控件在CollapsingToolbarLayout折叠过程中的折叠模式
                    pin，表示在折叠的过程中位置始终保持不变
                    parallax，表示会在折叠的过程中产生一定的错位偏移
                -->
            <ImageView
                android:fitsSystemWindows="true"
                android:id="@+id/fruitImageView"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="centerCrop"
                app:layout_collapseMode="parallax"/>
            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolBar3"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin"/>
        </com.google.android.material.appbar.CollapsingToolbarLayout>
    </com.google.android.material.appbar.AppBarLayout>
    <!--内容部分-->
    <!--NestedScrollView:内部都只允许存在一个直接子布局
        NestedScrollView在ScrollView基础之上还增加了嵌套响应滚动事件的功能
        -->
    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

        <LinearLayout
            android:orientation="vertical"
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
            <com.google.android.material.card.MaterialCardView
                android:theme="@style/Theme.MaterialComponents.Light"
                android:layout_marginBottom="15dp"
                android:layout_marginStart="15dp"
                android:layout_marginEnd="15dp"
                android:layout_marginTop="35dp"
                app:cardCornerRadius="4dp"
                android:layout_width="match_parent"
                android:layout_height="wrap_content">
                <TextView
                    android:layout_margin="10dp"
                    android:id="@+id/fruitContentText"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"/>
            </com.google.android.material.card.MaterialCardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>
    <!--app:layout_anchor属性指定一个锚点
        悬浮按钮会显示在指定的瞄点位置
    app:layout_anchorGravity属性
        显示位置-->
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:src="@drawable/ic_baseline_email_24"
        app:layout_anchor="@id/appBar"
        app:layout_anchorGravity="bottom|end"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

功能界面：

```kotlin
class FruitActivity : AppCompatActivity() {
    companion object{
        const val FRUIT_NAME = "fruit_name"
        const val FRUIT_IMAGE_ID = "fruit_image_id"
    }
    lateinit var binding:ActivityFruitBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityFruitBinding.inflate(layoutInflater)
        setContentView(binding.root)
        val fruitName = intent.getStringExtra(FRUIT_NAME)?:""
        val fruitIamgeId = intent.getIntExtra(FRUIT_IMAGE_ID,0)
        setSupportActionBar(binding.toolBar3)
        supportActionBar?.setDisplayHomeAsUpEnabled(true)
        binding.collapsingToolbar.title = fruitName//设置当前界面的标题
        Glide.with(this).load(fruitIamgeId).into(binding.fruitImageView)
        binding.fruitContentText.text = generaterFruitContent(fruitName)
    }

    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        when(item.itemId){
            android.R.id.home ->{
                finish()
                return true
            }
        }
        return super.onOptionsItemSelected(item)
    }

    private fun generaterFruitContent(fruitName:String) = fruitName.repeat(500)
}
```

处理recycleView点击事件：

```kotlin
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
    val view = LayoutInflater.from(context).inflate(R.layout.fruit_item_ka,parent,false)
    val holder = ViewHolder(view)
    holder.itemView.setOnClickListener {
        val position = holder.adapterPosition
        val fruit = fruitList[position]
        val intent = Intent(context,FruitActivity::class.java).apply {
            putExtra(FruitActivity.FRUIT_NAME,fruit.name)
            putExtra(FruitActivity.FRUIT_IMAGE_ID,fruit.imageId)
        }
        context.startActivity(intent)
    }
    return holder
}
```

### 2、系统状态栏空间

背景图与系统状态栏融合：

设置控件属性：android:fitsSystemWindows="true"，表示该控件会出现在 系统状态栏里。子控件所有的父布局都要设置这个属性。

指定主题中状态栏颜色为透明色：

```xml
<style name="FruitActivityTheme" parent="Theme.MaterialComponents.DayNight.NoActionBar.Bridge">
    <item name="android:statusBarColor">@android:color/transparent</item>
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
</style>
```

对应的Activity应用这个主题：

```xml
<activity
    android:theme="@style/FruitActivityTheme"
    android:name=".materialdemo.FruitActivity"
    android:exported="false">
    <meta-data
        android:name="android.app.lib_name"
        android:value="" />
</activity>
```

