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