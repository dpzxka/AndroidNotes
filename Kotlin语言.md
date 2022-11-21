## Kotlin语言

### 简介：

### 变量：

> val :(value缩写)用来声明一个不可变的变量，这种变量在初始赋值之后就再也不能重新赋值，对应java中final变量
>
> var:(variable)用来声明一个可变的变量，这种变量在初始赋值之后仍然可以再重新赋值，对应java中非final变量

```kotlin
val a = 10
//Kotlin中，a会被自动推到成整型变量
//显示声明变量,Kotlin中抛弃了java中的基本数据类型，全部使用对象数据类型，Int是一个类，拥有自己的方法和继承结构
val a:Int = 10
```



| Java基本数据类型 | Kotlin对象数据类型 | 数据类型说明 |
| ---------------- | ------------------ | ------------ |
| int              | Int                | 整型         |
| long             | Long               | 长整型       |
| short            | Short              | 短整型       |
| float            | Float              | 单精度浮点型 |
| double           | Double             | 双精度浮点型 |
| boolean          | Boolean            | 布尔型       |
| char             | Char               | 字符型       |
| byte             | Byte               | 字节型       |

> 永远优 先使用val来声明一个变量，而当val没有办法满足你的需求时再使用var。

### 函数

> 函 数和方法就是同一个概念，这两种叫法都是从英文翻译过来的，函数翻译自function，方法翻 译自method，它们并没有什么区别，只是不同语言的叫法习惯不一样而已。
>
> main()函数就是一个函数，只不过它比较特 殊，是程序的入口函数，即程序一旦运行，就是从main()函数开始执行的。

语法规则：fun 函数名(参数名：参数类型，参数名：参数类型)：返回值类型(可选，没有返回值不写)

返回两个参数最大的

```kotlin
fun largerNumber(num1:Int,num2:Int):Int{
    return max(num1,num2)
}
//当函数只有一行代码的时候，可以不必编写函数体，直接将唯一的一行代码写在函数的尾部，中间用等号连接。
fun largerNumber2(num1: Int, num2: Int): Int = max(num1, num2)
//根据Kotlin的类型推导机制，由于max函数返回的是一个Int值，不需要显示地声明返回值类型
fun largerNumber3(num1: Int, num2: Int) = max(num1, num2)

```

### 程序的逻辑控制

#### if条件语句

`if和when`

**if条件语句**

返回两个参数最大的

```kotlin
fun largerNumber_if(num1: Int, num2: Int): Int {
    var value = 0
    if (num1 > num2) {
        value = num1
    } else {
        value = num2
    }
    return value
}
```

Kotlin中的if语句相比较Java中有一个额外的功能，可以有返回值。if语句使用每个条件的最后一行代码作为返回值。

```kotlin
fun largerNumber_if_2(num1: Int, num2: Int): Int {
    val value = if (num1 > num2) {
        num1
    } else {
        num2
    }
    return value
}
```

当行数只有一行代码时，可以省略函数体部分。

```kotlin
fun largerNumber_if_4(num1: Int, num2: Int) = if (num1 > num2) {
        num1
    } else {
        num2
}
```

```kotlin
fun largerNumber_if_5(num1: Int, num2: Int) = if (num1 > num2) num1 else num2
```

#### when条件语句

> 类似Java中Switch

when语句以内需传入一个任意类型的参数，可以在when的结构题定义一系列的条件，格式：

匹配值 -> {执行逻辑}

```kotlin
fun getScore(name:String) = when(name){
    "Tom" -> 86
    "Jim" -> 77
    "Jack" -> 95
    "Lily" -> 100
    "else" -> 0
}
```

### 面向对象编程

```kotlin
class Person {
    var name = ""
    var age = 0
    
    fun eat(){
        println("$name is eating. He is $age years old.")
    }
}
```

实例化对象

```kotlin
fun main(){
    val p = Person()
    p.age = 20
    p.name = "Tom"
    p.eat()
}
```

任何一个非抽象类默认都是不可继承的，相当于java中年给类声明了final关键字。默认所有非抽象类都不可继承。非抽象类继承，添加关键字：open

```
open class Person{
...
}
```

使用<code>:</code> 继承，效果同java中extends

```kotlin
class Student : Person(){
    var sno = ""
    var grade = 0
}
```

Kotlin同样会有构造函数，分为，主构造函数和次构造函数。

主构造函数，每个类都会有一个不带参数的主构造函数，也可以显式地给它指明参数，主函数特点是没有函数体，直接定义在类名的后面即可。

```kotlin
class Student(val sno:String,val grade:Int) : Person(){}
```

实例化的时候，必须传入构造函数中要求的参数

```kotlin
val student = Student("a123",5)
```

主构造函数中添加逻辑，需要添加init结构体

