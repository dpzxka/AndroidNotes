# 第十章 Service

## 一、简述

Service是Android中实现程序后台运行的解决方案，适合执行不需要和用户交互而且还要求长期运行的任务。依赖与所在的应用程序进程，应用程序被杀掉，所依赖该进程的Service也会停止运行。

## 二、Android 多线程

### 1.线程的基本用法

- 类继承Thread

  ```kotlin
  class MyThread : Thread() {
  	override fun run() {
   		// 编写具体的逻辑
      }
  }
  ```

  `MyThread().start()`

- 实现Runnable

  ```kotlin
  class MyThread : Runnable {
   	override fun run() {
   		// 编写具体的逻辑
   	}
  }
  ```

  ```kotlin
  val myThread = MyThread()
  
  Thread(myThread).start
  ```

- Lambda

  ```kotlin
  Thread {
  	 // 编写具体的逻辑
  }.start()
  ```

- 内置函数

  ```kotlin
  thread {
   	// 编写具体的逻辑
  }
  ```

  

### 2.子线程更新UI

通过Handler更新UI

```kotlin
class ChangeUIActivity : AppCompatActivity() {
    lateinit var binding:ActivityChangeUiactivityBinding

    val updateText = 1

    val handle = object :Handler(Looper.getMainLooper()){
        override fun handleMessage(msg: Message) {
            super.handleMessage(msg)
            //在这里处理UI操作
            when(msg.what){
                updateText -> binding.textView.text = "Nice to meet you"
            }
        }
    }
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityChangeUiactivityBinding.inflate(layoutInflater)
        setContentView(binding.root)
        binding.changeBtn.setOnClickListener {
            thread {
                val msg = Message()
                msg.what = updateText
                handle.sendMessage(msg) //将Message对象发送出去
            }
        }
    }
}
```

其他更新UI方式

```kotlin
class ChangeUIActivity : AppCompatActivity() {
    lateinit var binding:ActivityChangeUiactivityBinding

    val updateText = 1

    val handle = object :Handler(Looper.getMainLooper()){
        override fun handleMessage(msg: Message) {
            super.handleMessage(msg)
            //在这里处理UI操作
            when(msg.what){
                updateText -> binding.textView.text = "Nice to meet you"
            }
        }
    }
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityChangeUiactivityBinding.inflate(layoutInflater)
        setContentView(binding.root)
        // 使用handler发送消息更新UI
        binding.changeBtn.setOnClickListener {
            thread {
                val msg = Message()
                msg.what = updateText
                handle.sendMessage(msg) //将Message对象发送出去
            }
        }
        // 使用handler.post(Runnable)更新UI
        binding.handlerPostBtn.setOnClickListener {
            handle.post {
                binding.textView.text = "this is handle post change"
            }
        }
        // 使用UI线程更新UI
        binding.uiChangeBtn.setOnClickListener {
            runOnUiThread {
                binding.textView.text = "this is runOnUiThread change"
            }
        }
        // 使用View.post( Runnable)更新UI
        binding.ViewPostBtn.setOnClickListener {
            binding.textView.post {
                binding.textView.text = "this is view post change"
            }
        }
    }
}
```

#### 解析异步消息处理机制

Android异步消息处理主要4个部分组成：Message、Handler、MessageQueue和Looper。

- <code>Meeeage</code>

  Message是在线程之间传递的消息，可以在内部携带少量的信息，用于在不同的线程之间传递数据。

  what字段、arg1和arg2字段携带一些整型数据，obj字段携带一个object对象

- <code>Handler</code>

  主要用于发送和处理消息，发送消息一般使用Handler的sendMessage()方法、post方法等，发出的消息会传递到Handler的handleMessage方法中。

- <code>MessageQueue</code>

  消息队列，主要用于存放所有通过Handler发送的消息。这部分消息会一直存在与消息队列中，等待被处理。每个线程只会有一个MessageQueue对象。

- <code>Looper</code>

  Looper是每个线程中的MessageQueue管家，调用Looper的loop方法后，会进入一个无限循环中，每当发现MessageQueue中存在一条消息时，就会取出，并传递到Handler的handleMessage方法中，每个线程只会有一个Looper对象。

