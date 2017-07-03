---
layout: post
title:      "读书笔记——Spark编程模型"
subtitle:   "Spark Programming model"
date:       2017-07-03 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-2015.jpg"
catalog: true
comments: true
tags:
    - spark
    - 读书笔记
---

>  《图解Spark核心技术与案例实战》 第三章

目前对spark还处在刚接触的阶段，请原谅我所了解的只是全部spark知识中的一部分。
这篇笔记主要讲spark的编程模型。

spark提供通用接口抽象每个rdd，接口包括：分区信息，依赖关系，函数，划分策略和数据位置的元数据


1，RDD分区
分区数目，如果没有指定，默认该程序分配到cpu核数，如果从hdfs文件创建，默认文件数据块数

2，RDD首选位置
spark形成任务有向无环图，尽可能把计算分配到靠近数据的位置，减少网络传输。
当rdd产生时存在首选位置，比如hadooprdd首选位置时hdfs块所在节点；当rdd分区被缓存，计算应该发送到缓存分区所在节点；找到具有首选位置属性的父rdd，并据此决定子rdd。

3，rdd依赖关系
宽依赖和窄依赖


4，分区计算
是分区为单位，不需要保存每次计算的结果。分区计算一般使用mapPartitions等操作。
f即为输入函数，他处理每个分区的内容。最终的rdd由所有分区经过输入函数处理后的结果合并起来的。

5，分区函数
hash分区划分器，范围区分划分器

