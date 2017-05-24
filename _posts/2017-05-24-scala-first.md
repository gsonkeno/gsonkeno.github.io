---
layout: post
title:  "scala基础入门学习"
categories: Scala
tags:  scala
author: gaos
---

* content
{:toc}

scala基础入门学习




## 基础语法
- 程序入口
```
def main(args: Array[String]){
    //dosomething()
}
```
Scala程序从main()方法开始处理，这是每一个Scala程序的强制程序入口部分。
- 引用
```
import java.awt.Color  // 引入Color
 
import java.awt._  // 引入包内所有成员

import java.awt.{Color, Font} //引用包内的几个成员
```

## 变量与常量
- 变量
声明变量
>var s : String = "hello"
>var s : String //声明变量不一定需要初始值 

- 常量
声明常量
>val c : String = "hello"
>val c : String //声明常量不一定需要初始值 

## scala函数
- 函数声明
```
def functionName ([参数列表]) : [return type]
```
如果你不写等于号和方法主体，那么方法会被隐式声明为"抽象(abstract)"，包含它的类型于是也是一个抽象类型。

- 函数定义
```
def functionName ([参数列表]) : [return type] = {
   function body
   return [expr]
}
```

示例1
```
object add{
   def addInt( a:Int, b:Int ) : Int = {
      var sum:Int = 0
      sum = a + b

      return sum
   }
}
```
示例2，如果函数没有返回值，可以返回为 Unit，这个类似于 Java 的 void：
```
object Hello{
   def printMe( ) : Unit = {
      println("Hello, Scala!")
   }
}
```

###scala函数传名调用与传值调用
语法上：
```
object Add {  
  def addByName(a: Int, b: => Int) = a + b   
  def addByValue(a: Int, b: Int) = a + b   
}  
```
传名调用时**参数与类型之间有一个=>符号**，传值调用时没有此符号。具体使用区别不再赘述。

###指定函数参数名
```
一般情况下函数调用参数，就按照函数定义时的参数顺序一个个传递。但是我们也可以通过指定函数参数名，并且不需要按照顺序向函数传递参数，实例如下：
object Test {
   def main(args: Array[String]) {
        printInt(b=5, a=7);
   }
   def printInt( a:Int, b:Int ) = {
      println("Value of a : " + a );
      println("Value of b : " + b );
   }
}
```
###函数可变参数
Scala 允许你指明函数的最后一个参数可以是重复的，即我们不需要指定函数参数的个数，可以向函数传入可变长度参数列表。

Scala 通过在参数的类型之后放一个**星号**来设置可变参数(可重复的参数)。例如：
```
object Test {
   def main(args: Array[String]) {
        printStrings("Runoob", "Scala", "Python");
   }
   def printStrings( args:String* ) = {
      var i : Int = 0;
      for( arg <- args ){
         println("Arg value[" + i + "] = " + arg );
         i = i + 1;
      }
   }
}
```
###递归函数
递归函数在函数式编程的语言中起着重要的作用。Scala 同样支持递归函数。

递归函数意味着函数可以调用它本身。以下实例使用递归函数来计算阶乘：
```
object Test {
   def main(args: Array[String]) {
      for (i <- 1 to 10)
         println(i + " 的阶乘为: = " + factorial(i) )
   }
   
   def factorial(n: BigInt): BigInt = {  
      if (n <= 1)
         1  
      else    
      n * factorial(n - 1)
   }
}
```
###默认参数值
Scala 可以**为函数参数指定默认参数值**，使用了默认参数，你在调用函数的过程中可以不需要传递参数，这时函数就会调用它的默认参数值，如果传递了参数，则传递值会取代默认值。实例如下：
```
object Test {
   def main(args: Array[String]) {
        println( "返回值 : " + addInt() );
   }
   def addInt( a:Int=5, b:Int=7 ) : Int = {
      var sum:Int = 0
      sum = a + b

      return sum
   }
}
```
###高阶函数
高阶函数（Higher-Order Function）就是操作其他函数的函数。Scala中允许使用高阶函数, 高阶函数可以使用其他函数作为参数，或者使用函数作为输出结果。

