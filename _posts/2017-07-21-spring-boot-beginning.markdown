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
1. 通过SPRING INITIALIZR工具产生基础项目<br>
  1) 访问：http://start.spring.io/<br>
  2) 选择构建工具Maven Project、Spring Boot版本以及一些工程基本信息<br>
  3) 点击Generate Project下载项目压缩包<br>
2. 解压项目包，并用IDE以Maven项目导入<br>
3. 写一个Controller，要求和`@SpringBootApplication`的主类在相同的包或者同一个父包里<br>
4. run主类，打开(http://localhost://8080)可以看到效果<br>
从案例，可以学到如下：<br>
* 如果是maven项目，则包含pom.xml，如果是Gradle，build.gradle<br>
pom.xml包含项目依赖，最基础的两个<br>
```
spring-boot-starter：核心模块，包括自动配置支持、日志和YAML
spring-boot-starter-test：测试模块，包括JUnit、Hamcrest、Mockito
```
如果引入Web模块，需添加`spring-boot-starter-web`模块<br>
* Application.java 带main() 方法的类，用于引导启动应用程序
* ApplicationTests.java 一个空的Junit测试类，它加载了一个使用Spring Boot自动配置功能的Spring应用程序上下文
* application.properties 根据需要添加配置属性


### 碰到的报错
尝试一个复杂一点的例子:[参考链接](http://blog.didispace.com/springbootrestfulapi/)
我碰到几个报错，以后出现同样的问题就容易解决了
* `Your ApplicationContext is unlikely to start due to a @ComponentScan of the default package.`<br>
因为application.Java 文件不能直接放在main/java文件夹下，必须要建一个包把他放进去<br>
* `Ambiguous mapping. Cannot map 'usersController' method`<br>
因为userController下某两个方法的映射名相同,导致这个错误.<br>
比如两个方法这样写<br>
```
@RequestMapping(value="/{id}", method = RequestMethod.GET)
public User getUser(@PathVariable Long id) {
    return  users.get(id);
}
@RequestMapping(value = "/{id}", method = RequestMethod.PUT)
```
他们都是 `/{id}`映射名<br>


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

### 配置（理论）
某次肯定是有配置的，配置是Spring Framework的核心元素，必须要有东西告诉Spring如何运行应用程序。<br>
在向应用程序加入Spring Boot的时候，有个名为spring-boot-autoconfigure的JAR文件，其中包含了很多配置类。每个配置类都应该在应用程序的Classpath里，<br>
这些配置有用于Thymeleaf的配置，有用于SpringDataJPA的配置，有用于SpringMVC的配置，还有很多其他东西的配置，你可以自己选择是否在Spring应用程序里使用它们。<br>
<br>
条件化配置允许配置存在于应用程序中，但在满足某些特定条件之前都忽略这个配置<br>
在Spring里可以很方便的编写你自己的条件，你所要做的就是实现Condition接口，覆盖它的matches()方法。