```kotlin
class Student(val sno:String,val grade:Int) : Person(){
    init{
        println("sno is $sno")
        println("grade is $grade")
    }
}
```

任何一个类中，只有一个主构造函数，但可以有多个次构造函数，次构造函数也可以用于实例化一个类。

当一个类既有主构造函数又有次构造函数时，所有的次构造函数都必须调用主构造函数(包括间接调用)。

```kotlin
class Student(val sno:String,val grade:Int,name:String,age:Int) : Person(name,age){
    init{
        println("sno is $sno")
        println("grade is $grade")
    }
    constructor(name: String,age: Int):this(" ",0,name,age){
    }
    constructor():this("ti",0)
}
```

次构造函数通过`constructor`关键字定义

当一个类没有显示定义主构造函数且定义了次构造函数时，它就是没有主构造函数。继承的时候，不需要加括号。

```kotlin
class StudentDemo:Person {
    constructor(name:String,age:Int):super(name,age)
    //没有主构造函数，次构造函数只能调用父类的构造函数，通过super关键字。
}
```

### 接口

> Kotlin也是单继承结构，任何一个类最多只能继承一个类，可以实现任意多个接口。

```kotlin
interface Study {
    fun readBooks()
    fun doHomeWork()
}
```

实现接口，使用`：`，代替java中implements，中间用`,`隔离。

```kotlin
class Student(val sno:String,val grade:Int,name:String,age:Int) : Person(name,age),Study{
    init{
        println("sno is $sno")
        println("grade is $grade")
    }
    constructor(name: String,age: Int):this(" ",0,name,age){
    }
    constructor():this("ti",0)

    override fun readBooks() {
        TODO("Not yet implemented")
    }

    override fun doHomeWork() {
        TODO("Not yet implemented")
    }
}
```

允许对接口中定义的函数进行默认实现。默认实现函数可以自由选择是否实现，不实现会使用默认的逻辑。

```kotlin
interface Study {
    fun readBooks()
    fun doHomeWork(){
        println("do homework default implementation")
    }
}
```

### 修饰符

> public，表示对所有类可见，默认项（java中默认项是protected）
>
> private：同java，表示当前类内部可见
>
> protected，只对当前类和子类可见
>
> internal，对同一模块中的类可见

| 修饰符    | Java                               | Kotlin             |
| --------- | ---------------------------------- | ------------------ |
| public    | 所有类可见                         | 所有类可见（默认） |
| private   | 当前类可见                         | 当前类可见         |
| protected | 当前类、子类、同一包路径下的类可见 | 当前类、子类可见   |
| default   | 同一包路径下的类可见（默认）       | 无                 |
| internal  | 无                                 | 同一模块中的类可见 |

### 数据类：

```kotlin
data class Cellphone (val brand:String,val price:Double) {
}
```

类前面加上`data`关键词，表示当前类是一个数据类，会根据主构造函数中参数自动实现equals(),hashCode(),toString()等固定且无实际逻辑意义的方法。

> 当一个类没有任何代码，可以将尾部的大括号省略

```kotlin
val cellphone1 = Cellphone("Samsung",1299.99)
val cellphone2 = Cellphone("Samsung",1299.99)
println(cellphone1)
println("cellphone1 equals cellephone2 "+(cellphone1==cellphone2))
```

### 单例

```kotlin
object Singleton {
    fun singletonTest(){
        println("singletonTest is called")
    }
}
```

调用方式

```kotlin
Singleton.singletonTest()
```

### Lambda编程

#### 集合创建与遍历

创建：

```kotlin
val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
```

或者

```kotlin
val list = ArrayList<String>()
list.add("Apple")
list.add("Banana")
list.add("Orange")
list.add("Pear")
list.add("Grape")
```

遍历：

```kotlin
val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
println(list)
for (fruit in list){
    println(fruit)
}
```

listOf()函数创建是一个不可变的集合，只能用于读取，无法添加、修改和删除操作。

创建可变集合：mutableListOf()函数。

```kotlin
fun arrayListTest3(){
    val list = mutableListOf<String>("Apple","Banana","Orange","Pear","Grape")
    list.add("Watermelon")
    for (fruit in list){
        println(fruit)
    }
}
```

Set集合，不可以存放重复元素，存放多个相同元素，只会保留其中一份。

```
fun setTest1(){
    val set = setOf<String>("Apple","Banana","Orange","Pear","Grape")
    for (fruit in set){
        println(fruit)
    }
}
```

Map函数

