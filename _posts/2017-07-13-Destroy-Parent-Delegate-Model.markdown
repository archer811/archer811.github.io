---
layout: post
title:      "破坏双亲委派模型"
subtitle:   "Destroy parent delegate model"
date:       2017-07-13 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-Destroypm.jpg"
catalog: true
comments: true
tags:
    - java
    - jvm
    - 读书笔记
---

>  《深入理解Java虚拟机》 第七章

双亲委派模型要求除了顶层的启动类加载器外，其余的类加载期都应当有自己的父类加载器。

### 工作流程
双亲委派模型的工作流程：如果一个类加载器收到类加载的请求，他首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成。
每个层次的累加器都是如此，因此所有的加载请求最终都应该传送到顶层的启动类加载器中。
只有父加载器反馈自己无法完成这个加载请求，子加载器才会尝试自己去加载。

### 破坏双亲委派模型
1，jdk1.0->jdk1.2，添加了新的protected的`findClass()`，之前是重写`loadClass()`方法
2，父类加载器请求子类加载器去完成类加载的动作。java团队引入线程上下文类加载器。这个类加载器可以通过`java.lang.Thread`类的
`setContextClassLoader()`方法设置，如果创建线程时还未设置，它将会从父线程继承一个，如果在应用程序的全局范围内都没有设置的话，那这个类加载器默认就是应用程序类加载器。
3，热替换