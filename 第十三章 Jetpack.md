# 第十三章 Jetpack

> [Android Jetpack 开发资源 - Android 开发者  | Android Developers (google.cn)](https://developer.android.google.cn/jetpack)
>
> Jetpack是一个开发组件工具集，它的主要目的是编写出更加简洁的代码，并简化开发过程。

## 1、ViewModel

> 专门用于存放于外界相关的数据，界面上能看到的数据，相关变量都应该存放在ViewModel中给，减少Activity中逻辑

ViewModel的生命周期和Activity不同，可以保证选装屏幕时，不会重新创建，只有当Activity退出时才和Activity一块销毁。保证数据在旋转过程中不丢失。

![image-20221126165528423](C:/Users/xkadp/AppData/Roaming/Typora/typora-user-images/image-20221126165528423.png)

### 1、基本用法

创建对应ViewModel

```
/*规范：每一个Activity或Fragment都创建一个对应的ViewModel*/
class MainViewModel : ViewModel() {
    var counter = 0
}
```

在activity中调用对应的ViewModel

```kotlin
class ViewModelDemoActivity : AppCompatActivity() {
    lateinit var binding:ActivityViewModelDemoBinding

    lateinit var viewModel: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityViewModelDemoBinding.inflate(layoutInflater)
        setContentView(binding.root)
        viewModel = ViewModelProvider(this).get(MainViewModel::class.java)
        binding.plusOneBtn.setOnClickListener {
            viewModel.counter++
            refreshCounter()
        }
        refreshCounter()
    }

    private fun refreshCounter(){
        binding.infoText.text = viewModel.counter.toString()
    }
}
```

不可以直接去创建ViewModel的实例，而是一定要通过ViewModelProvider来获取ViewModel的实例,是因为ViewModel有其独立的生命周期，并且其生命周期要长于Activity。 如果我们在onCreate()方法中创建ViewModel的实例，那么每次onCreate()方法执行的时候，ViewModel都会创建一个新的实例，这样当手机屏幕发生旋转的时候，就无法保留其中的数据。具体格式：

通过ViewModelProvider来获取实例

```kotlin
ViewModelProvider(<你的Activity或Fragment实例>).get(<你的ViewModel>::class.java)
```

### 2、向ViewModel传递参数

借助ViewModelProvider.Factory来实现。保证在退出程序再重新打开的情况下，数据不丢失。

```kotlin
/*参数用于记录之前保存的值，在初始化的时候重新赋值*/
class MainViewModel(countReserved:Int) : ViewModel() {
    var counter = countReserved
}
```

新建MainViewModelFactory类，实现ViewModelProvider.Factory接口.

create()方法的执行时机和 Activity的生命周期无关

```kotlin
class MainViewModelFactory(private val countReserved:Int):ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        return MainViewModel(countReserved) as T
    }
}
```

保存数据与回复数据

```kotlin
class ViewModelDemoActivity : AppCompatActivity() {
    lateinit var binding:ActivityViewModelDemoBinding

    lateinit var viewModel: MainViewModel
    lateinit var sp:SharedPreferences

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityViewModelDemoBinding.inflate(layoutInflater)
        setContentView(binding.root)
        sp = getPreferences(Context.MODE_PRIVATE)
        val countReserved = sp.getInt("count_reserved",0)
        viewModel = ViewModelProvider(this,MainViewModelFactory(countReserved)).get(MainViewModel::class.java)
        binding.clearBtn.setOnClickListener {
            viewModel.counter = 0
            refreshCounter()
        }

        /*viewModel = ViewModelProvider(this).get(MainViewModel::class.java)*/
        binding.plusOneBtn.setOnClickListener {
            viewModel.counter++
            refreshCounter()
        }
        refreshCounter()
    }

    override fun onPause() {
        super.onPause()
        sp.edit {
            putInt("count_reserved",viewModel.counter)
        }
    }

    private fun refreshCounter(){
        binding.infoText.text = viewModel.counter.toString()
    }
}
```

## 二、Lifecycles

感知Activity生命周期，可以让任何一个类都可以感应到Activity的生命周期。



```kotlin
/** LifecycleObserver是一个空接口，只需要接口实现声明，不需要重写方法*/
class MyObserver:LifecycleObserver {

    companion object{
        private const val TAG = "MyObserver"
    }

    /*通过注解感应生命周期*/
    @OnLifecycleEvent(Lifecycle.Event.ON_START)
    fun activityStart(){
        Log.d(TAG, "activityStart: Executed")
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    fun activityStop(){
        Log.d(TAG, "activityStop: executed")
    }
}
```

OnLifecycleEvent注解，生命周期事件类型：

- ON_CREATE
- ON_START
- ON_RESUME
- ON_PAUSE
- ON_STOP
- ON_DESTROY
- ON_ANY:匹配任何生命周期回调

当Activity生命周期发生变化时，需要通知MyObserver,需要借助Lifecycleowner，语法：

```kotlin
//调用它的addObserver()方法来观察LifecycleOwner的生命周期

lifecycleOwner.lifecycle.addObserver(MyObserver())
```

> Activity是继承自AppCompatActivity的，或者你的Fragment是继承自 androidx.fragment.app.Fragment的，那么它们本身就是一个LifecycleOwner的实例

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    binding = ActivityLifecyclesDemoBinding.inflate(layoutInflater)
    setContentView(binding.root)
    lifecycle.addObserver(MyObserver())//在fragment通用
}
```

MyObserver主动获取Activity生命周期状态，在MyObserver构造函数中将Lifecy对象传递进来：

```kotlin
class MyObserver(val lifecyle:Lifecycle):LifecycleObserver {

    companion object{
        private const val TAG = "MyObserver"
    }
}
```

通过调用`lifecyle.currentState`主动获取生命周期状态，枚举状态：`INITIALIZED、DESTROYED、CREATED、STARTED、RESUMED`

![image-20221126233050589](C:/Users/xkadp/AppData/Roaming/Typora/typora-user-images/image-20221126233050589.png)

> 当获取的生命周期状态是CREATED的时候，说明onCreate()方法已经执行了，但 是onStart()方法还没有执行。当获取的生命周期状态是STARTED的时候，说明onStart() 方法已经执行了，但是onResume()方法还没有执行.