```kotlin
fun mapTest1(){
    val map = HashMap<String,Int>()
    map.put("Apple",1)
    map.put("Banana",2)
    map.put("Orange",3)
    map.put("Pear",4)
    map.put("Grape",5)
}
//标准写法
fun mapStandTest(){
    val map=HashMap<String,Int>()
    map["Apple"] = 1
    map["Banana"] = 2
    map["Orange"] = 3
    map["Pear"] = 4
    map["Gape"] = 5
}

//简化写法
fun mapSimpleTest(){
    val map = mapOf("Apple" to 1,"Banana" to 2,"Orange" to 3,"Pear" to 4,"Grape" to 5)
    //to 不是关键字，而是一个infix函数
}

//遍历
fun mapSimpleForIn(){
    val map = mapOf("Apple" to 1,"Banana" to 2,"Orange" to 3,"Pear" to 4,"Grape" to 5)
    //to 不是关键字，而是一个infix函数
    for ((fruit,number) in map){
        println("fruit is $fruit, number is $number")
    }
}
```

#### 集合的函数式API

Lambda是一小段可以作为参数传递的代码。

Lambda表达式的语法结构：`{参数名1：参数类型，参数名2：参数类型 -> 函数体}`

```kotlin
//遍历单词最长
fun findMaxLength(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val maxLengthFruit = list.maxByOrNull { it.length }
    println("max length fruit is $maxLengthFruit")
}
```

最外层是一对大括号，如果有参数传入到Lambda表达式中，还需要生命参数列表，参数列表的结尾使用一个`->`符号，表示参数列表的结束以及函数体的开始，函数体中可以编写任意行代码(不建议太长)，并且最后一行代码会自动作为Lambda表达式的返回值。



```kotlin
fun findMaxLength1(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val lambda = {fruit:String -> fruit.length}
    val maxLengthFruit = list.maxByOrNull(lambda)
}
fun findMaxLength2(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val maxLengthFruit = list.maxByOrNull({fruit:String -> fruit.length})
}
//当lambda参数是函数的最后一个参数时，可以将lambda表达式移到函数括号外面
fun findMaxLength3(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val maxLengthFruit = list.maxByOrNull(){fruit:String -> fruit.length}
}
//如果lambda参数是函数的唯一一个参数的话，可以将函数的括号省略
fun findMaxLength4(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val maxLengthFruit = list.maxByOrNull(){fruit:String -> fruit.length}
    //由于kotlin出色的类型推导机制，lambda表达式的参数列表在大多数的情况下可以不必声明参数类型
    //val maxLengthFruit = list.maxByOrNull{fruit -> fruit.length}
}
//当lambda表达式的参数列表只有一个参数时，可以不必声明参数，使用it关键字代替
fun findMaxLength5(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val maxLengthFruit = list.maxByOrNull(){it.length}
}
```

#### 常用函数式API

##### Map

集合中的map函数，是最常用的一种函数式API，它将集合中每个元素都映射成一个另外的值，映射的规则在Lambda表达式中指定，最终生成一个新的集合。

```kotlin
fun mapTest(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val newList = list.map { it.toUpperCase() }
    for (fruit in newList){
        println(fruit)
    }
}
```

##### filter

另外一个比较常用的函数式API--filter函数用来过滤集合中的数据，可以单独使用，也可以和map函数一起使用

```kotlin
fun filterTest(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val newList = list.filter { it.length <=5 }.map { it.toUpperCase() }
    for (fruit in newList){
        println(fruit)
    }
}
```

保留5个字母以内的水果，借助filter函数实现

##### any

any函数用于判断集合中是否至少存在一个元素满足指定条件

##### all

all函数用于判断集合中是否所有的元素都满足指定条件。

```kotlin
fun anyAndAllTest() {
    val list = listOf<String>("Apple", "Banana", "Orange", "Pear", "Grape")
    val anyResult = list.any { it.length <= 5 }
    val allResult = list.all { it.length <= 5 }
    println("anyResult is $anyResult, allResult is $allResult")//true false
}
```

##### Java函数式API的使用

如果Kotlin代码中调用了一个Java方法，并且该方法接收一个Java单抽象方法接口参数，就可以使用函数式API。

```kotlin
Thread(object : Runnable {
    override fun run() {
        println("Runnable is running")
    }
}).start()
//简化
Thread(Runnable { 
        println("Runnable is running able")
    }).start()
//如果一个Java方法的参数列表有且仅有一个Java单抽象方法接口参数，可以将接口名省略
Thread{
        println("Thread is running the")
    }.start()

```

### 空指针检查



### 标准函数和静态方法

> Kotlin的标准函数指的是Standard.kt文件中定义的函数，任何Kotlin代码都可以自由地调用所 有的标准函数。

#### 标准函数let

配合?.操作符来进行 辅助判空处理

#### 标准函数with

> 接收两个参数：参1：任意类型 的对象，参2：Lambda表达式。with函数会在Lambda表达式中提供一个第一个参数对象的上下文，并使用lambda表达式的最后一行代码作为返回值返回。
>
> 作用：可以在连续调用同一个对象的多个方法时让代码变的更加精简。

