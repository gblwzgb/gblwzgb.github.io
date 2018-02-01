---
title: Spring配置欢迎页引发的思考
date: 2017-03-14 11:34:47
categories: Spring
---
# 引言
今天下载了[一个demo项目](https://github.com/fankay/ssmVue)，跑起来可以默认打开index.jsp的欢迎页。但是把index.jsp换成index.html就会404。平时也没配置过欢迎页，就顺手研究了一下。于是就查漏补缺了以下知识点。
![image](http://i1.piimg.com/567571/e07267f47dba6cc6.png)

# 项目概述
1. 项目中的web.xml
```
  <servlet>
    <servlet-name>mvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>mvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```
2. 项目的web.xml里没有配置`<welcome-file-list>`


# 知识点
### viewResolver

开始我以为是这个bean的问题，因为我看到这个bean的配置里有个`.jsp`，后来才知道这个bean是在请求处理完以后，返回ModelAndView，把数据渲染到页面里用的。

### 三种index.html可行的方法
1. 使用标签`<mvc:default-servlet-handler/>`

    在xxxx-servlet.xml里定义这个标签。这个标签的作用是，当返回404的时候，就使用默认的servlet去处理这个请求。所以当使用index.html的时候，先经过DispatcherServlet，发现没有对用的handler，于是返回404，然后被默认的servlet处理，成功显示index.html的内容。
    
    使用这个标签的时候，务必加上`<context:annotation-config />`，不然就会发现普通的请求过不了了。[原因可以参考这里](http://www.cnblogs.com/hujingwei/p/5349983.html)

2. 使用标签`<mvc:resource mapping="xx" location=""/>`

    在xxxx-servlet.xml里定义这个标签。这个标签的作用是，当访问的是静态资源的时候，并且被mapping匹配到的话，都会把位置定位到相对路径location下。

3. 定义一个使用default的servlet-mapping
    
    ```
    <servlet-mapping>
	    <servlet-name>default</servlet-name>
	    <url-pattern>*.html</url-pattern>
    </servlet-mapping>
    ```
    在项目的web.xml里加上这段代码也可以实现。因为index.html直接就匹配了default的servlet了。

### /和/*的区别
    
如果我把DispatcherServlet的匹配路径从`/`改成`/*`，就会发现连index.jsp也404了。这是为什么呢？

`/*`会匹配所有请求，如果请求没被其他servlet匹配走，那就都会走这个servlet，这种写法不是很推荐。还有个比较神奇的问题就是，当写成`/*`的时候，就不会去默认访问`<welcome-file-list>`里的欢迎页了。只有在访问的时候手动输入欢迎页的请求地址才能打开。

`/`呢，就是一个请求进来以后，所有的servlet都不匹配。就会通过`/`的servlet来实现。这种写法也不是很推荐的。

这里就要说到`<url-pattern>`的匹配优先级问题了。

### `<url-pattern>`的匹配优先级

精确路径>最长路径>扩展名

精确路径：类似`<url-pattern>/demo.html</url-pattern>`

路径匹配：类似`<url-pattern>/xxx/xx/x/*</url-pattern>`

扩展名匹配：类似`<url-pattern>*.jsp</url-pattern>`

路径匹配和扩展名匹配是不能同时使用的。比如`<url-pattern>/*.jsp</url-pattern>`就是一种错误的用法。

### 相同的`<url-pattern>`的问题

在同一个web.xml内，是不允许使用一样的`<url-pattern>`，否则编译也不会通过。

但是Tomcat的web.xml和项目里的web.xml如果定义了相同的`<url-pattern>`是可以的，如果发生这种情况，那么会匹配项目里的`<url-pattern>`。

如果Tomcat的web.xml和项目的web.xml里都配置`<welcome-file-list>`了的话，那只有项目里的会生效，而不是把两个`<welcome-file-list>`合并起来。

# 结论
### 先说这个项目的index.jsp是怎么实现欢迎页的
因为Tomcat的web.xml里配置了
```
<welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```
当访问http://127.0.0.1:8080的时候，就会默认加上后缀，也就是会请求http://127.0.0.1:8080/index.html，如果404的话，就请求http://127.0.0.1:8080/index.htm，以此类推。  

如果项目的web.xml里也配置`<welcome-file-list>`标签的话，Tomcat里的会被覆盖。

当请求http://127.0.0.1:8080/index.jsp的时候，因为匹配了定义在Tomcat的web.xml里的处理jsp的Servlet，所以显示成功了。

### 为什么把index.jsp换成index.html就不行了呢？
因为http://127.0.0.1:8080/index.html匹配的是DispatcherServlet，而controller里找不到对应的handler，所以报错404了。

# 结语

可能点比较多，描述的比较乱，其实弄清楚`<url-pattern>`的匹配优先级就基本能理解为什么index.html默认是不行的了。然后知道Tomcat的web.xml里有个处理jsp的jsp-servlet，和一个名叫default的可以处理静态资源的servlet就行了。