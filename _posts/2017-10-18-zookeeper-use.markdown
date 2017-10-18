---
layout: post
title:      "Zookeeper的常用命令及javaApi的使用"
subtitle:   "Zookeeper commands and the use of javaApi"
date:       2017-10-18 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-zookeeper.jpg"
catalog: true
comments: true
tags:
    - Zookeeper
---

### ZooKeeper服务命令

1. 启动ZK服务:`sh bin/zkServer.sh start`
2. 查看ZK服务状态:`sh bin/zkServer.sh status`
3. 停止ZK服务:`sh bin/zkServer.sh stop`
4. 重启ZK服务:`sh bin/zkServer.sh restart`

### zk客户端命令

使用 zkCli.sh -server 127.0.0.1:2181 连接到 ZooKeeper 服务，连接成功后，系统会输出 ZooKeeper 的相关环境以及配置信息。

### 命令行工具

1. 显示根目录下、文件： ls / 使用 ls 命令来查看当前 ZooKeeper 中所包含的内容
2. 显示根目录下、文件： ls2 / 查看当前节点数据并能看到更新次数等数据
3. 创建文件，并设置初始内容： create /zk "test" 创建一个新的 znode节点“ zk ”以及与它关联的字符串
4. 获取文件内容： get /zk 确认 znode 是否包含我们所创建的字符串
5. 修改文件内容： set /zk "zkbak" 对 zk 所关联的字符串进行设置
6. 删除文件： delete /zk 将刚才创建的 znode 删除
7. 退出客户端： quit
8. 帮助命令： help

### JavaAPI

#### 添加依赖
```
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.5-cdh5.4.7</version>
</dependency>
```
具体的使用
```
ZooKeeper zk = new zk = new ZooKeeper(zkAddress, ZK_SESSION_TIMEOUT, new Watcher() {
                public void process(WatchedEvent watchedEvent) {
                    connectedSignal.countDown();
                }
});
zk.create(path,null,ZooDefs.Ids.OPEN_ACL_UNSAFE,CreateMode.PERSISTENT);
zk.delete(path,-1);
zk.getChildren(path);
```
或者
```
<dependency>
	<groupId>com.101tec</groupId>
	<artifactId>zkclient</artifactId>
	<version>0.10</version>
</dependency>
```
具体的使用
```
ZkClient zk = new ZkClient(zkAddress,zookeeperTimeout);
zk.create(path,null,ZooDefs.Ids.OPEN_ACL_UNSAFE,CreateMode.PERSISTENT);
zk.delete(path);
zk.getChildren(path);
```
上面两种方式，都会有 如果zookeeper路径存在，就报“ZkNodeExistsException”溢出，然后结束程序。

### 可以用Zookeeper实现的功能
分布式系统中服务的注册与发现

### 参考
`http://blog.csdn.net/u012562943/article/details/52963506`<br>
`http://blog.csdn.net/xiaolang85/article/details/13021339`