```kotlin
fun resultWith(){
    val result = with(StringBuilder()){
        append("start eating fruits \n")
        for (s in list) {
            append(s).append("\n")
        }
        append("ate all fruits.")
        length
    }
    println(result)
    println(result.javaClass)
}
```

#### 标准函数run

>run函数通常不会直接调用，而要在某个对象的基础上调用；其次run函数只接收Lambda函数，并且在lambda表达式中提供调用对象的上下文。其他类型with，使用lambda表达式中的最后一行代码作为返回值。

```kotlin
fun resultRun(){
    val result = StringBuilder().run {
        append("start eating fruits \n")
        for (s in list) {
            append(s).append("\n")
        }
        append("ate all fruits.")
        length
    }
    println(result)
    println(result.javaClass)
}
```

#### 标准函数apply

> 类似run函数，但是无法指定返回值，会自动返回调用对象本身。

```kotlin
fun resultApply(){
    val result = StringBuilder().apply {
        append("start eating fruits \n")
        for (s in list) {
            append(s).append("\n")
        }
        append("ate all fruits.")
        length//不会返回长度，会返回list对象
    }
    println(result)
    println(result.javaClass)
}
```

### 定义静态方法

> 1. 单例类：会把类中的所有的方法全部变成类似静态方法的调用方式
> 2. 希望类中的某一个方法变成静态方法的调用方式。companion object，会在类的内部创建一个伴生类，一个类只会存在一个伴生类。
>
> 语法上模仿静态方法的调用，不是真正的静态方法。

真正意义的静态方法实现方式：

1. 注解

   ```kotlin
   class Util {
   
       fun doAction1(){
           println("do action 1")
       }
   
       companion object {
           fun doAction2(){
               println("do action 2")
           }
   
           @JvmStatic //JvmStatic注解 加在除二者以外的位置会报错
           fun doAction3(){
               println("do action 3")
               someThing()
           }
       }
   }
   ```

   可以给单例类或者companion object中的方法加上@JvmStatic注解，可以在java或kotlin中调用（doAction2不同通过静态方式在java中直接调用，doAction3可以）。其他位置会报错。

1. 顶层方法（没有定义任何方法的类）

   会将所有的方法定义成静态类。

#### 定义静态方法：

[类静态方法]()：在语法的形式上模仿了静态方法 的调用方式，实际上它们都不是真正的静态方法

1. 单例类：

   ```java
   /**
    * 使用单例类的写法会将整个类中的所有方法全部变成类似于静态方法的调用方式
    * */
   object OneTest {
       // doAction()方法并不是静态方法,单例类所带来的便利性
       fun doAction() {
           println("通过单例类来实现静态方法的调用")
       }
   
       fun doAction2() {
           println("单例类中所有的方法都会编程静态的方法")
       }
   }
   ```

   

2. `companion object`关键字

   ```java
   package com.example.langue.staicmethod
   
   class TwoTest {
       fun doAction1(){
           println("普通的方法")
       }
       /*companion object {
   
       }*/
       companion object {
           /**
            * 本质上不是静态方法，companion object关键字会在TwoTest类的内部
            * 创建一个伴生类，而都Action2方法是定义在这个伴生类里面的实例方法
            * Kotlin会保证TwoTest类只会存在一个伴生类对象
            * 但是伴生类里面可以存在多个方法
            * */
           fun doAction2() {
               println("通过companion object来实现单例类")
           }
   
           fun doAction3(){
               println("我也是伴生类对象的中的一个方法")
           }
       }
   }
   ```

[实际静态方法定义]()

1. 注解

   > @JvmStatic注解只能加在单例类或companion object中的方法

   通过JvmStatic注解修饰发方法，可以直接在Java代码中，通过类名.方法名，直接调用

   ```java
   package com.example.langue.staicmethod
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/10 9:47
    * Desc:
    */
   
   class RealStanderMethod {
       fun doAction1(){
           println("普通的方法")
       }
       companion object{
           /**
            * @JvmStatic注解只能加在单例类或companion object中的方法
            * */
           @JvmStatic
           fun doAction2(){
               println("真正的静态方法，可以在Java代码中直接类调用")
           }
       }
   }
   ```

   ```java
   package com.example.langue.staicmethod
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/10 9:54
    * Desc:
    */
   object RealStaticDemo {
   
       @JvmStatic
       fun doAction(){
           println("真正的静态方法")
       }
   }
   ```

   

