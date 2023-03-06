# Kotlin

![image-20230220135328284](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220135328284.png)

```kotlin
fun main() {
  println("hello world")
}

fun main(args: Array<String>) {
  print("Hello")
  println(args.contentToString())
}
```

## Functions

```kotlin
fun sum(a: Int, b: Int): Int {
  return a + b;
}

fun sum(a: Int, b: Int) = a + b
```

## Variables

```kotlin
val: 常量
var: 变量
```

![image-20230220141646669](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220141646669.png)

![image-20230220142009526](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220142009526.png)

![image-20230220143702273](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220143702273.png)

## When 表达式

![image-20230220145439246](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220145439246.png)

![image-20230220145739337](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220145739337.png)



## 类和对象

https://www.runoob.com/kotlin/kotlin-class-object.html

![image-20230220150346882](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220150346882.png)



![image-20230220154240105](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220154240105.png)



# 重写 Overide

1. 基类中使用 fun 声明函数，此函数默认为 final 修饰，不能被子类重写。
2. 基类函数添加 open 修饰，允许子类重写该函数。
3. 子类重写方法 fun，使用 override 关键字。

![image-20230220155729680](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220155729680.png)



## 属性重写

![image-20230220165014460](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220165014460.png)



## 接口

![image-20230220165446149](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230220165446149.png)





函数

```kotlin
fun sum(a: Int, b Int): Int{
  return a + b
}

fun sum(a: Int, b: Int) = a + b
```

lambda(匿名函数)

```kotlin
fun main(args: Array<String>) {
    val sumLambda: (Int, Int) -> Int = {x,y -> x+y}
    println(sumLambda(1,2))  // 输出 3
}
```

常量 val & 变量 var

```kotlin
val x = 10
var y = 9
```

字符串模板

```kotlin
var a = 1
val s1 = "a is $a"
a = 2
val s2 = "${s1.replace("is", "was")}, but now is $a"
```

NULL 检查机制

通过放置一个`?`来声明可空类型`?`

```kotlin
字段后加?可不做处理返回值为 null 或配合 ?: 做空判断处理
// 类型后面加 ? 表示可为空
var age: String? = "23"

fun parseInt(str: String): Int? {
	return Integer.parseInt(str)
}
```

类型检测和自动转化

```kotlin
/**
 * 使用 is 运算符检测一个表达式是否某类型的一个实例(类似于Java中的instanceof关键字)。
 * `is` operator
 * The `is` operator checks if an expression is an instance of a type.
 * If an immutable local variable or property is checked for a specific type, there's no need to cast it explicitly:
 */
    
    fun getStringLength(obj: Any): Int? {
        if (obj is String) {
            // `obj` is automatically cast to `String` in this branch
            return obj.length;
        }

        // `obj` is still of type `Any` outside of the type-checked branch
        return null
    }
```

区间

```kotlin
for(i in 1..4) print(i)

if(i in 1..10){
  // 等同于 i >= 1 && i <= 10
  println(i)
}
```

基本数据类型

![image-20230221154804307](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230221154804307.png)

比较两个数字

Kotlin 中没有基础的数据类型，只有封装的数字类型，定义变量的时候，Kotlin帮你封装了一个对象，这样保证不会出现空指针。

Kotlin中：`===` 三个等号表示比较**对象地址**；`==` 表示比较两个**值的大小**。

```kotlin
fun main(args: Array<String>) {
    val a: Int = 10000
    println(a === a) // true，值相等，对象地址相等

    //经过了装箱，创建了两个不同的对象
    val boxedA: Int? = a
    val anotherBoxedA: Int? = a

    //虽然经过了装箱，但是值是相等的，都是10000
    println(boxedA === anotherBoxedA) //  false，值相等，对象地址不一样
    println(boxedA == anotherBoxedA) // true，值相等
}
```

类型转换

```kotlin
推荐使用 toXxx() 方法
```

![image-20230221162225476](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230221162225476.png)

位操作

![image-20230221162324653](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230221162324653.png)

字符

布尔

数组

