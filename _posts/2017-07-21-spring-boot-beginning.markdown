---
layout: post
title:      "spring-boot-初学"
subtitle:   "spring-boot-beginning"
date:       2017-07-21 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-springboot-bg.jpg"
catalog: true
comments: true
tags:
    - spring
---

### spring boot 快速入门案例
网上随便找教程或者书，都有入门案例介绍：<br>
[参考链接](http://blog.didispace.com/spring-boot-learning-1/)<br>
* 通过SPRING INITIALIZR工具产生基础项目
  1.访问：http://start.spring.io/<br>
  2.选择构建工具Maven Project、Spring Boot版本以及一些工程基本信息<br>
  3.点击Generate Project下载项目压缩包<br>
* 解压项目包，并用IDE以Maven项目导入
<br>
`@SpringBootApplication` 主程序入口，直接启动当前项目

### 合理的工程结构
[参考链接](http://blog.didispace.com/springbootproject/)<br>
```
com
  +- example
    +- myproject
      +- Application.java
      |
      +- domain
      |  +- Customer.java
      |  +- CustomerRepository.java
      |
      +- service
      |  +- CustomerService.java
      |
      +- web
      |  +- CustomerController.java
      |
```
* 应用主类Application.java置于root package下，通常我们会在应用主类中做一些框架配置扫描等配置，我们放在root package下可以帮助程序减少手工配置来加载到我们希望被Spring加载的内容
* 实体（Entity）与数据访问层（Repository）置于com.example.myproject.domain包下
* 逻辑层（Service）置于com.example.myproject.service包下
* Web层（web）置于com.example.myproject.web包下

### 配置
未完待续