> 异步消息处理流程：
>
> - 主线程中创建Handler对象，并重写handleMessage方法
>
>   构造函数中传入Looper.getMainLooper()，handleMessage中的代码回运行在主线程中
>
> - 子线程进行UI操作时，创建一个Message对象，通过Handler将消息发送出去
>
> - 消息被添加到MessageQueue的队列中，等待被处理
>
> - Looper会一直尝试从MessageQueue中取出消息，最后分发回Handler的handleMessage方法中

![image-20221122142119328](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221122142119328.png)

#### AsyncTask

## 三、Service



#### 创建Service

```kotlin
//Service需要在AndroidManifest文件中注册
class MyService : Service() {

    companion object{
        private const val TAG = "MyService"
    }
    //会在Service创建的时候调用，首次启动Service时候调用，之后不再调用
    override fun onCreate() {
        super.onCreate()
        Log.i(TAG, "onCreate: start")
    }

    //在每次Service启动的时候调用，执行的动作逻辑在此
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        Log.i(TAG, "onStartCommand: start")
        return super.onStartCommand(intent, flags, startId)
    }

    //Service中唯一抽象方法，必须在子类中实现
    override fun onBind(intent: Intent): IBinder {
        TODO("Return the communication channel to the service.")
    }

    //在Service销毁的时候调用
    override fun onDestroy() {
        super.onDestroy()
        Log.i(TAG, "onDestroy: start")
    }
}
```

#### 启动和开启Service

```kotlin
binding.startServiceBtn.setOnClickListener {
    val intent = Intent(this,MyService::class.java)
    startService(intent)
}
binding.stopServiceBtn.setOnClickListener {
    val intent = Intent(this,MyService::class.java)
    stopService(intent)
}
```

> Service也可以在内部调用stopSelf自我停止运行。

> 从Android8之后，只有应用保持在前台可见的状态情况下，Service才能保证稳定运行，应用进入后台后，Service随时都有可能被系统回收。
>
> 如果需要长期在后台执行任务，可以使用前台Service或WorkManager

#### Activity与Service的通信

新建DownloadBinder类，继承Binder，内部提供开始下载以及查看下载进度的方法。在onBinder方法返回DownloadBinder实例

```kotlin
//Service需要在AndroidManifest文件中注册
class MyService : Service() {

    private val mBinder = DownloadBinder()

    class DownloadBinder:Binder(){
        fun startDownload(){
            Log.d(TAG, "startDownload: executed")
        }
        fun getProgress():Int{
            Log.d(TAG, "getProgress: getProgress executed")
            return 0
        }
    }

    companion object{
        private const val TAG = "MyService"
    }
    //会在Service创建的时候调用
    override fun onCreate() {
        super.onCreate()
        Log.i(TAG, "onCreate: start")
    }

    //在每次Service启动的时候调用，执行的动作逻辑在此
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        Log.i(TAG, "onStartCommand: start")
        return super.onStartCommand(intent, flags, startId)
    }

    //Service中唯一抽象方法，必须在子类中实现
    override fun onBind(intent: Intent): IBinder {
        return mBinder
    }

    //在Service销毁的时候调用
    override fun onDestroy() {
        super.onDestroy()
        Log.i(TAG, "onDestroy: start")
    }
}
```

创建ServiceConnection匿名类，重写onServiceConnnected方法和onServiceDisconnected方法，onServiceConnected方法会在Activity与Service成功绑定的时候调用，而onServiceDisconnected只有在Service的创建进程崩溃或者被杀掉的时候调用。在onServiceConnnected方法中，通过向下转型获得DownloadBinder实例，Activity可以通过这个实例，控制Service中DownloadBinder中的public方法。

