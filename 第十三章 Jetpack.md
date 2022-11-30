# 第十三章 Jetpack

> [Android Jetpack 开发资源 - Android 开发者  | Android Developers (google.cn)](https://developer.android.google.cn/jetpack)
>
> Jetpack是一个开发组件工具集，它的主要目的是编写出更加简洁的代码，并简化开发过程。

## 一、ViewModel

> 专门用于存放于外界相关的数据，界面上能看到的数据，相关变量都应该存放在ViewModel中给，减少Activity中逻辑

ViewModel的生命周期和Activity不同，可以保证旋转屏幕时，不会重新创建，只有当Activity退出时才和Activity一块销毁。保证数据在旋转过程中不丢失。

![image-20221126165528423](https://raw.githubusercontent.com/dpzxka/typora_pictures/master/image-20221126165528423.png)

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

![image-20221126233050589](https://raw.githubusercontent.com/dpzxka/typora_pictures/master/image-20221126233050589.png)

> 当获取的生命周期状态是CREATED的时候，说明onCreate()方法已经执行了，但 是onStart()方法还没有执行。当获取的生命周期状态是STARTED的时候，说明onStart() 方法已经执行了，但是onResume()方法还没有执行.

## 三、LiveData

是Jetpack提供的一种响应式编程组件，它可以包含任何类型的数据，并在数据发生变化的时候通知给观察者。

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
        /*viewModel.counter.observe(this, Observer {
            binding.infoText.text = it.toString()
        })*/
        viewModel.counter.observe(this){
            binding.infoText.text = it.toString()
        }
    }

    override fun onPause() {
        super.onPause()
        sp.edit {
            putInt("count_reserved",viewModel.counter.value?:0)
        }
    }
}
```

> 当一个Java方法同时接收两个单抽象方法接口参数时，要么同时使用函数式API的写法，要么都不使用函数式API的写法。由于我们第一个参数传的是this，因此第二个参数就无法使用函数式API的写法了。

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

将userLiveData声明成了private，以保证数据的封装性。外部使用的时候只要观察userName这个LiveData就可以了。当userLiveData的数据发生变化时，map()方法会监听到变化并执行转换函数中的逻辑，然后再将转换之后的数据通知给userName的观察者。

#### switchMap

LiveData对象的有可能ViewModel中的某个LiveData对象 是调用另外的方法获取的。

如单例类：根据传入的参数，从服务器获取或者数据库查到对应的User对象，每次见传入的userId用作用户名来创建一个新的User对象，每次调用 getUser()方法都会返回一个新的LiveData实例。

```kotlin
object Repository {
    fun getUser(userId: String): LiveData<User> {
        val liveData = MutableLiveData<User>()
        liveData.value = User(userId, userId, 0)
        return liveData
    }
}
```

在对应的ViewModel中定义一个getUser方法，用来调用repositroy中的getUser方法来获取LiveData对象

[switchMap应用场景]()：

如果ViewModel中的某个LiveData对象是调用另外的方法获取的，那么我们就可以借助 switchMap()方法，将这个LiveData对象转换成另外一个可观察的LiveData对象

修改MainVewModel：

```kotlin
class MainViewModel(countReserved:Int) : ViewModel() {

    val counter:LiveData<Int> get() = _counter

    private var _counter = MutableLiveData<Int>()

    init {
        _counter.value = countReserved
    }

    //定义一个新的userIdLiveData对象，用来观察userId数据变化
    private val userIdLiveData = MutableLiveData<String>()
    
    //调用switchMap方法，用来对另一个可观察的LiveData对象进行转换
    //参1：新增的userIdLiveData，switchMap方法会对它进行观察，参2：一个转换函数，这个转换函数返回的必须是一个LiveData对象。
    val user:LiveData<User> = Transformations.switchMap(userIdLiveData){ userId ->
        Repository.getUser(userId)
    }

    fun getUser(userId:String){
        userIdLiveData.value = userId
    }
    /*fun getUser(userId:String):LiveData<User>{
        return Repository.getUser(userId)
    }*/
}
```

> 外部调用MainViewModel的getUser方法来获取用户数据时，并不会发起任何请求或函数调用，只会将传入的数据设置到userIdLiveData中。
>
> 一旦userLiveData的数据发生变化，观察的userIdLiveData的switchMap方法就会执行，调用编写的转换函数。
>
> 在转换函数中调用Repository.getUSer方法获取真正的数据。同时switchMap方法会将Repositroy.getUser方法返回的LiveData对象转换成一个可观察的LiveData对象，在Activity中，只需要观察这个对象即可。

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    binding = ActivityViewModelDemoBinding.inflate(layoutInflater)
    setContentView(binding.root)

    binding.getUserBtn.setOnClickListener {
        val userId = (0..10000).random().toString()
        //将数据设置到userIdLiveData中
        viewModel.getUser(userId)
    }
    viewModel.user.observe(this){
        binding.infoText.text = it.firstName
    }
}
```

[注意]()

如果ViewModle中某个获取数据的方法没有参数，创建一个空的LiveData对象，

```kotlin
//方法没有参数情况下,定义一个不指定具体数据类型的LiveData
private val refreshLiveData = MutableLiveData<Any?>()
val refreshResult = Transformations.switchMap(refreshLiveData){
    Repository.refresh()
}
/*  LiveData内部不会判断即将设置的数据和原有数据是否相同，只要调用了setValue()或postValue()方法，
    就一定会触发数据变化事件
*/
fun refresh(){
    refreshLiveData.value = refreshLiveData.value
}
```

> LiveData之所以能够成为Activity与ViewModel之间通信的桥梁，并且还不会有内存泄漏的风险，靠的就是Lifecycles组件。LiveData在内部使用了Lifecycles组件来自我感知生命周期的变化，从而可以在Activity销毁的时候及时释放引用，避免产生内存泄漏的问题
>
> 如果在Activity处于不可见状态的时候，LiveData发生了多次数据变化，当 Activity恢复可见状态时，只有最新的那份数据才会通知给观察者，前面的数据在这种情况下相 当于已经过期了，会被直接丢弃。

## 四、Room

> ORM（Object Relational Mapping）也叫对象关系映射。编程语言是面向对象语言，而使用的数据库则是关系型数据库，将面向对象的语言和面向关系的数据库之间建立一种映射关系，称为ORM

### 1、增删改查

ROOM主要有三部分组成：

Entity：用于定义封装实际数据的实体类，每个实体类都会在数据库中有一张对应的表，并且表中的列是根据实体类中的字段自动生成的

Dao：Dao是数据访问对象的意思，通常会在这里对数据库的各项操作进行封装，在实际编程的时候，逻辑层就不需要和底层数据库打交道了，直接和Dao层进行交互即可

Database：用于定义数据库中的关键信息，包括数据库的版本号、包含哪些实体类以及提供Dao层的访问实例。

添加依赖：

> 由于于Room会根据我们在项目中声明的注解来动态生成代码，因此这里一定要使用kapt引入Room的编译时注解库，而启用编译时注解功能则一定要先添加kotlin-kapt插件。kapt只能在kotlin项目中使用，java中使用annotationProcessor

```groovy
plugins {
 	...
    id 'kotlin-kapt'
}

dependencies{
	...
    /*room ORM框架*/
	implementation "androidx.room:room-runtime:2.4.2"
	kapt "androidx.room:room-compiler:2.4.2"    
}

```

**基本用法：**

1、定义entity层实体类，每个实体类都添加一个id字段，并将这个字段设置为主键。

```kotlin
/**
 * Entity 声明成一个实体类
 */
@Entity
data class Users(var firstName:String, val lastName:String, var age:Int) {

    /*设置ID字段，将此字段设置为主键。autoGenerate 指定为true，使得主键的值自动生成*/
    @PrimaryKey(autoGenerate = true)
    var id:Long = 0
}
```

2、定义Dao层，所有访问数据库的操作都是在这里封装的。而Dao要做的事情就是覆盖所有的业务需求，使得业务方永远只需要与Dao层进行交互，而不必和底层的数据库打交道

```kotlin
//添加Dao注解，Room才可以识别成Dao。根据业务需求对各种数据库操作进行封装。
@Dao
interface UserDao {
    //表示会将参数中传入的User对象插入数据库中，插入完成后还会将自动生成的主键id值返回
    @Insert
    fun insertUser(users: Users):Long

    //将参数中传入的User对象更新到数据库当中
    @Update
    fun updateUser(newUsers: Users)

    //从数据库中查询数据，或者使用非实体类参数来增删改数据，那么就必须编写SQL语句
    @Query("select * from Users")
    fun loadAllUsers():List<Users>

    //将方法中传入的参数指定到SQL语句当中
    @Query("select * from Users where age > :age")
    fun loadUsersOlderThan(age:Int):List<Users>
    
    //会将参数传入的User对象从数据库中删除
    @Delete
    fun deleteUser(users:Users)

    //如果是使用非实体类参数来增删改数据，那么也要编写SQL语句，而且必须使用用@Query注解
    @Query("delete from Users where lastName = :lastName")
    fun deleteUsersByLastName(lastName:String):Int
}
```

3、定义Database层：定义数据库版本号，包含哪些实体类，以及提供Dao层的访问实例

AppDatabase类必须继承自RoomDatabase类，并且一定要使用abstract关键字将它声明成抽象类，然后提供相应的抽象方法，用于获取之前编写的Dao的实例,只需要进行方法声明即可，具体是由Room底层自动完成。

```kotlin
@Database(version = 1, entities = [Users::class])
abstract class AppDatabase:RoomDatabase() {
    abstract fun userDao() : UserDao

    companion object {
        private var instance: AppDatabase? = null

        fun getDatabase(context:Context):AppDatabase{
            instance?.let {
                return it
            }
            //构建databaseBuilder实例，参1：applicationContext，不能用普通context会内存泄露
            //参2：AppDatabase的Class类型，参3：数据库名
            return Room.databaseBuilder(context.applicationContext,AppDatabase::class.java,"app_database")
                .build().apply {
                    instance = this
                }
        }
    }
}
```



Activity中使用：

```kotlin
class RoomDemoActivity : AppCompatActivity() {
    companion object{
        private const val TAG = "RoomDemoActivity"
    }
    lateinit var binding:ActivityRoomDemoBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityRoomDemoBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val userDao = AppDatabase.getDatabase(this).userDao()
        val user1 = Users("Tom","Brady",40)
        val user2 = Users("Tom","Hanks",90)
        binding.addDataBtn.setOnClickListener {
            thread {
                user1.id = userDao.insertUser(user1)
                user2.id = userDao.insertUser(user2)
            }
        }
        binding.updateDataBtn.setOnClickListener {
            thread {
                user1.age = 42
                userDao.updateUser(user1)
            }
        }
        binding.deleteDataBtn.setOnClickListener {
            thread {
                userDao.deleteUsersByLastName("Hanks")
            }
        }
        binding.queryDataBtn.setOnClickListener {
            thread {
                for (user in userDao.loadAllUsers()){
                    Log.d(TAG, "onCreate: ${user.toString()}")
                }
            }
        }
    }
}
```

> 由于数据库操作属于耗时操作，Room默认是不允许在主线程中进行数据库操作的，因此增删改查的功能都放到了子线程中。不过为了方便测试，Room还提供了一个更加简单的方法，如下所示：
>
> ```kotlin
> Room.databaseBuilder(context.applicationContext, AppDatabase::class.java,"app_database")
>  .allowMainThreadQueries()
>  .build()
> ```

### 2、数据库升级

> 数据库框架LitePal

> 测试阶段：简单方法：只要数据库进行了升级，Room就会将当前的数据库销毁，然后再重新创建，副作用就是之前数据库中的所有数据就全部丢失了
>
> ```kotlin
> Room.databaseBuilder(context.applicationContext, AppDatabase::class.java,"app_database")
>  .fallbackToDestructiveMigration()
>  .build()
> ```

#### 2.1 新增表

新增Book实体类

```kotlin
@Entity
data class Book(var name: String, var pages: Int) {

    @PrimaryKey(autoGenerate = true)
    var id: Long = 0
}
```

新增BookDao接口

```kotlin
@Dao
interface BookDao {

    @Insert
    fun insertBook(book: Book):Long

    @Query("select * from Book")
    fun loadAllBooks():List<Book>
}
```

修改Appdatabase,升级数据库升级逻辑

```kotlin
/*数据库升级，新增Book表,将Book类添加到了实体类声明*/
@Database(version = 2, entities = [Users::class, Book::class])
abstract class AppDatabase:RoomDatabase() {
    abstract fun userDao() : UserDao

    //新增表
    abstract fun bookDao():BookDao

    companion object {

        //升级
        /**
         * 参数(1,2)表示当数据库版本从1升级到2的时候就执行这个匿名类中的升级逻辑
         * */
        val MIGRATION_1_2= object :Migration(1,2){
            override fun migrate(database: SupportSQLiteDatabase) {
                /*Book表的建表语句必须和Book实体类中声明的结构完全一致，否则Room就会抛出异常*/
                database.execSQL("create table Book (id integer primary key autoincrement not null," +
                        "name text not null, pages integer not null)")
            }
        }

        private var instance: AppDatabase? = null

        fun getDatabase(context:Context):AppDatabase{
            instance?.let {
                return it
            }
            return Room.databaseBuilder(context.applicationContext,AppDatabase::class.java,"app_database")
                .addMigrations(MIGRATION_1_2)
                .build().apply {
                    instance = this
                }
        }
    }
}
```

#### 2.2 表中新增列

Book实体类新增author字段

```kotlin
@Entity
data class Book(var name: String, var pages: Int,var author:String) {

    @PrimaryKey(autoGenerate = true)
    var id: Long = 0
}
```

修改Appdatabase

```kotlin
/*数据库升级，新增Book表中的字段*/
@Database(version = 3, entities = [Users::class, Book::class])
abstract class AppDatabase:RoomDatabase() {
    abstract fun userDao() : UserDao

    //新增表
    abstract fun bookDao():BookDao

    companion object {

        //升级
        /**
         * 参数(1,2)表示当数据库版本从1升级到2的时候就执行这个匿名类中的升级逻辑
         * */
        val MIGRATION_1_2= object :Migration(1,2){
            override fun migrate(database: SupportSQLiteDatabase) {
                /*Book表的建表语句必须和Book实体类中声明的结构完全一致，否则Room就会抛出异常*/
                database.execSQL("create table Book (id integer primary key autoincrement not null," +
                        "name text not null, pages integer not null)")
            }
        }
        
        val MIGRATION_2_3= object :Migration(2,3){
            override fun migrate(database: SupportSQLiteDatabase) {
                /*Book表 新增author列*/
                database.execSQL("alter table Book add column author text not null default 'unknown'")
            }
        }

        private var instance: AppDatabase? = null

        fun getDatabase(context:Context):AppDatabase{
            instance?.let {
                return it
            }
            return Room.databaseBuilder(context.applicationContext,AppDatabase::class.java,"app_database")
                .addMigrations(MIGRATION_1_2, MIGRATION_2_3)
                .build().apply {
                    instance = this
                }
        }
    }
}
```

## 五、WorkManager



> WorkManager很适合用于处理一些要求定时执行的任务，它可以根据操作系统的版本自动选择底层是使用AlarmManager实现还是JobScheduler实现，从而降低使用成本。另外， 它还支持周期性任务、链式任务处理等功能，是一个非常强大的工具.
>
> WorkManager和Service并不相同，也没有直接的联系。 Service是Android系统的四大组件之一，它在没有被销毁的情况下是一直保持在后台运行的。 而WorkManager只是一个处理定时任务的工具，它可以保证即使在应用退出甚至手机重启的情况下，之前注册的任务仍然将会得到执行，因此WorkManager很适合用于执行一些定期和服务器进行交互的任务，比如周期性地同步数据。

### 1、基本用法

添加依赖：

```groovy
/*workManager*/
implementation "androidx.work:work-runtime:2.7.1"
```

**WorkManager的基本用法：**

1. 定义一个后台任务，并实现具体的任务逻辑
2. 配置该后台任务的运行条件和约束信息，并构建后台任务请求
3. 将该后台任务请求传入WorkManager的enqueue()方法中，系统会在合适的时间运行

#### 1、定义后台任务

每一个后台任务都必须继承自Worker类，并调用它唯一的构造函数。然后重写父类中的doWork()方法，在这个方法中编写具体的后台任务逻辑即可

doWork()方法不会运行在主线程当中，doWork()方法要求返回一个Result对象，用于表示任务的运行结果，成功就返回Result.success()，失败就返回Result.failure()，Result.retry()方法，它其实也代表着失败，结合WorkRequest.Builder的setBackoffCriteria()方法来重新执行任务

```kotlin
class SimpleWorker(context: Context,params:WorkerParameters) :Worker(context,params){
    private val TAG = "SimpleWorker"

    override fun doWork(): Result {
        Log.d(TAG, "doWork: do work in SimpleWorker")
        return Result.success()
    }
}
```

#### 2、配置该后台任务的运行条件和约束信息，并构建后台任务请求

OneTimeWorkRequest.Builder是WorkRequest.Builder的子类，用于构建单次运行的后台任务请求。

```kotlin
val request = OneTimeWorkRequest.Builder(SimpleWorker::class.java).build()
```

OneTimeWorkRequest.Builder是WorkRequest.Builder的子类，用于构建单次运行的后台任务请求。

WorkRequest.Builder还有另外一个子类PeriodicWorkRequest.Builder，可用于构建周期性运行的后台任务请求，但是为了降低设备性能消耗,PeriodicWorkRequest.Builder构造函数中传入的运行周期间隔不能短于15分钟

```kotlin
val request = PeriodicWorkRequest.Builder(SimpleWorker::class.java, 15, TimeUnit.MINUTES).build()
```

#### 3、传入请求

系统就会在合适的时间去运行。没有指定任何约束，后台任务基本上会在点击按钮之后立刻运行

```kotlin
WorkManager.getInstance(context).enqueue(request)
```

### 2、WorkManager处理复杂的任务

延迟执行定时任务

```kotlin
//延迟执行任务，延迟5分钟后运行
val request2 = OneTimeWorkRequest.Builder(SimpleWorker::class.java)
    .setInitialDelay(5,TimeUnit.MINUTES)
    .build()
```

给后台任务请求添加标签

> 标签功能：可以通过标签来取消后台的任务请求。

```kotlin
val request2 = OneTimeWorkRequest.Builder(SimpleWorker::class.java)
    .setInitialDelay(5,TimeUnit.MINUTES)
    .addTag("simple")
    .build()

//有标签之后，可以通过后台取消任务
WorkManager.getInstance(this).cancelAllWorkByTag("simple")
```

没有标签也可以通过id来取消后台任务

```kotlin
//没有标签通过Id来取消后台任务
WorkManager.getInstance(this).cancelWorkById(request.id)
```

一次性取消所有的后台任务请求：

```kotlin
//取消所有的后台任务请求
WorkManager.getInstance(this).cancelAllWork()
```

如果后台任务返回了result.retry(),可以结合setBackoffCriteria方法重新执行任务。

```kotlin
val request3 = OneTimeWorkRequest.Builder(SimpleWorker::class.java)
                .setInitialDelay(5,TimeUnit.MINUTES)//延迟五分钟过后执行
                    /*setBackoffCriteria
                    * 参1：于指定如果任务再次执行失败，下次重试 的时间应该以什么样的形式延迟。
                    *   LINEAR:代表下次重试时间以线性的方式延迟
                    *   EXPONENTIAL，代表下次重试时间以指数的方式延迟
                    * 参2，参3：用于指定在多久之后重新执行任务，时间最短不能少于10秒钟
                    * */
                .setBackoffCriteria(BackoffPolicy.LINEAR,10,TimeUnit.SECONDS)
                .build()
```

> doWork()方法中返回 Result.success()和Result.failure()又有什么作用?
>
> 用于通知任务运行结果，
>
> ```kotlin
> WorkManager.getInstance(this).getWorkInfoByIdLiveData(request.id)
>     .observe(this){ workInfo ->
>         if (workInfo.state == WorkInfo.State.SUCCEEDED){
>             Log.d("TAG", "onCreate: do wrok succeeded")
>         } else if (workInfo.state == WorkInfo.State.FAILED) {
>             Log.d("TAG", "onCreate: do work failed")
>         }
>     }
> ```
>
> getWorkInfoByIdLiveData()方法，并传入后台任务请求的id，会返回一个 LiveData对象。然后我们就可以调用LiveData对象的observe()方法来观察数据变化了， 以此监听后台任务的运行结果(也可以getWorkInfosByTagLiveData)

链式任务

假设定义3个独立的后台任务：同步数据、压缩数据和上传数据

```kotlin
val sync = ...
val compress = ...
val upload = ...
WorkManager.getInstance(this)
 .beginWith(sync)
 .then(compress)
 .then(upload)
 .enqueue()

```

WorkManager要求，必须在前一个后台任务运行成功之后，下一个后台任务才会运行。如果某个后台任务运行失败，或者被取消了，那么接下来的后台任务就都得不到运行