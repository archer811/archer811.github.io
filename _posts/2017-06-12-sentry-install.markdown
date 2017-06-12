---
layout: post
title:      "apache sentry的安装"
subtitle:   "Installation  of apache sentry"
date:       2017-06-12 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - sentry
    - hive
---

apache sentry 目前有1.7的发布版，1.7之前的都是孵化版。从1.5开始就加入了列权限。

可以用cdh版本的，也可以不用。

以下是sentry-1.5.1-cdh5.5.0的安装过程。其他版本的类似。

### 代码修改
因为我用的是cdh版的hive，sentry里面有个地方的代码，
<img src="//img/post-code-sentry.png"  width="320" alt="sentry code"/>
如果Authorizer是null，就会强制使用Auth V2，根据sentry的代码，后面AuthV2 的accessController，authValidator都是null，会报错。
所以把删掉


### 代码编译

### 修改配置