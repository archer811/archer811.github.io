---
layout: post
title:      "感知机"
subtitle:   "the-perceptron"
date:       2017-07-26 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-perceptron.jpg"
catalog: true
comments: true
tags:
    - 统计学习方法
---

> 《统计学习方法》 第二章

这本书还是挺有挑战性的。有的论证，公式第一遍看不懂，看第二，三遍的时候突然一下子明白了，所以还蛮有意思。<br>
对书中的算法用java实现，以后慢慢改成Python，再写写自己的理解。这样一套下来，是有点累，现在是纯当作兴趣，工作主线还是把hive，spark精通。<br>

### 感知机概念
学这本书首先要改变的一个观念，输入X，X不是单纯一个变量，它是输入空间，很多维的。<br>
感知机是二类分类的线性分类模型。对应于输入空间，将实例划分为正负两类的分离超平面。<br>
`f(x)=sign(w*x+b)`

### 感知机学习策略
损失函数：误分类点到超平面S的总距离。<br>
```
 \left[
 \begin{matrix}
   a & 0\\
   1 & 1\\
   2 & 3
  \end{matrix}
  \right]
  \tag{tag}
  ```
  <br>
 \begin{equation*}L(w,b) = - \sum\limits_{x_i\in{M}}y_i(w*x_i+b)\end{equation*}
### 感知机学习算法实现
#### 原始形式
采用梯度下降法，只要数据集是线性可分的，，算法收敛性。<br>
[代码](https://github.com/archer811/MachineLearning/blob/master/Perceptron/PerceptionM.java)
#### 对偶形式
可以理解成：原始形式是同时修改w，b参数。而对偶形式输入的每一维是互相独立，每次修改某一维。<br>
[代码](https://github.com/archer811/MachineLearning/blob/master/Perceptron/DualM.java)