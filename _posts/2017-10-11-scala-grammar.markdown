---
layout: post
title:      "scala语法"
subtitle:   "scala语法"
date:       2017-10-11 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-scala-gram.jpg"
catalog: true
comments: true
tags:
    - scala
---

### 简单语法

#### _ 符合代替
如果仅有一个参数在右侧的函数体内只使用一次，将接收参数省略，用 _ 替代

#### 伴生类和伴生对象
所谓伴生对象， 也是一个Scala中的单例对象， 使用object关键字修饰。 除此之外， 还有一个使用class关键字定义的同名类， 这个类和单例对象存在于同一个文件中， 这个类就叫做这个单例对象的伴生类， 相对来说， 这个单例对象叫做伴生类的伴生对象
```
class Test{
  var field = "field"
  def doSomeThing = println("do something")
}
object  Test {
	val a = "a string";
	def printString = println(a)
}
```
### 函数



### 面向对象编程





### 函数式编程

#### 将函数赋值给变量
将函数赋值给变量时，必须在函数后面加上空格和下划线
```
def sayHello(name: String) { println("Hello, " + name) }
val sayHelloFunc = sayHello _
```
#### 匿名函数
1. 函数可以不需要命名
2. 直接定义函数之后，可以将函数赋值给某个变量，亦可以将直接定义的匿名函数传入其他函数之中
3. 定义匿名函数的语法规则就是，(参数名: 参数类型) => 函数体

#### 高阶函数
1. 直接将某个函数传入其他函数，**作为参数**。被称作高阶函数。
```
def greeting(func:(String)=>Unit,name:String)={func(name)}
def sayHello(name: String) { println("Hello, " + name) }
greeting(sayHello,"gg")
```
2. **作为返回值**

```
def getGreetingFunc(msg:String) = (name:String)=>println(msg+name)
```

#### 高阶函数的类型推断
在使用高阶函数的时候，注意是在使用的时候，高阶函数可以自动推断出参数类型

#### Scala的常用高阶函数
1. `map`: 对传入的**每个元素**都进行映射，返回一个处理后的元素
2. `foreach`： 对传入的**每个元素**都进行处理，但是没有返回值
3. `filter`： 对传入的**每个元素**都进行条件判断，如果对元素返回true，则保留，否则过滤

#### 闭包
在变量不处于其有效作用域时，还能够对变量进行访问，即为闭包
#### Currying函数
1. 指的是，将原来接收两个参数的一个函数，转换为两个函数，第一个函数接收原先的第一个参数，然后返回接收原先第二个参数的第二个函数
2. 在函数调用过程中，就变为了两个函数连续调用的形式
### 模式匹配



### 类型参数
意思与Java的泛型一样
#### 泛型类
#### 泛型函数
#### 上下界Bounds
#### View Bounds
上下边界Bounds的加强版，支持可以对类型进行**隐式转换**，将指定的类型进行隐式转换后，**再判断是否在边界指定的范围内**
```
class Person(val name: String) {
  def sayHello = println("Hello, I'm " + name)
  def makeFriends(p: Person) {
    sayHello
    p.sayHello
  }
}
class Student(name: String) extends Person(name)
class Dog(val name: String) { def sayHello = println("Wang, Wang, I'm " + name) }

implicit def dog2person(dog: Object): Person = if(dog.isInstanceOf[Dog]) {val _dog = dog.asInstanceOf[Dog]; new Person(_dog.name) } else Nil

class Party[T <% Person](p1: T, p2: T)
```
#### Context Bounds
比如“T: 类型”要求必须存在一个类型为`“类型[T]”`的隐式值
```
class Calculator[T: Ordering] (val number1: T, val number2: T) {
  def max(implicit order: Ordering[T]) = if(order.compare(number1, number2) > 0) number1 else number2
}
```
#### Manifest Context Bounds
如果数组元素类型为T的话，需要为类或者函数定义`[T: Manifest]`泛型类型
```
def packageFood[T: Manifest] (food: T*) = {
```
### 隐式转换与隐式参数
1. 允许你手动指定，将某种类型的对象转换成其他类型的对象。
2. 定义隐式转换函数，只要在编写的程序内引入，就会被Scala自动使用。Scala会根据隐式转换函数的签名，在程序中使用到隐式转换函数接收的参数类型定义的对象时，会自动将其传入隐式转换函数
3. 命名通常"one2one"
#### 隐式函数
唯一的语法区别就是，要以implicit开头，而且最好要定义函数返回类型
#### 隐式参数
1. 所谓的隐式参数，指的是在函数或者方法中，定义一个用`implicit`修饰的参数`（比如，implicit signPen: SignPen）`，此时Scala会尝试找到一个指定类型的，用`implicit`修饰的对象`（比如implicit val signPen = new SignPen）`，即隐式值，并注入参数。
2. `Scala`会在两个范围内查找: 一种是当前作用域内可见的val或var定义的隐式变量；一种是隐式参数类型的伴生对象内的隐式值<br>

```
class SignPen {
  def write(content: String) = println(content)
}
implicit val signPen = new SignPen

def signForExam(name: String) (implicit signPen: SignPen) {
  signPen.write(name + " come to exam in time.")
}
```
#### 隐式转换函数的作用域与导入
1. Scala默认会使用两种隐式转换，一种是源类型，或者目标类型的伴生对象内的隐式转换函数；一种是当前程序作用域内的可以用唯一标识符表示的隐式转换函数<br>
2. 如果隐式转换函数不在上述两种情况下的话，那么就必须手动使用`import`语法引入某个包下的隐式转换函数。<br>
#### 隐式转换的发生时机
1. 调用某个函数，但是给函数传入的参数的类型，与函数定义的接收参数类型不匹配

