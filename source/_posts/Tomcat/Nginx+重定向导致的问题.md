---
title: Nginx+重定向导致的问题
date: 2017-03-07 11:34:47
categories: Tomcat
---
## 场景描述
在做项目迁移的时候，有个bug是页面跳转以后显示不出来：  
 ```java
 response.sendRedirect("/xxx/xxx.htm?action=xxx");
```

原因是虽然Nginx设置了https的协议，但是根据下面的图片来看，response.sendRedirect()以后请求被重定向到http上了。  
![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-7/16359046-file_1488855118533_754c.png)

[查了资料](http://feitianbenyue.iteye.com/blog/2056357)

## 解决方法：

1. 找运维人员nginx配置： 
```
proxy_set_header       Host $host;  
proxy_set_header  X-Real-IP  $remote_addr;  
proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;  
proxy_set_header X-Forwarded-Proto  $scheme;  
```
2. 在tomcat的server.xml里的Engine模块下加入：  
```
<Valve className="org.apache.catalina.valves.RemoteIpValve" protocolHeader="X-Forwarded-Proto" />
```


