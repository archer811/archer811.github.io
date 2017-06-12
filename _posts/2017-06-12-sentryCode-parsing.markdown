---
layout: post
title:      "apache sentry的安装"
subtitle:   "Sentry Code Parsing"
date:       2017-06-12 18:00:00
author:     "Zhouxj"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - sentry
    - hive
---
我们看一下在hive-site里面配置了什么参数，从参数入手，来看sentry部分的代码。
1，	metastore的插件
<div>
</di>
<property>
    <name>hive.metastore.pre.event.listeners</name>
    <value>org.apache.sentry.binding.metastore.MetastoreAuthzBinding</value>
    <description>list of comma separated listeners for metastore events.</description>
</property>
<property>
    <name>hive.metastore.event.listeners</name>
    <value>org.apache.sentry.binding.metastore.SentryMetastorePostEventListener</value>
    <description>list of comma separated listeners for metastore, post events.</description>
</property>
<div>
</di>
就是hiveserver2/cli 调用metastore，如果metastore执行的时候触发events，调用插件。这部分代码比较简单，先跳过。
MetastoreAuthzBinding处理的event:
<img src="//archer811.github.io/img/post-sentrycode-parsing-1.png"  width="680" alt="sentry code"/>
具体的过程是inputHierarchy,outputHierarchy，给相应的event增加相应的输入层次机构，输出层次结构，inputPrivileges,outputPrivileges，输入/输出的权限，并进行权限认证。
SentryMetastorePostEventListener做的事情: 如果涉及到路径（比如create table，table的hdfs地址），检查路径的权限。然后连存sentry的数据库加/减数据，新建一个 HiveMetaStoreClient，
发送thrift rpc请求进行操作。
2，
<property>
   <name>hive.security.authorization.task.factory</name>
   <value>org.apache.sentry.binding.hive.SentryHiveAuthorizationTaskFactoryImpl</value>
</property>
具体实现以下task
<img src="//archer811.github.io/img/post-sentrycode-parsing-2.png"  width="680" alt="sentry code"/>
上面这些函数都会返回sentry的task: SentryFilterDDLTask，SentryGrantRevokeTask.
在语法hook的postAnaylse，对task进行参数的设置。
当hive执行task的时候，就调用SentryFilterDDLTask，SentryGrantRevokeTask的execute方法了。
<img src="//archer811.github.io/img/post-sentrycode-parsing-3.png"  width="680" alt="sentry code"/>
3，
<property>
        <name>hive.server2.session.hook</name>
        <value>org.apache.sentry.binding.hive.HiveAuthzBindingSessionHook</value>
</property>
Hive的源码里session执行过程中调用sessionhook的run方法。
<img src="//archer811.github.io/img/post-sentrycode-parsing-4.png"  width="680" alt="sentry code"/>
HiveAuthzBindingSessionHook实现run方法。
那么run方法干了什么：
设置SEMANTIC_ANALYZER_HOOK为org.apache.sentry.binding.hive.HiveAuthzBindingHook
设置HIVE_AUTHORIZATION_MANAGER为org.apache.sentry.binding.hive.HiveAuthzBindingSessionHook$SentryHiveAuthorizerFactory
设置其他的变量。
所以要修改hive-site，加上面两个参数，hivecli不用disabled，也可以和hiveserver2一样使用列权限。

那么什么时候会执行这两个参数。
看hive源码。
Hql传入，先词法分析，生成tree，再语法分析，调用语法hook（SEMANTIC_ANALYZER_HOOK），再doAuthorization授权。HIVE_AUTHORIZATION_MANAGER是null，没做什么。
所以看语法hook干了什么。
1.preAnalyze, 2.postAnalyze
preAnalyze：根据tree里节点的类型（比如TOK_CREATEDATABASE），给currDB,currTab等赋值。
postAnalyze：sentryTask设置setAuthzConf,setSubject等。
<img src="//archer811.github.io/img/post-sentrycode-parsing-5.png"  width="680" alt="sentry code"/>
DDLTask设置成filterTask
<img src="//archer811.github.io/img/post-sentrycode-parsing-6.png"  width="680" alt="sentry code"/>
然后进行权限认证，看已有的权限的是否包含需要的权限，
authorizeWithHiveBindings(context, stmtAuthObject, stmtOperation);
需要的权限都存在HiveAuthzPrivilegesMap.java里
例如drop table 需要Db的ALL:
<img src="//archer811.github.io/img/post-sentrycode-parsing-7.png"  width="680" alt="sentry code"/>
4，
<property>
    <name>hive.metastore.client.impl</name>
    <value>org.apache.sentry.binding.metastore.SentryHiveMetaStoreClient</value>
    <description>Sets custom Hive metastore client which Sentry uses to filter out metadata.</description>
</property>
生成authorizerV2的时候用到
<img src="//archer811.github.io/img/post-sentrycode-parsing-8.png"  width="680" alt="sentry code"/>