```kotlin
数组用类 Array 实现，并且还有一个 size 属性及 get 和 set 方法，由于使用 [] 重载了 get 和 set 方法，所以我们可以通过下标很方便的获取或者设置数组对应位置的值。
[] 运算符代表调用成员函数 get() 和 set()。
```

For循环

```kot
for 循环可以对任何提供迭代器（iterator）的对象进行遍历
```

类的属性

![image-20230221200336469](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230221200336469.png)

Kotlin 类有一个主构造器，一个或多个次构造器，主构造器是类头部的一部分，位于类名之后

![image-20230221200606701](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230221200606701.png)

```kotlin
class Person constructor(firstName: String) {}
// 如果主构造器没有任何注解，也没有任何修饰符，constructor 关键字可以删除
class Person(firstName: String) {}
```

getter() 和 setter() 完整语法

```kotlin
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

主构造器中不能包含任何代码，初始化代码可以放在初始化代码段中，初始化代码段使用 init 关键字作为前缀。

```kotlin
class Person constructor(firstName: String) {
    init {
        println("FirstName is $firstName")
    }
}
```

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    var age: Int, // trailing comma
) { /*...*/ }
```



次级构造函数

```kotlin
类也可以有二级构造函数，需要加前缀 constructor:
class Person {
  constructor(parent: Person){
    parent.children.add(this)
  }
}

如果类有主构造函数，每个次构造函数都要，或直接或间接通过另一个次构造函数代理主构造函数。
在同一个类中代理另一个构造函数使用 this 关键字：
class Person(val name: String) {
    constructor (name: String, age:Int) : this(name) {
        // 初始化...
    }
}
```



## Any

**All classes in Kotlin have a common superclass, `Any`**



抽象类

```kotlin
abstract class Test{  //抽象类
    abstract fun fun22()  //抽象方法，必须被子类重写
    open fun fun33(){}  //可被子类重写
    fun fun44(){}  //不可被子类重写 final类型
}
```

嵌套类

```kotlin
// 把类嵌套在其他类中
class Outer {                  // 外部类
    private val bar: Int = 1
    class Nested {             // 嵌套类
        fun foo() = 2
    }
}
```

内部类

```kotlin
// 内部类使用 inner 关键字来表示
// 内部类会带有一个对外部类的对象的引用，所以内部类可以访问外部类成员属性和成员函数
class Outer {
    private val bar: Int = 1
    var v = "成员属性"
    /**嵌套内部类**/
    inner class Inner {
        fun foo() = bar  // 访问外部类成员
        fun innerTest() {
            var o = this@Outer //获取外部类的成员变量 this@Outer
            println("内部类可以引用外部类的成员，例如：" + o.v)
        }
    }
}
```

类的修饰符

![image-20230221204213985](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230221204213985.png)

## 继承: common superclass `Any`

Kotlin 中默认`类&方法`都是`final`的，因此默认无法被子类继承或重写，但可通过 `open` 关键字来解除

Kotlin classes are `final` - they can't be inherited. Mark it with `open` keyword. 

```kotlin
All classes in Kotlin have a common superclass, Any, which is the default superclass for a class with no supertypes declared:

Any has three methods: `equals(), hashCode(), and toString()`. Thus, these methods are defined for all Kotlin classes.

By default, Kotlin classes are final – they can't be inherited. To make a class inheritable, mark it with the open keyword:
```

To declare an explicit supertype, place the type after a colon in the class header.

```kotlin
open classl Base(p: Int)
class Derived(p: Int): Base(p)
```

If the derived class has a `primary constructor`, the base class **can (and must)** be initialized in that primary constructor according to its parameters.

```
If there is no `open` modifier on a function, like Shape.fill(), declaring a method with the same signature in a subclass is not allowed, either with override or without it.
```



## Override rules 重写规则

In Kotlin, implementation inheritance is regulated by the following rule: 

1. if a class inherits multiple implementations of the same member from its immediate superclasses, it must override this member and provide its own implementation (perhaps, using one of the inherited ones).
2. To denote the supertype from which the inherited implementation is taken, use `super` qualified by the supertype name in angle brackets, such as `super<Base>`:

