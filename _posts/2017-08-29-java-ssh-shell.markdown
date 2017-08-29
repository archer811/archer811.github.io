---
layout: post
title:      "java ssh 远程执行shell"
subtitle:   "java remote call shell"
date:       2017-08-29 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-java-ssh.jpg"
catalog: true
comments: true
tags:
    - java基础
---

方法有很多，这里讲3种

### JSch
maven依赖
```
<dependency>
    <groupId>com.jcraft</groupId>
    <artifactId>jsch</artifactId>
    <version>0.1.46</version>
</dependency>
```
版本不一定0.1.46，具体多少版本参考官网
### Ganymed
maven依赖
```
<dependency>
    <groupId>ch.ethz.ganymed</groupId>
    <artifactId>ganymed-ssh2</artifactId>
    <version>262</version>
</dependency>
```
具体链接实现
```
String hostname = "xxx.xxx.xxx.xxx";
    String username = "*";
    String password = "*";
    Connection conn = new Connection(hostname);
    Session ssh = null;
    try {
        //连接到主机
        conn.connect();
        //使用用户名和密码校验
        boolean isconn = conn.authenticateWithPassword(username, password);
        if (!isconn) {
            System.out.println("用户名或密码不正确");
        } else {
            System.out.println("连接OK");
            ssh = conn.openSession();
            //使用多个命令用分号隔开
            //只允许使用一行命令，即ssh对象只能使用一次execCommand这个方法，多次使用则会出现异常
            ssh.execCommand("pwd;cd /tmp;mkdir hb;ls;ps -ef|grep weblogic");
            //将屏幕上的文字全部打印出来
            InputStream is = new StreamGobbler(ssh.getStdout());
            BufferedReader brs = new BufferedReader(new InputStreamReader(is));
            while (true) {
                String line = brs.readLine();
                if (line == null) {
                    break;
                }
                System.out.println(line);
            }
        }
        //连接的Session和Connection对象都需要关闭
        ssh.close();
        conn.close();
    } catch (IOException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
}
```
我实际测试时候connect失败。
### 直接shell
直接执行shell命令。shell命令里先ssh，然后跑linux命令
```
String[] cmdA = { "/bin/sh", "-c", cmd };
Process process = Runtime.getRuntime().exec(cmdA);
```
*ExecLinuxCMD.exec("ssh -t -p 端口 用户名@ip地址 'ls -a'");*
### 总结
Jsch 和 Ganymed 都是利用第三方插件，步骤也差不多，先有Connection，然后connection建立session，session可能要打开channel
第三种方法用命令ssh远程，因为要手动输密码，麻烦，不易对执行失败的处理
