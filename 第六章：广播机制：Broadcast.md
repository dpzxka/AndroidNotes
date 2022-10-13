# 第六章：广播机制：Broadcast

## 1、定义

## 2、类型

### 标准广播：normal broadcasts

一种完全异步执行的广播，在广播发出后，所有的BroadcastReceiver几乎会在同一时刻接收到这条广播消息，没有先后顺序。效率高，不会被截断。



![image-20221013143608710](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221013143608710.png)

### 有序广播：ordered broadcasts

一种同步执行的广播，在广播发出后，同一时刻只会有一个BroadcastReceiver能够接收到这条广播，当这个BroadcastReceiver中的逻辑执行完毕后，广播才会继续传递。有先后顺序，优先级高的先收到，前一个可以截断正在传递的广播，后续的就会无法接收到这条广播。

![image-20221013144231672](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221013144231672.png)

## 3、如何接受广播

> 两种接受方法：
>
> 1. 动态注册接受：在代码中注册
> 2. 在AndroidManifest.xml中注册

### 动态注册：

> 动态注册的BroadcastReceiver可以自由地控制注册与注销，需要开机才可以

定义一个类，继承BroadcastReceiver，重写父类的onReceive()方法，当有广播来时，会执行里面的方法。

1. 定义类，继承BroadcastReceive，
2. 获得过滤器对象：IntentFilter
3. 添加需要注册的广播的action：
4. 通过registerReceiver注册广播
5. 通过unregisterReceiver注销广播

```kotlin
package com.example.kotlinlearn.broadcastdemo.simplebroadcst

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.content.IntentFilter
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Toast
import com.example.kotlinlearn.databinding.ActivityBroadcastReceiverBinding

/**
 * 动态注册监听广播*/
class BroadcastReceiverActivity : AppCompatActivity() {
    lateinit var binding:ActivityBroadcastReceiverBinding
    lateinit var timeChangeReceiver:TimeChangeReceiver
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityBroadcastReceiverBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val intentFilter = IntentFilter() //过滤器
        //action添加需要监听的广播，
        intentFilter.addAction("android.intent.action.TIME_TICK")
        timeChangeReceiver = TimeChangeReceiver()
        //注册广播
        registerReceiver(timeChangeReceiver, intentFilter)
    }

    companion object{
        fun actionStart(context: Context){
            val intent = Intent(context,BroadcastReceiverActivity::class.java)
            context.startActivity(intent)
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        //注销广播
        unregisterReceiver(timeChangeReceiver)
    }

    /*1、 动态注册监听广播,定义内部类，继承BroadcastReceiver，实现onReceive方法*/
    inner class TimeChangeReceiver:BroadcastReceiver(){
        /*收到广播时，onReceiver会被执行*/
        override fun onReceive(context: Context?, intent: Intent?) {
            Toast.makeText(context,"time has changed",Toast.LENGTH_SHORT).show()
        }
    }
}
```

### 静态注册广播

1. 创建广播接收器

   ```kotlin
   package com.example.kotlinlearn.broadcastdemo.simplebroadcst
   
   import android.content.BroadcastReceiver
   import android.content.Context
   import android.content.Intent
   import android.widget.Toast
   
   class BootCompleteRecevier : BroadcastReceiver() {
   
       override fun onReceive(context: Context, intent: Intent) {
           // This method is called when the BroadcastReceiver is receiving an Intent broadcast.
           Toast.makeText(context,"Boot complete",Toast.LENGTH_SHORT).show()
       }
   }
   ```

2. 在AndroidManifest.xml中注册

   新增权限：

   ```xml
   <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
   ```

```xml
<receiver
    android:name=".broadcastdemo.simplebroadcst.BootCompleteRecevier"
    android:enabled="true"
    android:exported="true">

    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>
```

## 4、如何发送广播

### 发送标准广播

```kotlin
binding.sendStanderBroadcast.setOnClickListener {
            val intent = Intent("com.zhangtao.broadcast.MY_BROADCASERECEIVER")
            //传入当前应用程序的包名
            /*8.0系统之后，静态注册的BroadcastReceiver是无法接收隐式广播的，而默认情况下我们发出
的自定义广播恰恰都是隐式广播。因此这里一定要调用setPackage()方法，指定这条广播是
发送给哪个应用程序的，从而让它变成一条显式广播*/
            intent.setPackage(packageName)
            sendBroadcast(intent)
        }
```

### 发送有序广播

```kotlin
binding.sendOrderBroadcast.setOnClickListener {
    val intent = Intent("com.zhangtao.broadcast.MY_BROADCASERECEIVER")
    intent.setPackage(packageName)
    //参2：与权限相关的字符串
    sendOrderedBroadcast(intent,null)
}
```

设置广播接受优先级：

通过priority设置优先级，

```xml
<receiver android:name=".broadcastdemo.simplebroadcst.AnotherMyBroadcastReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="100">
        <action android:name="com.zhangtao.broadcast.MY_BROADCASERECEIVER"/>
    </intent-filter>
</receiver>
```

优先级高的，会优先其他广播接收到广播，接收到后可以中断广播

```kotlin
override fun onReceive(context: Context, intent: Intent) {
    // This method is called when the BroadcastReceiver is receiving an Intent broadcast.
    Toast.makeText(context,"received in AnotherMyBroadcastReceiver",Toast.LENGTH_SHORT).show()
    abortBroadcast()
}
```