2. 顶层方法

   > 创建kt文件，

   ```kotlin
   package com.example.langue.staicmethod
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/10 9:58
    * Desc: 顶层方法
    */
   
   /*在kt文件中定义的任何方法都会是顶层方法*/
   
   fun doSomething(){
       println("do somethings")
   }
   ```

   kotlin中调用：

   ```kotlin
   package com.example.langue.staicmethod
   
   fun main() {
       doSomething()
   }
   ```

   java中调用：

   ```java
   package com.example.langue.staicmethod;
   
   /**
    * Author: Zhangtao
    * Created on 2022/10/10 9:38
    * Desc:
    */
   public class StaticJavaTest {
       public static void main(String[] args) {
          //是Kotlin编译器会自动创建一个叫作TopMethodKt的Java类，doSomething()方法就是以静态方法的形式定义在TopMethodKt类里面的
           TopMethodKt.doSomething();
       }
   }
   ```

### 5、拓展函数与运算符重载

#### 拓展函数

```kotlin
/*相比于定义一个普通的函数，定义扩展函数只需要在函数名的前面加上一个ClassName.的语
法结构，就表示将该函数添加到指定类当中了。*/
/*fun ClassName.methodName(param1: Int, param2: Int): Int {
    return 0
}*/

/**向String新增拓展函数*/
fun String.lettersCount():Int{
    var count = 0
    for (char in this) {
        /*自动拥有了String实例的上下文。因此lettersCount()函数就不再需要接收一个字符串参数了，而是直接遍历this*/
        if (char.isLetter()){
            count++
        }
    }
    return count
}
```

#### 运算符重载

```kotlin
/* 重载加号运算符函数
class Obj {
    operator fun plus(obj: Obj):Obj{
        //处理相加的逻辑
    }
}*/

/**
 * 调用方式
 * val obj1 = Obj()
 * val obj2 = Obj()
 * val obj3 = obj1 + obj2*/
```

```kotlin
class Money(val value:Int) {
    /*通过运算符重载来实现Money对象来相加*/
    operator fun plus(money: Money):Money{
        /*当前Money对象的value和参数传入的Money对象的value相加，然后将得到的
和传给一个新的Money对象并将该对象返回*/
        val sum = value + money.value
        return Money(sum)
    }

    /*还可以进行多重重载*/
    operator fun plus(newValue:Int):Money{
        val sum = value + newValue
        return Money(sum)
    }
}
```

```kotlin
fun main(){
    val money1 = Money(5)
    val money2 = Money(10)
    //成obj1.plus(obj2)
    val money3 = money1 + money2
    val money4 = money2.plus(money1)
    val money5 = money2 +3
    println(money3.value)
    println(money5.value)
}
```

**`语法糖表达式和实际调用函数对照表`**

![image-20221013141836715](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221013141836715.png)

练习：对String字串拓展重复次数函数：

原来方式：

```kotlin
fun getRandomLengthString(str:String):String{
    val n = (1..20).random()
    val builder = StringBuilder()
    repeat(n){
        builder.append(str)
    }
    return builder.toString()
}
```

拓展String函数：

```kotlin
/**拓展重复次数函数*/
/*operator fun String.times(n:Int):String{
    val builder = StringBuilder()
    repeat(n){
        builder.append(this)
    }
    return builder.toString()
}*/

operator fun String.times(n:Int) = repeat(n)
```

拓展后实现String重复次数方式：

```kotlin
val string = "String" * (1..20).random()
```

### 6、高阶函数

#### 6.1定义高阶函数

> 如果一个函数接收另一个函数作为参数，或者返回值的类型是 另一个函数，那么该函数就称为高阶函数。

基本规则：

```kotlin
/**
   * 声明该函数接受什么参数，返回值的类型
   * -> 左边用来声明该函数接收什么参数，多个参数之间使用逗号隔开，如果不接受任何参数，使用一对空括号
   * -> 右边部分用来声明该函数的返回值是什么类型，没有返回值使用Unit，类似java中的void
  */
(String,Int) -> Unit

fun example(func: (String, Int) -> Unit) {
 func("hello", 123)
}
```

**测试：**

```kotlin
/*StringBuilder. 的语法结构:在函数类型的前面加上ClassName. 就表示这个函数类型是定义在哪个类当中的*/
fun num1AndNum2(num1: Int, num2: Int, operation: (Int, Int) -> Int): Int {
    return operation(num1, num2)
}
```

定义与其函数类型相匹配的函数：

```kotlin
/*这两个函数的参数声明和返回值声明都和num1AndNum2()函数中的函数类型参数是完全匹配的*/
fun plus(num1: Int,num2: Int):Int{
    return num1+num2
}

fun minus(num1: Int,num2: Int):Int{
    return num1 - num2
}
```

调用高阶函数：

```kotlin
fun main(){
    val num1 = 100
    val num2 =89
    /*函数引用方式:表示将plus()和minus()函数作为参数传递给num1AndNum2()函数*/
    val result = num1AndNum2(num1,num2,::plus)
    val result2 = num1AndNum2(num1,num2,::minus)
    println(result)
    println(result2)
}
```

