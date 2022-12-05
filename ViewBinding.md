# ViewBinding

## 1、概述

使用ViewBinding需要注意条件：

1. Android Studio3.6或更高版本

2. 需要在build.gradle配置：

   ```groovy
   android{
   	...
   	buildFeatures{
   		viewBinding true
   	}
   }
   ```

   

## 2、Activity中应用

一旦启动了ViewBinding功能之后，Android Studio会自动为我们所编写的每一个布局文件都生成一个对应的Binding类。Binding类的命名规则是将布局文件按驼峰方式重命名后，再加上Binding作为结尾。

如定义了一个activity_main.xml布局，那么与它对应的Binding类就是ActivityMainBinding。

如不需要布局文件自动生成对应的Binding类，需要在布局文件的根元素位置加入：

```xml
<LinearLayout
    xmlns:tools="http://schemas.android.com/tools"
    ...
    tools:viewBindingIgnore="true">
    ...
</LinearLayout>
```

使用viewbinding:

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        binding.textView.text = "Hello"
    }

}
```

## 3、Fragment中应用

```kotlin
class MainFragment : Fragment() {

    private var _binding: FragmentMainBinding? = null

    private val binding get() = _binding!!

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View {
        _binding = FragmentMainBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }

}
```

保证binding变量的有效生命周期是在onCreateView()函数和onDestroyView()函数之间

## 4、Adapter中应用

fruit_item.xml来作为RecyclerView子项的布局

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="5dp">

    <ImageView
        android:id="@+id/fruitImage"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="10dp" />

    <TextView
        android:id="@+id/fruitName"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="left"
        android:layout_marginTop="10dp" />

</LinearLayout>
```

通过RecycleView Adapter来加载和显示子项布局

```kotlin
class FruitAdapter(val fruitList: List<Fruit>) : RecyclerView.Adapter<FruitAdapter.ViewHolder>() {

    inner class ViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val fruitImage: ImageView = view.findViewById(R.id.fruitImage)
        val fruitName: TextView = view.findViewById(R.id.fruitName)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.fruit_item, parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val fruit = fruitList[position]
        holder.fruitImage.setImageResource(fruit.imageId)
        holder.fruitName.text = fruit.name
    }

    override fun getItemCount() = fruitList.size

}
```

通过ViewBinding简化

```kotlin
class FruitAdapter(val fruitList: List<Fruit>) : RecyclerView.Adapter<FruitAdapter.ViewHolder>() {

    inner class ViewHolder(binding: FruitItemBinding) : RecyclerView.ViewHolder(binding.root) {
        val fruitImage: ImageView = binding.fruitImage
        val fruitName: TextView = binding.fruitName
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val binding = FruitItemBinding.inflate(LayoutInflater.from(parent.context), parent, false)
        return ViewHolder(binding)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val fruit = fruitList[position]
        holder.fruitImage.setImageResource(fruit.imageId)
        holder.fruitName.text = fruit.name
    }

    override fun getItemCount() = fruitList.size

}
```

## 5、引入布局中应用

### 5.1 include

定义titlebar布局，作为被引入的布局

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
 
    <Button
        android:id="@+id/back"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentLeft="true"
        android:layout_centerVertical="true"
        android:text="Back" />
 
    <TextView
        android:id="@+id/title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="Title"
        android:textSize="20sp" />
 
    <Button
        android:id="@+id/done"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:layout_centerVertical="true"
        android:text="Done" />
 
</RelativeLayout>
```

在ctivity_main.xml中引入这个布局

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
 
    <include 
        layout="@layout/titlebar" />
    ...
</LinearLayout>
```

使用viewbinding特性，需要在include中添加ID

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <include 
        android:id="@+id/titleBar"
        layout="@layout/titlebar" />
    ...
</LinearLayout>
```

在Activity中应用：

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        binding.titleBar.title.text = "Title"
        binding.titleBar.back.setOnClickListener {
        }
        binding.titleBar.done.setOnClickListener {
        }
    }

}
```



### 5.2 merge

定义titlebar布局

```xml
<merge xmlns:android="http://schemas.android.com/apk/res/android">
 
    <Button
        android:id="@+id/back"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentLeft="true"
        android:layout_centerVertical="true"
        android:text="Back" />

    <TextView
        android:id="@+id/title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="Title"
        android:textSize="20sp" />

    <Button
        android:id="@+id/done"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:layout_centerVertical="true"
        android:text="Done" />
 
</merge>
```

在布局中引用：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <include
        layout="@layout/titlebar" />

</LinearLayout>
```

通过viewbinding调用，在activity中

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding
    private lateinit var titlebarBinding: TitlebarBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        titlebarBinding = TitlebarBinding.bind(binding.root)
        setContentView(binding.root)
        titlebarBinding.title.text = "Title"
        titlebarBinding.back.setOnClickListener {
        }
        titlebarBinding.done.setOnClickListener {
        }
    }

}
```

