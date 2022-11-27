# 第十三章 Jetpack

> [Android Jetpack 开发资源 - Android 开发者  | Android Developers (google.cn)](https://developer.android.google.cn/jetpack)
>
> Jetpack是一个开发组件工具集，它的主要目的是编写出更加简洁的代码，并简化开发过程。

## 一、ViewModel

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

## 三、LivwData

是Jetpack提供的一种响应式编程组件，它可以包含任何类型的数据，并在数据发生 变化的时候通知给观察者。

### 1、基本用法

将ViewModel计数器的计数使用LiveData来包装，然后在Activity中去观察它，就可以主动将数据变化通知给Activity。

> ViewModel的生命周期长于Activity，不能将Activity实例传给ViewModel，会造成Activity无法释放导致内存泄漏。 

```kotlin
/*参数用于记录之前保存的值，在初始化的时候重新赋值*/
class MainViewModel(countReserved:Int) : ViewModel() {
    
    var counter = MutableLiveData<Int>()

    init {
        counter.value  = countReserved
    }

    fun plusOne(){
        val count = counter.value?:0
        counter.value = count+1
    }

    fun clear(){
        counter.value = 0
    }
}
```

MutableLiveData 是一种可变的LiveData,3种读写数据的方式：泛型为Int，表示它包含的是整型数据

* getValue()方法用于获取LiveData中包含的数据；
* setValue()方法用于给LiveData设置数据，但是只能在主线程中调用；
* postValue()方法用于在非主线程中给LiveData设置数据

Activity调用：

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
            viewModel.clear()
        }
        binding.plusOneBtn.setOnClickListener {
            viewModel.plusOne()
        }
        viewModel.counter.observe(this, Observer {
            binding.infoText.text = it.toString()
        })
    }

    override fun onPause() {
        super.onPause()
        sp.edit {
            putInt("count_reserved",viewModel.counter.value?:0)
        }
    }
}
```

> 当一个Java方法同时接收两个单抽象方法接口参数时，要么同时使用函数式API的写法，要么都不使用函数式API的写法。由于我们第一个参数传的是this，因此第二个参 数就无法使用函数式API的写法了。

observe()方法来观察数据的变化,任何LiveData对象都可以调用它的observe()方法来观察数据的变化。

observe()方法接收两个参数：

- 参1：是一个LifecycleOwner对象(Activity本身 就是一个LifecycleOwner对象，因此直接传this就好)；
- 参2：数是一个Observer接口， 当counter中包含的数据发生变化时，就会回调到这里，因此我们在这里将最新的计数更新到界面上即可

> 如果在子线程中给LiveData设置数据，一定要调用postValue()方法， 而不能再使用setValue()方法，否则会发生崩溃.
>
> ```kotlin
> counter.postValue(countReserved)
> ```

永远只暴露不可变的LiveData给外部，让非ViewModel中只能观察LiveData的数据变化，而不能给LiveData设置数据。

```kotlin
class MainViewModel(countReserved:Int) : ViewModel() {

    //重新定义counter变量，声明为不可变的LvieData，并在get()属性方法返回_counter变量
    val counter:LiveData<Int> get() = _counter

    //定义成私有的，外部不可见
    private var _counter = MutableLiveData<Int>()

    init {
        _counter.value = countReserved
    }

    fun plusOne(){
        val count = _counter.value ?: 0
        _counter.value = count + 1
    }

    fun clear(){
        _counter.value = 0
    }
}
```

当外部调用counter变量时，实际上获得的就是_counter的实例，但是无法给 counter设置数据，从而保证了ViewModel的数据封装性

### 2、map和switchMap

#### map

将实际包含数据的LiveData和仅用于观察数据的LiveData进行转换

如：一个User类，包含用户姓名和年龄

```kotlin
data class User(var firstName: String, var lastName: String, var age: Int)
```

在ViewModel中创建一个响应的LiveData类包含User类型：

```kotlin
class MainViewModel(countReserved:Int):ViewModel(){
	val userLiveData = MutableLiveData<User>()
    ...
}
```

如果Activity中明确指挥显示用户的姓名，不在乎年龄，不需要将整个User类型LiveData暴露给外部。map方法可以将User类型的LiveData自由转换成任意其他类型的LiveData：

```kotlin
class MainViewModel(countReserved:Int):ViewModel(){
    private val userLiveData = MutableLiveData<User>()
	val userName:LiveData<String> = Transformations.map(userLiveData){ user ->
        "${user.firstName} ${user.lastName}"
    }
    ...
}
```

Transformations的map()方法来对LiveData的数据类型进行转换。map()方法接收两个参数：第一个参数是原始的LiveData对象；第二个参数是一个转换函数，我们在转换函数里编写具体的转换逻辑即可.

将userLiveData声明成了private，以保证数据的封装性。外部使用的时候只 要观察userName这个LiveData就可以了。当userLiveData的数据发生变化时，map()方法 会监听到变化并执行转换函数中的逻辑，然后再将转换之后的数据通知给userName的观察者。

#### switchMap

LiveData对象的有可能ViewModel中的某个LiveData对象 是调用另外的方法获取的。