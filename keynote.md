name: inverse
layout: true
class: center, middle, inverse
---
#用Kotlin开发Android
[Kotlin介绍]

.footnote[[林进森](http://www.kescoode.com) 2015年8月8日]
---
layout: false

# Kotlin，一门新的JVM语言

Kotlin，原意是在俄罗斯的一个[小岛][0]，JetBrain于2011年开始研发一种JVM语言，并以Kotlin来命名。Kotlin在语法结构上看似和Scala与苹果新出的Swift非常像，虽然是静态类型的语言，但是Kotlin也支持部分动态语言的特性。与Scala最大的不同在于，Kotlin设计之处就力求和Java保持最大兼容，所以Kotlin可以很轻松使用Java的类库，同时Java也可以很轻松引用Kotlin的代码，很方便的在同一个项目内混编。

目前，Kotlin的版本为0.12.613(M12)，还没发布正式版。但是已经受到很多大牛的关注，出了很多用Kotlin编写的Android库，而且Android M的SDK中Google也用Kotlin编写了Data Binding库，不过使用的Kotlin版本为M11。
---
template: inverse

## 用Java开发Android麻烦的地方
---
layout: false
.left-column[
## 1. 非空判断
]
.right-column[
Java中对象赋值有可能为空，容易对空对象操作导致出现NullPointException异常，挂掉程序，所以我们往往在操作对象时，先判断是否对象为空：

```java
if (jObj != null) {
    jObj.doOperation();
}
```
]
---
.left-column[
## 1. 非空判断
## 2. 冗余的Getter和Setter
]
.right-column[
作为一名Java程序员，我们都知道，字段（Filed）要声明成private，如果要读取或修改字段，就声明一些公开方法（Public Method），以get和set开头，像这段Java代码一样：

```java
public class User {
    private int id;
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    ...
}
```

显而易见的，但一个类的字段多起来之后，就要写很多相应的Setter和Getter。虽然现代IDE可以自动生成这些代码，但是多余的代码显得繁杂碍眼。
]
---
.left-column[
## 1. 非空判断
## 2. 冗余的Getter和Setter
]
.right-column[
然而，并不是所有的语言都要像Java那样要对Setter和Getter硬编码的。比如C#可以这样写：

```c#
class User {
    private int _id;
    private string _name;

    public string name {
        get { return _name; }
        set { _name = value; }
    }
}
```

这种方法，可以避免过多的Getter和Setter，减少冗余代码。
]
---
.left-column[
## 1. 非空判断
## 2. 冗余的Getter和Setter
## 3. 过多的工具类
]
.right-column[
Java是非常纯粹的面向对象语言(Java 8之前)，一切的操作都脱离不了对象，就算是静态方法也要定义在类里面，所以有时候会出现里面只包含静态方法的工具类，当工程量大起来后，工具类数量和静态方法数会非常多，而且过多的静态方法个人觉得并不符合Java的面向对象设计。
]
---
.left-column[
## 4. Android不支持Java 8
]
.right-column[
Java 8在2013年已经发布了，而过去了两年多时间里，Google在Android 5.0时才支持到Java 7的语言特性，而且新增的Java 7 API还不向下兼容旧版本。随着Google和Oracle对Java官司不断打口水战，估计Java 8的支持遥遥无期。这也意味着Java 8引入的Lambda表达式和接口的默认方法都无法在Android平台上使用。

对于Lambda表达式，虽然有第三方的Hack方法，比如[gradle-retrolambda][1]可以使用，但是产生的.class文件会与源码对不上，导致调试的时候很不方便，不推荐在生产环境上使用。
]
---
template: inverse

## Kotlin语法介绍
---
layout: false
.left-column[
## Kotlin语法
]
.right-column[
Kotlin的语法非常简洁，熟悉Java的开发者可以快速上手。而且JetBrain系的IDE上还可以一键把已有的Java代码转换成Kotlin代码(不得不说一键傻瓜式操作系列真的非常人性化=_=)，有点像Groovy，开发者可以一开始以Java的风格写Kotlin代码，然后慢慢转成Kotlin自己的风格。

.footnote[语法详细可参考[kotlin官网][2]]
]
---
.left-column[
## Kotlin语法
### 1. 声明对象
]
.right-column[
Kotlin的一切变量都是对象，所以没有Java那样的基本类型。Kotlin声明对象有两种类型：
1. 可变对象
```kotlin
var x = 5                       // 类型推断为`Int`型
var b: String = "Hello"         // String型
```
2. 不可变对象，Kotlin没有final关键字，而且不存在静态变量
```kotlin
val x = 5                       // 不可变`Int`型变量
x += 1                          // 编译器会报错
```

从代码片段可以看出，Kotlin不需要在每行结束加分号。
]
---
.left-column[
## Kotlin语法
### 1. 声明对象
### 2. 区分非空类型
]
.right-column[
在Kotlin中，nullable对象和nullable对象是严格区分的，甚至在编译期解决了不少潜在的空指针问题。声明变量时，对变量的类型都是默认非空的，如果要允许变量为空，必须在定义类型后面加个`?`：

```kotlin
var a: String = "hello"         // a字符串不可为空
var b: String? = "hello"        // b字符串可以为空
a = null                        // 编译器会报错
b = null                        // OK!!!
```

而且，在对有可能为空的对象进行操作时，编译器会提示Warning。同时，Kotlin提供类似Ruby和CoffeeScript的语法糖：

```kotlin
var b: String? = "hellp"
b?.length()                     // 如果b不为空对象，则取b的长度
```
]
---
.left-column[
## Kotlin语法
### 1. 声明对象
### 2. 区分非空类型
### 3. 智能类型转换
]
.right-column[
在Kotlin中，进行强制类型转换可以使用`as`关键字，但有可能会抛出异常：

```kotlin
if (c is String) {              // Kotlin使用`is`关键字判断对象类型
    c.length()
}
```

在上面的例子中，如果`c`是一个String对象，则在if块中，可以直接使用String的方法，编译器会智能的帮你识别出c在if-blcok里面是一个String对象。

Kotlin也提供一个"安全"的类型转换方式：
```kotlin
val d: String? = c as String?
/* 或者 */
val d: String? = c as? String
```
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
]
.right-column[
### `if`
Kotlin的`if`表达式与Java的一样，只是Kotlin中没有三目表达式，所以`if`和`else`可以这样写：

```kotlin
val a = 1
val b = 2
val max = if (a > b) a else b   // 类似Java的: int max = a > b?a:b;
```

### `when`
Kotlin用`When`表达式来替代Java的`Switch`：

```kotlin
when (x) {
  1 -> print("x == 1")
  2 -> print("x == 2")
  else -> { // Note the block
    print("x is neither 1 nor 2")
  }
}
```
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
]
.right-column[
### `for`

Kotlin的`for`表达式和Java的`foreach`一致。

```kotlin
for (i in array.indices)
  print(array[i])
```

### `while`

`while`表达式和Java的一样。

```kotlin
while (x > 0) {
  x--
}

do {
  val y = retrieveData()
} while (y != null)     // 与Java不同
```
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
]
.right-column[
与Java不同的是，Kotlin的函数是一等成员，不需要在类内定义，是可以脱离类存在的，而且Kotlin是不支持类静态方法的。

```kotlin
fun hello():Unit {      
    print("hello")
}

fun add(a: Int, b: Int):Int{
    return a + b
}
```

Kotlin没有void关键字，函数都是要返回对象的，所以如果没东西返回的时候，函数后要声明Unit类型(M10版本之后就默认不需要了)。

如果函数比较简单可以放在一行的话，甚至可以这样：

```kotlin
fun add(a: Int, b: Int) = a + b
```

在这种情况下，函数默认返回最后计算的结果。
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
]
.right-column[
### 扩展类的函数

通常开发中，我们往往要对提供的API类进行扩展，增加一些方法，如果是Java的话，要想这样做，则声明一个继承该API的子类。Kotlin采取了C#的办法，可以直接扩展类的方法：

```
fun Fragment.findViewById(id: Int) = this.getView.findViewById(id)
```

从而不需要衍生出一堆子类或者辅助工具类。
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
]
.right-column[
那么问题来了，如果扩展的类里面本来就有这个同名方法，但类对象调用这个同名方法的时会出现什么情况呢？答案是： 如果类里面就有这个方法，Kotlin就会调用原来的方法，而不调用扩展方法。

利用这个特性，Kotlin的扩展函数可以提供旧版本API兼容。比如自Android API 16之后，View提供了`setBackground`方法，原来的`setBackgroundDrawable`则被标记为过时的了，如果要在旧Android手机上使用该API，我们可以这样写：

```kotlin
fun View.setBackground(background: Drawable) = this.setBackgroundDrawable(background:)
```

这样，在旧的手机上，APP就可以用自定义的`setBackground`的Wrapper，而在高版本的手机上APP会调用原生的`setBackground`方法。
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
]
.right-column[
### Lambda表达式

Kotlin引入了Lambda表达式，而且Kotlin的Lambda表达式支持Android平台：

```kotlin
view.setOnClickListener({ toast("Hello world!") })
```

这样，我们就可以不用写那么多监听器对象了。
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
### 6. 类
]
.right-column[
Kotlin的类是这样声明的：

```kotlin
class User(val id: Int,val name: String) { // 只有一个构造方法是，可以这样声明
    init {
        print("Constructor $id : $name")
    }
    // Nothing   
}

val user = User(1, "Kesco")
```

从上面代码片段可以看出，Kotlin在调用构建方法时，会先调用`init`代码块内的代码，而且构建类对象的时候，是不需要`new`关键字的。
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
### 6. 类
]
.right-column[
那么，如果有多个构造方法怎么办呢？在Kotlin的类内，构造方法名都是规定为`constructor`的：

```kotlin
class User { // 只有一个构造方法是，可以这样声明
    var _id: Int = 0
    var _name: String = ""

    constructor(id: Int, name: String) {
        _id = id
        _name = name
    }

    constructor(name: String): this(0, name) {
    }

    init {
        print("Constructor $id : $name")
    }
    // Nothing   
}

val user = User(1, "Kesco")
```
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
### 6. 类
]
.right-column[
Kotlin的类默认是final的，也就是不可继承，如果让类可继承，则要带有`open`关键字声明：
```kotlin
open class User(val id: Int, val name: String) {
    // Nothing
}
```

虚类Kotlin与Java一样，都是用`abstract`关键字声明，当有`abstract`关键字的时候，就不需要带有`open`了：

```kotlin
abstract class User() {
    // Nothing
}
```
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
### 6. 类
]
.right-column[
### Getter和Setter

Kotlin的Setter和Getter编码风格与C#类似：

```kotlin
class User {
    private var _id: Int
    var id: Int
        get() = _id
        set(value) {
            _id = value
        }
}
```
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
### 6. 类
]
.right-column[
### Data Class

Kotlin的类可以申明`data`关键字，相当与专用与存储数据的Pojo类：

```kotlin
data class User(val id: Int = 0, val name: String = "")
```

而且Data Class可以进行这样的操作：

```kotlin
val jane = User(1, "Jane")
val (id, name) = jane
println("$name id is $id")
```
]
---
.left-column[
## Kotlin语法
### 4. 流程控制
### 5. 函数
### 6. 类
]
.right-column[
### 内部类

Kotlin同样支持内部类，但是Kotlin的内部类是默认不带有外部类的引用的，也就是说默认的Kotlin内部类都是静态的。要想内部类带有外部类的引用，要在内部类声明上加入`inner`关键字：

```kotlin
class User(val id:Int, val name: String) {
    inner class School(val name: String) {
        // Nothing
    }
}
```
]
---
.left-column[
## Kotlin语法
### 7. 接口
]
.right-column[
Kotlin的接口和Java的类似，而且还支持Java 8的默认方法：

```kotlin
interface MyInterface {
    fun bar()
    fun foo() {
        print("foo")
    }
}
```
]
---
template: inverse

## 如何在现有Android项目中引入Kotlin
---
layout: false

在Android项目中使用Kotlin非常简单，而且Kotlin可以和Java混编，所以完全部分功能用Kotlin开发，部分功能用Java开发。

首先，确保Android Studio或者Intellij Idea安装了Kotlin插件。

然后，在项目Module的`build.gradle`上声明：

```groovy
apply plugin: 'kotlin-android'
```

接着，添加Kotlin依赖：

```groovy
dependencies {
    compile 'org.jetbrains.kotlin:kotlin-stdlib:0.1-SNAPSHOT'
}
```

最后，添加Kotlin源码文件夹即可：

```groovy
android {

    ...

    sourceSets {
        main.java.srcDirs += 'src/main/kotlin'
    }
}
```
---
template: inverse

# The End #
Thank you !


[0]: https://zh.wikipedia.org/wiki/%E7%A7%91%E7%89%B9%E6%9E%97%E5%B3%B6
[1]: https://github.com/evant/gradle-retrolambda
[2]: http://kotlinlang.org/docs/reference/
