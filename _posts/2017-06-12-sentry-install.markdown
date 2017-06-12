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
---

apache sentry 目前有1.7的发布版，1.7之前的都是孵化版。从1.5开始就加入了列权限。

可以用cdh版本的，也可以不用。

以下是sentry-1.5.1-cdh5.5.0的安装过程。其他版本的类似。

### 代码修改
因为我用的是cdh版的hive，sentry里面有个地方的代码，
<img src="//archer811.github.io/img/post-code-sentry.png"  width="680" alt="sentry code"/>
如果Authorizer是null，就会强制使用Auth V2，根据sentry的代码，后面AuthV2 的accessController，authValidator都是null，会报错。
所以把SessionState.get().setAuthorizer(null);这行删掉


### 代码编译
> mvn clean install -DskipTests
取sentry-dist\target\apache-sentry-1.5.1-cdh5.5.0-bin.tar.gz安装


### 修改配置
有3个地方需要增加/修改配置
- 1，在sentry的安装目录/usr/lib/sentry/conf，添加sentry-site.xml，sentry服务启动需要的配置项。
加入以下参数：
   <property>
       <name>sentry.service.server.rpc-port</name>
       <value>8038</value>
    </property>
    <property>
       <name>sentry.service.server.rpc-address</name>
       <value>----</value>
    </property>
    <property>
       <name>sentry.service.server.rpc-connection-timeout</name>
       <value>200000</value>
    </property>
    <property>
        <name>sentry.service.security.mode</name>
        <value>kerberos</value>
    </property>
    <property>
       <name>sentry.service.server.principal</name>
        <value>-----</value>
    </property>
    <property>
        <name>sentry.service.server.keytab</name>
        <value>/etc/sentry/conf/sentry.keytab</value>
    </property>
    <property>
        <name>sentry.service.admin.group</name>
        <value>admin,hive,op,bigdata</value>
    </property>
    <property>
        <name>sentry.service.allow.connect</name>
        <value>admin,sentry,op,hive</value>
    </property>
    <property>
            <name>sentry.store.jdbc.url</name>
            <value>----</value>
    </property>
    <property>
            <name>sentry.store.jdbc.driver</name>
            <value>com.mysql.jdbc.Driver</value>
    </property>
    <property>
            <name>sentry.store.jdbc.user</name>
            <value>-----</value>
    </property>
    <property>
            <name>sentry.store.jdbc.password</name>
            <value>----</value>
    </property>
    <property>
        <name>sentry.hive.provider.backend</name>
        <value>org.apache.sentry.provider.db.SimpleDBProviderBackend</value>
    </property>
    <property>
        <name>sentry.hive.server</name>
        <value>server1</value>
    </property>
    <property>
        <name>sentry.hive.testing.mode</name>
        <value>true</value>
     </property>
初始化sentry的mysql数据库。
sentry --command schema-tool --conffile /etc/sentry/conf/sentry-site.xml --dbType mysql –initSchema
sudo sh  /usr/lib/sentry/bin/sentry --log4jConf /etc/sentry/conf/sentry-log4j.properties  --command service --conffile /etc/sentry/conf/sentry-site.xml > ~/sentry.pid 2>&1 &

- 2，hive的安装目录/usr/lib/sentry/conf，添加sentry-site.xml，这是sentry的客户端配置。
    <property>
       <name>sentry.service.client.server.rpc-port</name>
       <value>8038</value>
    </property>
    <property>
       <name>sentry.service.client.server.rpc-address</name>
       <value>----</value>
    </property>
    <property>
       <name>sentry.service.client.server.rpc-connection-timeout</name>
       <value>200000</value>
    </property>
    <property>
        <name>sentry.hive.provider.backend</name>
        <value>org.apache.sentry.provider.db.SimpleDBProviderBackend</value>
    </property>
    <property>
        <name>sentry.metastore.service.users</name>
        <value>op,hive</value><!--queries made by hive user (beeline) skip meta store check-->
    </property>
      <property>
        <name>sentry.hive.server</name>
        <value>server1</value>
      </property>
     <property>
        <name>sentry.hive.testing.mode</name>
        <value>true</value>
     </property>
    <property>
       <name>sentry.service.server.principal</name>
        <value>----</value>
    </property>
- 3，修改hive的配置文件hive-site.xml，hive加入sentry插件。
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
    <property>
       <name>hive.security.authorization.task.factory</name>
       <value>org.apache.sentry.binding.hive.SentryHiveAuthorizationTaskFactoryImpl</value>
    </property>
    <property>
       <name>hive.sentry.conf.url</name>
       <value>file:///etc/hive/conf/sentry-site.xml</value>
    </property>
    <property>
        <name>hive.metastore.client.impl</name>
        <value>org.apache.sentry.binding.metastore.SentryHiveMetaStoreClient</value>
        <description>Sets custom Hive metastore client which Sentry uses to filter out metadata.</description>
    </property>
     <property>
            <name>hive.server2.session.hook</name>
            <value>org.apache.sentry.binding.hive.HiveAuthzBindingSessionHook</value>
     </property>