```kotlin
open class Rectangle {
    open fun draw() { /* ... */ }
}

interface Polygon {
    fun draw() { /* ... */ } // interface members are 'open' by default
}

class Square() : Rectangle(), Polygon {
    // The compiler requires draw() to be overridden:
    override fun draw() {
        super<Rectangle>.draw() // call to Rectangle.draw()
        super<Polygon>.draw() // call to Polygon.draw()
    }
}
```

## 接口 Interface

与 Java 类似，使用 interface 关键字定义接口，允许方法有默认实现

```kotlin
interface MyInterface {
    fun bar()    // 未实现
    fun foo() {  //已实现
      // 可选的方法体
      println("foo")
    }
}
```

接口中的属性只能是抽象的，不允许初始化，接口不会保存属性值，实现接口时需要重写属性

## 扩展

扩展一个类的新功能而无需继承该类或者使用像装饰者这样的设计模式

```kotlin
例如，可以为一个不能修改的、来自第三方库中的类编写一个新的函数。 这个新增的函数就像那个原始类本来就有的函数一样，可以用普通的方法调用。 这种机制称为 扩展函数 。此外，也有 扩展属性 ， 允许你为一个已经存在的类添加新的属性。
```

```kotlin
声明一个扩展函数，我们需要用一个 接收者类型 也就是被扩展的类型来作为他的前缀。 
下面代码为 MutableList<Int> 添加一个swap 函数：

fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // “this”对应该列表
    this[index1] = this[index2]
    this[index2] = tmp
}
```

如果一个类定义有一个成员函数与一个扩展函数，而这两个函数又有相同的接收者类型、 相同的名字，并且都适用给定的参数，这种情况**总是取成员函数**。

```kotlin
class Example {
    fun printFunctionType() { println("Class method") }
}

fun Example.printFunctionType() { println("Extension function") }

Example().printFunctionType()
```

扩展属性

```kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```



## 数据类

只保存数据，标记为 `data`

```kotlin
data class User(val name: String, val age: Int)
```

复制

需要复制一个对象改变它的一些属性，其余部分保持不变。`copy()`函数就是为此而生。

```kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

数据类与结构声明

```kotlin
val jane = User("Jane", 20)
val (name, age) = jane
println("$name, $age years of age")
```



## 密封类 Sealed Classes

[Sealed classes](https://kotlinlang.org/docs/reference/sealed-classes.html) let you **restrict the use of inheritance**. Once you declare a class sealed, it **can only be subclassed from inside the same package** where the sealed class is declared. It cannot be subclassed outside of the package where the sealed class is declared.

密封类：限制继承的使用。声明密封类之后，只能在生命密封类的同一个包中被继承。



## 枚举类

```kotlin
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```

```kotlin
enum class Color(val rgb: Int) {
        RED(0xFF0000),
        GREEN(0x00FF00),
        BLUE(0x0000FF)
}
```



## 嵌套类

类嵌套在其他类中

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```



## 外部类

标记为 `inner` 的嵌套类能够访问其外部类的成员。内部类会带有一个对外部类的对象的引用：

A nested class marked as `inner` can access the members of its outer class. Inner classes carry a reference to an object of an outer class:

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer().Nested().foo() // == 2
```



## 泛型

Classes in Kotlin can have type parameters, just like in Java

```kotlin
class Box<T>(t: T) {
    var value = t
}
```

1. 创建这个类的实例，需要提供类型参数；
2. 但是如果参数可以推断出，可以省略参数类型。

```kotlin
val box: Box<Int> = Box<Int>(1)
val box = Box(1) // 1 具有类型 Int，所以编译器知道我们说的是 Box<Int>
```

型变



## 函数

Kotlin 中的函数使用 `fun` 关键字声明

**参数**：函数参数使用 Pascal 表示法定义，即 *name*: *type*。参数用逗号隔开。 

*每个参数必须有显式类型

```kotlin
fun powerOf(number: Int, exponent: Int): Int { /*……*/ }
```

Lambda

表达式完整语法：

```kotlin
val sum: (Int, Int) -> Int = { x: Int, y: Int -> x + y }
```





```shell
https://docs.google.com/document/d/10BM_pGDkfDtohzGXJSdYGKfzZxezJCmGlz8EMv-Rua8/edit
```















