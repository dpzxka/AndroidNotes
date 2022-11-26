# 第十三章 Jetpack

> [Android Jetpack 开发资源 - Android 开发者  | Android Developers (google.cn)](https://developer.android.google.cn/jetpack)
>
> Jetpack是一个开发组件工具集，它的主要目的是编写出更加简洁的代码，并简化开发过程。

## 1、ViewModel

> 专门用于存放于外界相关的数据，界面上能看到的数据，相关变量都应该存放在ViewModel中给，减少Activity中逻辑

ViewModel的生命周期和Activity不同，可以保证选装屏幕时，不会重新创建，只有当Activity退出时才和Activity一块销毁。保证数据在旋转过程中不丢失。

![image-20221126165528423](C:/Users/xkadp/AppData/Roaming/Typora/typora-user-images/image-20221126165528423.png)