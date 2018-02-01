---
title: git clone的时候显示Filename too long的解决办法
date: 2017-03-03 22:34:47
categories: git
---

在Git bash中，运行下列命令即可： 
```
git config --global core.longpaths true
```

如果只想对本次clone有效，只要在上述命令中去掉```--global```即可。