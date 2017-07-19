---
layout: post
title:      "spark编程rdd-programming"
subtitle:   "rdd-programming"
date:       2017-07-19 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-rdd-program.jpg"
catalog: true
comments: true
tags:
    - spark
---

> 跟Spark-Rdd有关的编程练习

### rdd和partition
前面一篇博客说到spark的编程模型，RDD分区，RDD依赖关系，分区计算。那么RDD分区和 rdd编程有什么关系呢
答：（有点啰嗦）Spark集群的节点个数为集群的机器的数量。一个机器上有几个worker，一个woker可以申请多少core是可配置的。一个常用的配置是：<br>
一台机器一个worker，一个woker可拥有的最大core数是机器逻辑cpu的数量。<br>
在这种情况下，一个core就可以理解为一台机器的一个逻辑核。<br>

**而RDD的分区个数决定了这个RDD被分为多少片（partition）来执行，一个片给一个Core**<br>

rdd编程就是生成一个个rdd，经过变化变成新的rdd。<br>

初始化 sparkContext<br>
```
val conf = new SparkConf().setAppName(appName).setMaster(master)
new SparkContext(conf)
```

### 生成rdd
resilient distributed dataset ： 弹性分布式数据集<br>
* 存在的 collection， dataset<br>
* 从文件系统 ， HDFS, HBase等<br>

### rdd 操作
transformations：不加载到内存或其他，只是一个指针指向到目标<br>
可以 persist an RDD in memory using the persist (or cache) method,<br>

spark里使用函数<br>
```
object MyFunctions {
  def func1(s: String): String = { ... }
}
myRdd.map(MyFunctions.func1)
```

### 理解闭包
看个例子<br>
```
var counter = 0
var rdd = sc.parallelize(data)

// Wrong: Don't do this!!
rdd.foreach(x => counter += x)

println("Counter value: " + counter)
```
counter是存在driver那台机器内存里。<br>
闭包内的变量发送给每个executor，每个function用的counter不再是driver的那个<br>
但，如果在本地，同一个jvm内。就可以出正确的执行结果<br>

### shuffle
all to all 模式，partitions 到 partitions