Koltin还支持其他多种方式来调用高阶函数，如Lambda表达式、匿名函数、成员引用等。

通过lambda表达式来引用：

```kotlin
fun main(){
    //不通过定义函数，通过lambda表达式来实现
    val result3 = num1AndNum2(num1,num2){ n1,n2->
        n1+n2
    }
    println(result3)
    val result4 = num1AndNum2(num1,num2){n1,n2->
        n1 -n2
    }
    println(result4)
}
```

使用高阶函数模拟apply函数功能

```kotlin
/*StringBuilder. 的语法结构:在函数类型的前面加上ClassName. 就表示这个函数类型是定义在哪个类当中的*/
fun StringBuilder.build(block:StringBuilder.() -> Unit):StringBuilder{
    block()
    return this
}
```

调用：

```kotlin
fun main(){
    val list = listOf<String>("Apple","Banana","Orange","Pear","Grape")
    val result6 = StringBuilder().build {
        append("String eating fruits ,|n")
        for (s in list) {
            append(s).append("\n")
        }
        append("Alte all Fruits")
    }
    println(result6)
}
```

#### 6.2 内联函数

> Lambda表达式在底层被转换成了匿名类的实现方式，每调用一次Lambda表达式，都会创建一个新的匿名类实例，当然也会造成额外的内存和性能开销。
>
> Kotlin提供了内联函数的功能，它可以将使用Lambda表达式带来的运行时 开销完全消除

内联函数的用法：在定义高阶函数时加上inline关键字的声明即可

```kotlin
inline fun num1AndNum2(num1: Int, num2: Int, operation: (Int, Int) -> Int): Int {
    return operation(num1, num2)
}
```

工作原理：Kotlin编译器会将内联函数中的代码 在编译的时候自动替换到调用它的地方

 `第一步替换过程：`Kotlin编译器会将Lambda表达式中的代码替换到函数类型参数调用的地方

![image-20221017170426083](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221017170426083.png)

![image-20221017170202136](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221017170202136.png)

`第二步替换过程：`再将内联函数中的全部代码替换到函数调用的地方

![image-20221017170634148](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221017170634148.png)

最终的替换结果：

![image-20221017170930970](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221017170930970.png)

如果一个高阶函数接收了两个或者更多函数类型的参数，给函数加上inline关键字，会自动将所有引用的Lambda表达式全部进行内联，如果只想内联其中一个Lambda表达式，可以使用noinline关键字

```kotlin
inline fun inlineTest(block: () -> Unit, noinline block2: () -> Unit) {}
```

> 为什么Kotlin还要提供一个noinline关键字来排除内联功能呢？
>
> 这是因为内联的函数类型参数在编译的时候会被进行代码替换，因此它没有真正 的参数属性。非内联的函数类型参数可以自由地传递给其他任何函数，因为它就是一个真实的 参数，而内联的函数类型参数只允许传递给另外一个内联函数，这也是它最大的局限性。

内联函数和非内联函数还有一个重要的区别，那就是内联函数所引用的Lambda表达式 中是可以使用return关键字来进行函数返回的，而非内联函数只能进行局部返回。

```kotlin
package com.example.langue.advancedfunction

/**
 * Author: Zhangtao
 * Created on 2022/10/17 17:18
 * Desc:
 */

fun main(){
    println("main start")
    val str = ""
    printString(str){ s->
        println("lambda start")
        if (s.isEmpty()) return
        println(s)
        println("lambda end")
    }
    println("main end")
}

inline fun printString(str:String,block:(String)->Unit){
    println("printString begin")
    block(str)
    println("printString end")
}
```

执行结果:

```kotlin
main start
printString begin
lambda start
```

### 7 高阶函数的应用

#### 7.1 简化SharedPreferences用法

标准用法：

```kotlin
val editor = getSharedPreferences("data", Context.MODE_PRIVATE).edit()
editor.putString("name","Tom")
editor.putInt("age",28)
editor.putBoolean("married",false)
editor.apply()
```

使用高阶函数简化：

先建SharedPreferences.kt文件

```kotlin
fun SharedPreferences.open(block:SharedPreferences.Editor.() -> Unit) {
    val editor = edit()
    editor.block()
    editor.apply()
}
```

通过扩展函数的方式向SharedPreferences类中添加了一个open函数，并且它还接收一个函数类型的参数，因此open函数是一个高阶函数。由于open函数内拥有SharedPreferences的上下文，因此这里可以直接调用edit()方法来 获取SharedPreferences.Editor对象。另外open函数接收的是一个 SharedPreferences.Editor的函数类型参数，因此这里需要调用editor.block()对函数 类型参数进行调用，我们就可以在函数类型参数的具体实现中添加数据了。最后还要调用 editor.apply()方法来提交数据，从而完成数据存储操作。

