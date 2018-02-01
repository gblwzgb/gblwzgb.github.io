---
title: Linux熵池导致的Tomcat启动缓慢
date: 2017-03-29 11:34:47
categories: Tomcat
---

在阿里云的Linux服务器下，启动一个项目，查看Log4J日志，显示项目已经启动完成。

![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-29/64688158-file_1490765179140_13adf.png)

但是页面在请求的时候，一直在等待响应，大概要等5分钟，才能响应成功。

![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-29/82861973-file_1490765195892_6ad8.png)

这里记录一下解决的方法。

<!-- more -->

项目启动后，查看Tomcat目录/logs/catalina.out的日志。

发现
> Server startup in 338255 ms

竟然花了338秒。

继续查看日志，发现大部分时间都花在了SessionIdGenerator这个类的createSecureRandom()方法上。
> Mar 28, 2017 3:38:35 PM org.apache.catalina.util.SessionIdGenerator createSecureRandom  
INFO: Creation of SecureRandom instance for session ID generation using [SHA1PRNG] took [286,439] milliseconds.

大致意思是，Tomcat在生成sessionId的时候，用了叫做`SHA1PRNG`的算法，这个算法依赖于Linux下的熵池。

项目启动后，通过`jstack pid > log.txt`命令，如下图，观察项目中线程的运行情况，也确实看到有线程在执行createSecureRandom()方法。  
**PS：pid就是项目的进程id。`jstack`可能会提示无效的命令，这个时候就要到jdk/bin目录下去执行`jstack`命令了。**
![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-29/12999901-file_1490756527994_11216.png)

### 两种改法
**我试了第一种未生效，第二种生效了。**
1. 把%JAVA_HOME%/jre/lib/security/java.security下的

    `securerandom.source=file:/dev/random`

    改成

    `securerandom.source=file:/dev/./urandom`

2. 在启动参数中加上` -Djava.security.egd=file:/dev/./urandom`

    如图，我在catalina.sh加上了这么一句。
    
    ![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-29/95284874-file_1490756122738_99d5.png)

### 效果
> 信息: Server startup in 37857 ms

生效后，查看catalina.out日志，发现花了37秒，相比设置前快了十倍。

## 题外话

说点自己的理解，这里就不摘用很高深的术语了。

上面说到的优化方式在[Tomcat的启动优化文档](https://wiki.apache.org/tomcat/HowTo/FasterStartUp)里也提到了。

其他的优化有去掉不必要的jar包依赖（Maven把scope设置成private）等等。

### Log4J中的日志
Log4J中显示的加载完成，只是Spring框架加载完成了。

我们知道Tomcat是一个Servlet容器，而生成sessionId是容器这块的。容器没加载完毕，就算Spring加载完毕，也是算没有启动完成的。

### random和urandom的区别
上面提到的第一种改法是把random改成了urandom。两个有什么区别呢？

网上的结论是random更加安全，产生的是真正的随机数，而urandom是‘伪’随机数，可能没有random来的安全。

这些随机数是依赖噪声产生的，鼠标移动，键盘键入，I/O等就会产生噪声。

而为什么有urandom就变快了呢？这里就有个熵池的概念，`SHA1PRNG`算法根据熵池里的熵（我的理解熵就是随机数）来计算，
random是阻塞的，也就是说使用random的时候，如果熵池空了，那就会阻塞线程至熵池里的熵够用为止。而urandom是非阻塞的。

### 为什么是/dev/./urandom
据说是jdk的bug，如果不加`/./`的话，最后采用的还是random的。

### 关于熵池的补种
除了上面提到的两种办法，还有一种办法就是使用工具来补种，也就是补充熵池，可以使用`rng-tools`工具。
