---
title: git在push的时候显示the remote end hung up的解决办法 long的解决办法
date: 2017-03-04 00:00:01
categories: git
---
今天在家华数网push的时候，一会提示

> fatal: Could not read from remote repository

一会提示

> The remote end hung up unexpectedly

就是push不成功。开始还以为是家里电脑没配好github的公钥。

但是可以pull成功的，排除这种可能。

后来尝试只修改一点点文件，发现可以push成功。

## 解决方法
最后发现是华数网太坑了。。网速贼慢导致的push失败。。因为博客生成的静态文件太多，网速又慢，相当于git提交超时了。。略微设置一下就解决了这个问题了。

在git bash里运行这两句命令即可。
> git config --global http.lowSpeedLimit 0

> git config --global http.lowSpeedTime 999999

单位都是秒。