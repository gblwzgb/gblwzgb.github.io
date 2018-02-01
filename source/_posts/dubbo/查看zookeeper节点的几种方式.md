---
title: 查看zookeeper节点的几种方式
date: 2017-03-28 11:34:47
categories: dubbo
---

在使用dubbo的时候，有时候会报一个`Please check registry access list (whitelist/blacklist)`的异常。一般报这个错误就是说你在调用的这个服务找不到provider了。

这个时候如果想查看一个zookeeper上注册的节点有没有这个服务的话。有以下几种方式。

<!-- more -->

## 1. 直接通过命令行查看
在Linux环境下，cd进入zookeeper的bin目录下，执行下面的命令

`./zkCli.sh -server 192.168.0.12:2181`

成功后就会进入zookeeper的命令行了。如下图。
![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-28/5352397-file_1490678243046_11d10.png)

输入`help`可以查看有哪些命令

![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-28/40299390-file_1490678454707_8da5.png)

这里我们选用`ls`命令来查看节点。比如`ls /dubbo/xx.xxx.xxxx/providers`。这条命令会列出该路径下的所有节点。

如果要删除节点的话，就使用`delete`命令，比如`delete /dubbo/xxx.xxx.xxxx/providers`。用`delete`删除节点的时候，如果有子节点，必须先删除子节点，才能删除该节点。当然你也可以使用`rmr`来递归删除，不过挺危险的。

**这种方式打印出来的节点是没有格式化过，全部挤在一起，完全看不了。而且节点是被encode过的，更加不美观。**

## 2. 用Java程序遍历
```java
import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooKeeper;

import java.net.URLDecoder;
import java.util.List;

public class Zoo {

    private static final String connectString = "192.168.0.12:2181";

    private static final int sessionTimeout = 2000;

    public static void main(String[] args) throws Exception {

        String path = "/dubbo/xx.xxx.xxxx/providers";

        ZooKeeper zk = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            // 监控所有被触发的事件
            public void process(WatchedEvent event) {
                System.out.println("已经触发了" + event.getType() + "事件！");
            }
        });

        //获取路径下的节点
        List<String> children = zk.getChildren(path, false);
        for (String pathCd : children) {
            System.out.println(URLDecoder.decode(pathCd, "UTF-8"));  //记得转码
        }

    }
}
```

**这种方式查看还行，但也挺麻烦。不过可以用这个来遍历删除节点。**

## 3. 使用dubbokeeper
使用开源项目[dubbokeeper](https://github.com/dubboclub/dubbokeeper)，有可视化界面。