```
class SpecialPerson(val name: String)
class Student(val name: String)
class Older(val name: String)
implicit def object2SpecialPerson (obj: Object): SpecialPerson = {
  if (obj.getClass == classOf[Student]) { val stu = obj.asInstanceOf[Student]; new SpecialPerson(stu.name) }
  else if (obj.getClass == classOf[Older]) { val older = obj.asInstanceOf[Older]; new SpecialPerson(older.name) }
  else Nil
}
var ticketNumber = 0
def buySpecialTicket(p: SpecialPerson) = {
  ticketNumber += 1
  "T-" + ticketNumber
}
```
2. 使用某个类型的对象，调用某个方法，而这个方法并不存在于该类型时

```
class Man(val name: String)
class Superman(val name: String) {
  def emitLaser = println("emit a laster!")
}
implicit def man2superman(man: Man): Superman = new Superman(man.name)
val leo = new Man("leo")
leo.emitLaser
```
3. 使用某个类型的对象，调用某个方法，虽然该类型有这个方法，但是给方法传入的参数类型，与方法定义的接收参数的类型不匹配

```
class TicketHouse {
  var ticketNumber = 0
  def buySpecialTicket(p: SpecialPerson) = {
    ticketNumber += 1
    "T-" + ticketNumber
  }
}
```
### 模式匹配
#### 基础语法
1. 匹配变量的值
2. `match case`的语法如下：变量 `match { case 值 => 代码 }`。如果值为下划线，则代表了不满足以上所有情况下的默认情况如何处理。此外，`match case`中，只要一个case分支满足并处理了，就不会继续判断下一个case分支了<br>
```
def judge(id:Int) = {
  id match {
    case 1 => println("1")
    case 2 => println("a")
  }
}
judge(1)
judge(2)
```
3. **可以在case后的条件判断中，不仅仅只是提供一个值，而是可以在值后面再加一个if守卫，进行双重过滤**
```
def judge(name:String, id:Int) = {
  id match {
    case 1 => println("1")
    case 2 if name =="leo" => println("a")
  }
}
```
4. **可以将模式匹配的默认情况，下划线，替换为一个变量名，此时模式匹配语法就会将要匹配的值赋值给这个变量，从而可以在后面的处理语句中使用要匹配的值**
```
def judge(name:String, id:Int) = {
  id match {
    case _id  => println("1" + _id)
  }
}
```
#### 对类型进行模式匹配
其他语法与匹配值其实是一样的，但是匹配类型的话，就是要用`“case 变量: 类型 => 代码”`这种语法，而不是匹配值的`“case 值 => 代码”`这种语法
#### 对Array和List的元素进行模式匹配
1. 对Array进行模式匹配，分别可以匹配带有指定元素的数组、带有指定个数元素的数组、以某元素打头的数组
2. 对List进行模式匹配，与Array类似，但是需要使用List特有的::操作符
```
def greeting(arr: Array[String]) {
  arr match {
    case Array("Leo") => println("Hi, Leo!")
    case Array(girl1, girl2, girl3) => println("Hi, girls, nice to meet you. " + girl1 + " and " + girl2 + " and " + girl3)
    case Array("Leo", _*) => println("Hi, Leo, please introduce your friends to me.")
    case _ => println("hey, who are you?")
  }
}
def greeting(list: List[String]) {
  list match {
    case "Leo" :: Nil => println("Hi, Leo!")
    case girl1 :: girl2 :: girl3 :: Nil => println("Hi, girls, nice to meet you. " + girl1 + " and " + girl2 + " and " + girl3)
    case "Leo" :: tail => println("Hi, Leo, please introduce your friends to me.")
    case _ => println("hey, who are you?")
  }
}
```
#### case class
1. Scala中提供了一种特殊的类，用`case class`进行声明。`case class`其实有点类似于Java中的JavaBean的概念。即只定义field，并且由Scala编译时自动提供`getter和setter`方法，但是没有method。
2. `case class`的主构造函数接收的参数通常不需要使用var或val修饰，Scala自动就会使用val修饰（但是如果你自己使用var修饰，那么还是会按照var来）
3. Scala自动为`case class`定义了伴生对象，也就是object，并且定义了`apply()`方法，该方法接收主构造函数中相同的参数，并返回`case class`对象
```
class Person
case class Teacher(name: String, subject: String) extends Person
case class Student(name: String, classroom: String) extends Person
def judgeIdentify(p: Person) {
  p match {
    case Teacher(name, subject) => println("Teacher, name is " + name + ", subject is " + subject)
    case Student(name, classroom) => println("Student, name is " + name + ", classroom is " + classroom)
    case _ => println("Illegal access, please go out of the school!")
  }
}
```
#### Option
```
val grades = Map("Leo" -> "A", "Jack" -> "B", "Jen" -> "C")
def getGrade(name: String) {
  val grade = grades.get(name)
  grade match {
    case Some(grade) => println("your grade is " + grade)
    case None => println("Sorry, your grade information is not in the system")
  }
}
```