```kotlin
getSharedPreferences("data", MODE_PRIVATE).open {
    putString("name","Tom")
    putInt("age",28)
    putBoolean("married",false)
}
```

> KTX库:edit函数
>
> ```kotlin
> getSharedPreferences("data", Context.MODE_PRIVATE).edit {
> 	putString("name", "Tom")
> 	putInt("age", 28)
> 	putBoolean("married", false)
> }
> ```
>
> 

#### 7.2 简化ContentValues的用法

标准写法：

```kotlin
val values1 = ContentValues().apply {
    // 开始组装第一条数据
    put("name","The Da Vinci Code")
    put("author","Dan Brown")
    put("pages",454)
    put("price",16.95)
}
```

先创建ContentValues.kt文件



```kotlin

/*在Kotlin中使用A to B这样的语法结构会创建一个Pair对象*/
/*
fun cvOf(vararg pair: Pair<String,Any?>):ContentValues{}*/
//vararg关键字 可变参数
// pair键值对类型，数据结构 Any 是kotlin中所有类的共同基类，相当于java中object，Any？表示可以空值
fun cvOf(vararg pairs: Pair<String,Any?>):ContentValues{
    val cv = ContentValues()
    for (pair in pairs) {
        val key = pair.first
        val value = pair.second
        /*使用了Kotlin中的Smart Cast功能。比如when语句进入Int条件分支后，这个条件下面的value会被自动转换成Int类型，而不再是Any?类型，这样我们就不需要像Java中那
样再额外进行一次向下转型了，这个功能在if语句中也同样适用*/
        when(value){
            is Int -> cv.put(key,value)
            is Long -> cv.put(key,value)
            is Short -> cv.put(key,value)
            is Float -> cv.put(key,value)
            is Double -> cv.put(key,value)
            is Boolean -> cv.put(key,value)
            is String -> cv.put(key,value)
            is Byte -> cv.put(key,value)
            is ByteArray -> cv.put(key,value)
            null -> cv.putNull(key)
        }
    }
    return cv
}
```



```kotlin
fun cvOf(vararg pairs: Pair<String,Any?>)=ContentValues().apply {  
    for (pair in pairs) {
        val key = pair.first
        /*使用了Kotlin中的Smart Cast功能。比如when语句进入Int条件分支后，这个条件下面的value会被自动转换成Int类型，而不再是Any?类型，这样我们就不需要像Java中那
样再额外进行一次向下转型了，这个功能在if语句中也同样适用*/
        when(val value = pair.second){
            is Int -> put(key,value)
            is Long -> put(key,value)
            is Short -> put(key,value)
            is Float -> put(key,value)
            is Double -> put(key,value)
            is Boolean -> put(key,value)
            is String -> put(key,value)
            is Byte -> put(key,value)
            is ByteArray -> put(key,value)
            null -> putNull(key)
        }
    }
}
```

使用：

```kotlin
//使用高级函数简化
val values1 = cvOf("name" to "Game of Thrones","author" to "George Martin","pages" to 720,"price" to 20.85)
```

> KTX库：contentValuesOf()方法 

### 8、泛型和委托

#### 1、泛型

> 泛型。在一般的编程模式下，需要给任何一个变量指定一个具体的类型，而泛型允许在不指定具体类型的情况下进行编程，这样编写出来的代码将会拥有更好 的扩展性

**定义泛型：**

- 定义泛型类

  ```kotlin
  class MyClass<T> {
  	fun method(param: T): T {
   		return param
   	}
  }
  
  //调用
  val myClass = NyClass<Int>()
  val result = myClass.method(123)
  ```

- 定义泛型方法

  ```kotlin
  class MyClass{
      fun <T> method(param:T):T{
          return param
      }
  }
  
  //调用
  val myClass = MyClass()
  val result = myClass.method<Int>(123)
  ```

  > 类型推到机制，如果传入的是Int类型的参数，可以自动推导出泛型的类型是Int型
  >
  > ```kotlin
  > val myClass = MyClass()
  > val result = myClass.method(123)
  > ```

  限制泛型类型：

  如，只能将method方法的泛型指定成数字类型，如Int、Float、Double

  ```kotlin
  class MyClass{
      fun <T:Number> method(param:T):T{
          return param
      }
  }
  ```

  > 在默认情况下，所有的泛型都是可以指定成可空类型的，这是因为在不手动指定上界的 时候，泛型的上界默认是Any?。而如果想要让泛型的类型不可为空，只需要将泛型的上界手动 指定成Any

```kotlin
fun StringBuilder.build(block: StringBuilder.() -> Unit): StringBuilder {
 	block()
 	return this
}
```