```kotlin
class ServiceTestActivity : AppCompatActivity() {
    lateinit var downloadBinder:MyService.DownloadBinder
    private val connection = object :ServiceConnection{
        override fun onServiceConnected(name: ComponentName?, service: IBinder?) {
            downloadBinder = service as MyService.DownloadBinder
            downloadBinder.startDownload()
            downloadBinder.getProgress()
        }

        override fun onServiceDisconnected(name: ComponentName?) {
            TODO("Not yet implemented")
        }

    }
    lateinit var binding:ActivityServiceTestBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityServiceTestBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.startServiceBtn.setOnClickListener {
            val intent = Intent(this,MyService::class.java)
            startService(intent)
        }
        binding.stopServiceBtn.setOnClickListener {
            val intent = Intent(this,MyService::class.java)
            stopService(intent)
        }
        binding.bindServiceBtn.setOnClickListener {
            val intent = Intent(this,MyService::class.java)
            /*表示在Activity和Service进行绑定后自动创建Service，会使MyService中的onCreate方法得到执行，onStartCommand不会执行*/
            bindService(intent, connection, Context.BIND_AUTO_CREATE)//绑定Service
        }
        binding.unBindServiceBtn.setOnClickListener {
            unbindService(connection)//接触绑定
        }
    }
}
```

## 四、Service生命周期

任何位置调用Context的startService的方法，service就会被启动：

`onCreate --> onStartConmmand` 

会一直保持运行状态，直到调用stopService方法或stopSelf方法或被系统回收。

startService方法之后调用stopService方法:

`onCreate --> onStartConmmand-->onDestory`

bindService方法之后调用unBinderService方法：

`onCreate-->自定义bind实例的方法-->onDestory`

startService方法之后调用bindService方法，必须要使启动和绑定同时不满足，Service才可以被销毁，同时调用unBinderService和stopService方法，onDestory方法才会执行。

## 五、Service其他技巧

### 1、前台Service

> 前台Service和普通Service最大区别，前台Service会有一个正在运行的图标在系统的状态栏中，下拉可以看到详细的信息，类似通知。



在MyService的onCreate方法中修改

```kotlin
override fun onCreate() {
    super.onCreate()
    Log.i(TAG, "onCreate: start")
    val manager = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
    if (Build.VERSION.SDK_INT>=Build.VERSION_CODES.O){
        val channel = NotificationChannel("my_service","前台Service通知",NotificationManager.IMPORTANCE_DEFAULT)
        manager.createNotificationChannel(channel)
    }
    val intent = Intent(this,MainActivity::class.java)
    val pi = PendingIntent.getActivity(this,0,intent, PendingIntent.FLAG_IMMUTABLE)
    val notification = NotificationCompat.Builder(this,"my_service")
        .setContentTitle("This is content title")
        .setContentText("This is content text")
        .setSmallIcon(R.drawable.testdemo)
        .setLargeIcon(BitmapFactory.decodeResource(resources,R.drawable.testdemo4))
        .setContentIntent(pi)
        .build()
    startForeground(1,notification)
}
```

创建通知后，通过startForeground方法，将通知显示出来。参1：通知id，参2：notification对象。显示出来后会在系统状态栏显示出来。

Android9.0之后，前台Service必须要在AndroidManifest.xml中进行权限声明：

```xml
<uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
```

#### 2、IntentService

Service中的代码默认运行在主线程中，如果在Service中处理一些耗时的逻辑，容易出现ANR(Application Not Responding)情况。需要在Service中每个具体的方法里开启一个子线程，在里面处理耗时逻辑。

```kotlin
class MyService : Service() {
 	...
 	override fun onStartCommand(intent: Intent, flags: Int, startId: Int): Int {
 		thread {
 			// 处理具体的逻辑
            stopSelf()//执行完自动停止
 		}
 		return super.onStartCommand(intent, flags, startId)
 	}
}
```



Android提供一个IntentService来，管理异步、自动停止的Service

```kotlin
class MyIntentService:IntentService("MyIntentService") {

    companion object{
        private const val TAG = "MyIntentService"
    }

//    这个方法里面处理耗时的逻辑，运行在子线程中。
    override fun onHandleIntent(intent: Intent?) {
        //打印当前进程ID
        Log.d(TAG, "onHandleIntent: Thread id is ${Thread.currentThread().name}")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, "onDestroy: Executed")
    }
}
```

获取主线程ID

```kotlin
binding.intentServiceBtn.setOnClickListener {
    //打印主线程ID
    Log.d("ServiceTestActivity", "onCreate: Thread id is ${Thread.currentThread().name}")
    val intent = Intent(this,MyIntentService::class.java)
    startService(intent)
}
```

IntentService也需要在AndroidManifest.xml中注册

```xml
<service
    android:name=".servicedemo.test.MyIntentService"
    android:enabled="true"
    android:exported="true" />
```