以下实例中，apply() 函数使用了另外一个函数 f 和 值 v 作为参数，而函数 f 又调用了参数 v：
```
object Test {
   def main(args: Array[String]) {

      println( apply( layout, 10) )

   }
   // 函数 f 和 值 v 作为参数，而函数 f 又调用了参数 v
   def apply(f: Int => String, v: Int) = f(v)

   def layout[A](x: A) = "[" + x.toString() + "]"
   
}
```
###函数嵌套
```
我们可以在 Scala 函数内定义函数，定义在函数内的函数称之为局部函数。
以下实例我们实现阶乘运算，并使用内嵌函数：
object Test {
   def main(args: Array[String]) {
      println( factorial(0) )
      println( factorial(1) )
      println( factorial(2) )
      println( factorial(3) )
   }

   def factorial(i: Int): Int = {
      def fact(i: Int, accumulator: Int): Int = {
         if (i <= 1)
            accumulator
         else
            fact(i - 1, i * accumulator)
      }
      fact(i, 1)
   }
}
```
###匿名函数
Scala 中定义匿名函数的语法很简单，箭头左边是参数列表，右边是函数体。
使用匿名函数后，我们的代码变得更简洁了。

下面的表达式就**定义了一个接受一个Int类型输入参数的匿名函数**:
```
var inc = (x:Int) => x+1
```
###偏应用函数
Scala 偏应用函数是一种表达式，你不需要提供函数需要的所有参数，只需要提供部分，或不提供所需参数:
```
import java.util.Date

object Test {
   def main(args: Array[String]) {
      val date = new Date
      val logWithDateBound = log(date, _ : String)

      logWithDateBound("message1" )
      Thread.sleep(1000)
      logWithDateBound("message2" )
      Thread.sleep(1000)
      logWithDateBound("message3" )
   }

   def log(date: Date, message: String)  = {
     println(date + "----" + message)
   }
}
```
###函数柯里化
柯里化(Currying)指的是将原来接受两个参数的函数变成新的接受一个参数的函数的过程。新的函数返回一个以原有第二个参数为参数的函数。
```
object Test {
   def main(args: Array[String]) {
      val str1:String = "Hello, "
      val str2:String = "Scala!"
      println( "str1 + str2 = " +  strcat(str1)(str2) )
   }

   def strcat(s1: String)(s2: String) = {
      s1 + s2
   }
}
```
##闭包
scala的闭包概念与javascript的类似，**闭包是一个函数，返回值依赖于声明在函数外部的一个或多个变量。闭包通常来讲可以简单的认为是可以访问一个函数里面局部变量的另外一个函数。**
```
object Test {  
   def main(args: Array[String]) {  
      println( "muliplier(1) value = " +  multiplier(1) )  
      println( "muliplier(2) value = " +  multiplier(2) )  
   }  
   var factor = 3  
   val multiplier = (i:Int) => i * factor  
}  
```
##数组
声明数组
```
var z:Array[String] = new Array[String](3)

var z = new Array[String](3)

var z = Array("Runoob", "Baidu", "Google")

```
##scala元组
与列表一样，元组也是不可变的，但与列表不同的是元组可以包含不同类型的元素。
元组的值是通过将单个的值包含在圆括号中构成的。例如：
```
val t = (1, 3.14, "Fred")  

val t = (4,3,2,1)
```

- 访问
访问格式`变量名称._编号`,编号从一开始计数,如下
```
t._1  //访问元组t的第一个元素
```
- 迭代元组
你可以使用 `Tuple.productIterator()` 方法来迭代输出元组的所有元素：
```
object Test {
   def main(args: Array[String]) {
      val t = (4,3,2,1)
      
      t.productIterator.foreach{ i =>println("Value = " + i )}
   }
}
```
- 元组转为字符串
```
object Test {
   def main(args: Array[String]) {
      val t = new Tuple3(1, "hello", Console)
      
      println("连接后的字符串为: " + t.toString() )
   }
}

//结果:连接后的字符串为: (1,hello,scala.Console$@4dd8dc3)
```
##scala迭代器
**Scala Iterator（迭代器）不是一个集合，它是一种用于访问集合的方法。**

**迭代器 it 的两个基本操作是 next 和 hasNext。**

调用 it.next() 会返回迭代器的下一个元素，并且更新迭代器的状态。

调用 it.hasNext() 用于检测集合中是否还有元素。

调用it.max用于从迭代器中寻找最大值，相反的，调用it.min用于从迭代器中寻找最小值。

让迭代器 it 逐个返回所有元素最简单的方法是使用 while 循环：
```
object Test {
   def main(args: Array[String]) {
      val it = Iterator("Baidu", "Google", "Runoob", "Taobao")
      
      while (it.hasNext){
         println(it.next())
      }
   }
}
```
##类和对象
**Scala中的类不声明为public，一个Scala源文件中可以有多个类**

**Scala 的类定义可以有参数，称为类参数,类参数在整个类中都可以访问。**
```
import java.io._

class Point(xc: Int, yc: Int) {
   var x: Int = xc
   var y: Int = yc

   def move(dx: Int, dy: Int) {
      x = x + dx
      y = y + dy
      println ("x 的坐标点: " + x);
      println ("y 的坐标点: " + y);
   }
}

object Test {
   def main(args: Array[String]) {
      val pt = new Point(10, 20);

      // 移到一个新的位置
      pt.move(10, 10);
   }
}
```
###继承
Scala继承一个基类跟Java很相似, 但我们需要注意以下几点：

