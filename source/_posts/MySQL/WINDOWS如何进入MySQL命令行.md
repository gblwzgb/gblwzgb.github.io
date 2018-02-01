---
title: WINDOWS如何进入MySQL命令行
date: 2017-04-12 14:34:47
categories: MySQL
---
平时查看数据库都用工具Navicat for MySQL。

今天想通过命令行来实践一下MySQL的事务隔离性，发现竟然不知道怎么进入命令行，记录一下吧。

<!-- more -->

准备工作：

安装MySQL，配置环境变量，启动MySQL。这些不多说。

1. 通过WIN+R打开CMD窗口
    - 不知道为什么，git提供的黑窗口进不了MySQL命令行。

2. 输入`mysql -h127.0.0.1 -uroot -proot`即可
    - `-h`后面是数据库连接的地址
    - `-u`后面是数据库的用户名
    - `-p`后面是数据库的密码

3. 通过`show databases;`查看所有的库
    - 命令都必须以`;`作为结尾

4. 输入`exit`或通过快捷键`Ctrl+C`退出MySQL命令行 
