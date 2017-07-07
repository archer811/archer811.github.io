---
layout: post
title:      "Java反射"
subtitle:   "Java Reflect"
date:       2017-07-07 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-javareflect.jpg"
catalog: true
comments: true
tags:
    - java基础
---
参考 [别人的博客](http://www.sczyh30.com/posts/Java/java-reflection-1/) .



### 什么是反射

通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息，程序中一般的对象的类型都是在编译期就确定下来的，而Java反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。

Java反射框架主要提供以下功能：

- 1.在运行时判断任意一个对象所属的类；
- 2.在运行时构造任意一个类的对象；
- 3.在运行时判断任意一个类所具有的成员变量和方法（通过反射甚至可以调用private方法）；
- 4.在运行时调用任意一个对象的方法

### 在运行时判断任意一个对象所属的类

#### 1 获得class

*  1.使用Class类的forName静态方法
```
public static Class<?> forName(String className)
比如Class.forName("org.apache.storm.task.ShellBolt");
```
```
Class<?> klass = methodClass.class;
//创建methodClass的实例
Object obj = klass.newInstance();
//获取methodClass类的add方法
Method method = klass.getMethod("add",int.class,int.class);
//调用method对应的方法 => add(1,4)
Object result = method.invoke(obj,1,4);// 这里的obj一定要是实例
```


*  2.直接获取某个对象的class

```
Class<?> klass = int.class;
Class<?> classInt = Integer.TYPE;
```

*  3.调用某个对象的getClass()方法

```
StringBuilder str = new StringBuilder("123");
Class<?> klass = str.getClass();
```
#### 2 判断是否为某个类的实例
```
A a = null;
System.out.println(a instanceof A); // false
System.out.println(A.class.isInstance(a)); // false
a = new A();
System.out.println(a instanceof A); // true
System.out.println(A.class.isInstance(a)); // true
```

### 在运行时构造任意一个类的对象

:smile: **这里很重要一点**

Class和实例的区别：Class可以理解成就是二进制代码。实例是真实对象。

通过反射来生成对象主要有两种方式

* 1.使用Class对象的newInstance()方法来创建Class对象对应类的实例
```
Class<?> c = String.class;
Object str = c.newInstance();
```
* 2.先通过Class对象获取指定的Constructor对象，再调用Constructor对象的newInstance()方法来创建实例。这种方法可以用指定的构造器构造类的实例
```
//获取String所对应的Class对象
Class<?> c = String.class;
//获取String类带一个String参数的构造器
Constructor constructor = c.getConstructor(String.class);
//根据构造器创建实例
Object obj = constructor.newInstance("23333");
System.out.println(obj);
```

### 在运行时判断任意一个类所具有的成员变量和方法

获取某个Class对象的方法集合，主要有以下几个方法：
getDeclaredMethods()方法返回类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。

```
public Method[] getDeclaredMethods() throws SecurityException
```

getMethods()方法返回某个类的所有公用（public）方法，包括其继承类的公用方法。

```
public Method[] getMethods() throws SecurityException
```

getMethod方法返回一个特定的方法，其中第一个参数为方法名称，后面的参数为方法的参数对应Class的对象

```
public Method getMethod(String name, Class<?>... parameterTypes)
```
```
Class<?> c = methodClass.class;
Object object = c.newInstance();
Method[] methods = c.getMethods();
Method[] declaredMethods = c.getDeclaredMethods();
//获取methodClass类的add方法
Method method = c.getMethod("add", int.class, int.class);
```
### 在运行时调用任意一个对象的方法

当我们从类中获取了一个方法后，我们就可以用invoke()方法来调用这个方法。invoke方法的原型为:

```
public Object invoke(Object obj, Object... args)
        throws IllegalAccessException, IllegalArgumentException,
           InvocationTargetException
```

### 总结
**由于反射会额外消耗一定的系统资源，因此如果不需要动态地创建一个对象，那么就不需要用反射** <br>
**另外，反射调用方法时可以忽略权限检查，因此可能会破坏封装性而导致安全问题**