- 重写一个非抽象方法必须使用override修饰符。
- 只有主构造函数才可以往基类的构造函数里写参数。
- 在子类中重写超类的抽象方法时，你不需要使用override关键字。
```
class Point(xc: Int, yc: Int) {
   var x: Int = xc
   var y: Int = yc

   def move(dx: Int, dy: Int) {
      x = x + dx
      y = y + dy
      println ("x 的坐标点: " + x);
      println ("y 的坐标点: " + y);
   }
}

class Location(override val xc: Int, override val yc: Int,
   val zc :Int) extends Point(xc, yc){
   var z: Int = zc

   def move(dx: Int, dy: Int, dz: Int) {
      x = x + dx
      y = y + dy
      z = z + dz
      println ("x 的坐标点 : " + x);
      println ("y 的坐标点 : " + y);
      println ("z 的坐标点 : " + z);
   }
}
```
###单例对象
在 Scala 中，是没有 static 这个东西的，但是它也为我们提供了单例模式的实现方法，那就是使用`关键字 object`。
Scala 中使用单例模式时，除了定义的类之外，还要定义一个同名的 object 对象，它和类的区别是，object对象不能带参数。
当单例对象与某个类共享同一个名称时，他被称作是这个类的`伴生对象`：companion object。你必须在同一个源文件里定义类和它的伴生对象。类被称为是这个单例对象的`伴生类`：companion class。类和它的伴生对象可以互相访问其私有成员
```
// 私有构造方法
class Marker private(val color:String) {

  println("创建" + this)
  
  override def toString(): String = "颜色标记："+ color
  
}

// 伴生对象，与类共享名字，可以访问类的私有属性和方法
object Marker{
  
    private val markers: Map[String, Marker] = Map(
      "red" -> new Marker("red"),
      "blue" -> new Marker("blue"),
      "green" -> new Marker("green")
    )
    
    def apply(color:String) = {
      if(markers.contains(color)) markers(color) else null
    }
  
    
    def getMarker(color:String) = { 
      if(markers.contains(color)) markers(color) else null
    }
    def main(args: Array[String]) { 
        println(Marker("red"))  
        // 单例函数调用，省略了.(点)符号  
		println(Marker getMarker "blue")  
    }
}

创建颜色标记：red
创建颜色标记：blue
创建颜色标记：green
颜色标记：red
颜色标记：blue
```
###样例类
使用了case关键字的类定义就是就是样例类(case classes)，样例类是种特殊的类，经过优化以用于模式匹配。

以下是样例类的简单实例:
```
object Test {
   def main(args: Array[String]) {
   	val alice = new Person("Alice", 25)
	val bob = new Person("Bob", 32)
   	val charlie = new Person("Charlie", 32)
   
    for (person <- List(alice, bob, charlie)) {
    	person match {
            case Person("Alice", 25) => println("Hi Alice!")
            case Person("Bob", 32) => println("Hi Bob!")
            case Person(name, age) =>
               println("Age: " + age + " year, name: " + name + "?")
         }
      }
   }
   // 样例类
   case class Person(name: String, age: Int)
}
```
##scala特征
- Scala Trait(特征) 相当于 Java 的接口，实际上它比接口还功能强大。
与接口不同的是，**它还可以定义属性和方法的实现**。

##模式匹配
Scala 提供了强大的模式匹配机制，应用也非常广泛。

一个模式匹配包含了一系列备选项，每个都开始于关键字 **case**。每个备选项都包含了一个模式及一到多个表达式。**箭头符号 =>** 隔开了模式和表达式。
```
object Test {
   def main(args: Array[String]) {
      println(matchTest(3))

   }
   def matchTest(x: Int): String = x match {
      case 1 => "one"
      case 2 => "two"
      case _ => "many"
   }
}
```
match 对应 Java 里的 switch，但是写在选择器表达式之后。即： **选择器 match {备选项}**。
##正则表达式

##提取器

##文件IO

##案例
###提取器在正则表达式中的运用
```
val pattern = "(\\d+) (\\w+)".r //定义正则
"1234 abc" match {
  case pattern(number, chars) => println( number +" " + chars)
}
```
**pattern(number, chars) ，就是将”1234 abc” 通过正则匹配后，变为2个分组，第一个分组为number，第二分组为chars，结果变为 1234 abc**

## 参考文档
- 1 [菜鸟教程scala](http://www.runoob.com/scala/scala-tutorial.html)