改成适用于所有的函数

新建一个build.kt文件

```kotlin
fun <T> T.build(block:T.() -> Unit):T{
	block()
	return this
}
```

#### 2、类委托和委托属性

> 委托：操作对象自己不会去处理某段逻辑，而是会把工作委 托给另外一个辅助对象去处理。

Kotlin中也支持委托功能，分为两种：类委托和委托属性

##### 类委托：

操作对象自己不会去处理某段逻辑，而是会把工作委 托给另外一个辅助对象去处理。

> 如果只是让大部分的方法实现调用辅助对象中的方法，少部分的方法实现由自己来重写，甚至加入一些自己独有的方法，那么MySet就会成为一个全新的数据结构类，这就是委托模式的意义所在

```kotlin
/**
 * 接收HashSet参数，相当于辅助对象，通过辅助对象实现所有的方法。
 * */
class MySet<T>(private val helperSet:HashSet<T>):Set<T> {
    override val size: Int
        get() = helperSet.size

    override fun contains(element: T): Boolean {
        return helperSet.contains(element)
    }

    override fun containsAll(elements: Collection<T>): Boolean {
        return helperSet.containsAll(elements)
    }

    override fun isEmpty(): Boolean {
        return helperSet.isEmpty()
    }

    override fun iterator(): Iterator<T> {
        return helperSet.iterator()
    }
}
```

通过类委派简化，在接口声明的后面使用by关键字，再街上受委托的辅助对象，可以免去模板式代码。如果需要对某个方法进行重写，只需要单独写一个方法，其他方法仍然可以享受类委托的便捷。

```
//通过类委派简化
class MySet<T>(private val helperSet:HashSet<T>):Set<T> by helperSet{
    fun helloWorld() = println("Hello World")
    override fun isEmpty() = false
}
```

##### 委托属性

> 类委托的核心思想是将一个类的具体实现委托给另一个类去完成，而委托属性的核心思想是将 一个属性（字段）的具体实现委托给另一个类去完成。

```kotlin
class MyClass{
    var p by Delegate()//代表着将p属性的具体实现委托给了Delegate类去完成。
    //当调用p属性的时候会自动调用Delegate类的getValue()方法，当给p属性赋值的时候会自动调用Delegate类的setValue()方法。
}
```

标准的代码实现模板，在Delegate类中必须实现getValue()和setValue()这 两个方法，并且都要使用operator关键字进行声明。 getValue()方法要接收两个参数：

- 第一个参数用于声明该Delegate类的委托功能可以在什么 类中使用，这里写成MyClass表示仅可在MyClass类中使用；

- 第二个参数KProperty<*>是 Kotlin中的一个属性操作类，可用于获取各种属性相关的值，在当前场景下用不着，但是必须在 方法参数上进行声明。*

  另外，<*>这种泛型的写法表示不知道或者不关心泛型的具体类型，只 是为了通过语法编译而已，有点类似于Java中的写法。至于返回值可以声明成任何类型，根据具体的实现逻辑去写就行.

```kotlin
class Delegate {
    var propValue:Any? = null
    operator fun getValue(myClass: MyClass,prop:KProperty<*>):Any?{
        return propValue
    }
    operator fun setValue(myClass: MyClass,prop: KProperty<*>,value:Any?){
        propValue = value
    }
}
```

#### 3、lazy函数

> by lazy并不是连在一起的关键 字，只有by才是Kotlin中的关键字，lazy在这里只是一个高阶函数而已

```kotlin
class Later<T>(val block:() -> T) {
    var value:Any? = null
    operator fun getValue(any: Any?,prop:KProperty<*>):T{
        if (value == null) {
            value = block()
        }
        return value as T
    }
}
```

### 9、infix函数

> 限制：
>
> infix函数是`不能定义成顶层函数`的，它必须是某个类的成员函数，可以使用扩展函数的方式将它定义到某个类当中；
>
> 其次，`infix函数必须接收且只能接收一个参数`，至于参数类型是没有限制的。只有同时满足这两点，infix函数的语法糖才具备使用的条件

```kotlin
/**
 * String 的拓展函数
 * 加上infix关键字之后，beginsWith函数变成infix函数，除了传统的函数调用，可以用一种特殊的语法糖格式调用
 * if(”Hello Kotlin“ beginsWith "Hello"){
 *      //处理具体的逻辑
 *  }
 *  infix函数允许我们将函数调用时的小数点、括号等计算机相关的语法去掉，从而使用一种更接近英语的语法来编写程序
 *  */

infix fun String.beginsWith(prefix: String) = startsWith(prefix)
```

调用：

```kotlin
if("Hello Kotlin" beginsWith "Hello"){
    //处理具体的逻辑
}
```