---
title: Navicat for MySQL直接编辑Blob字段
date: 2017-03-24 12:34:47
categories: MySQL
---
今天项目中用到了Blob来存储数据，结果被中文乱码问题给坑了。  

<!-- more -->

## 开发过程

简化一下开发过程，如下。

**1、建表并插入数据**

先建了一张表，content字段使用Blob格式的。数据库都是UTF-8格式的。  
如下图，通过直接在Navicat for MySQL的备注里写入内容来新增一条基础数据。
![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-24/55141125-file_1490334430090_78dc.png)

**2、生成POJO**

Java代码里对应库表的model如下。
```java
public class News {

    private Integer id;

    private byte[] content;

    /**
     * 省略get/set
     */
}
```

**3、查询数据库并打印**

通过数据库连接，查询到这条数据。数据库连接里设置了UTF-8。  
代码直接简化成伪代码了。
```java
public static void main(String[] args) {
    News news = new News();  //从数据库读取数据
    System.out.println(new String(news.getContent()));
}
```
最后成功地在我的Win10上打印出了正确的中文。

## 出现乱码

然后代码提交测试，基础数据从我本机导出到.sql文件，然后插入到测试数据库内（也都是UTF-8的）。  
发现在Linux机器下，打印出的中文是乱码。

做出了以下两点修改，解决了中文乱码的问题。

**1、在byte数组转String的时候指定编码**

将代码改成如下。
```java
public static void main(String[] args) throws Exception{
    News news = new News();  //从数据库读取数据
    System.out.println(new String(news.getContent(), "UTF-8"));
}
```
然后我的Win10也乱码了。线上也是乱码。问题并没有得到解决。

**2、修改基础数据的维护方式**

基础数据不直接在Navicat for MySQL编辑。  

而是通过Java代码将String转成UTF-8编码格式的byte数组，然后插入到数据库中。

伪代码如下
```java
public static void main(String[] args) throws Exception{
    News news = new News();  //新增一条记录
    news.setContent(new String("中文", "UTF-8");
    mapper.insert(news);  //插入数据库
}
```
通过Java代码插入基础数据后，在Navicat for MySQL中查看。  

如下图所示，Blob显示的内容为乱码

![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-24/50795568-file_1490336844500_39c0.png)

基础数据重新维护后，中文乱码问题解决。  

本地和测试服务器的中文都显示正常了。

## 总结
### 原因分析
在Win10上的Navicat for MySQL通过`备注`查看Blob内容的时候，都是通过GBK格式的。

也就是说，如果直接在Navicat for MySQL通过`备注`直接编辑Blob的内容，并写入中文时，中文会被转码成GBK格式的二进制。

而我开始读取的时候读取的是GBK格式的二进制中文，  
然后我使用了`new String(byte[] bytes)`方法，由于忘了指定编码，所以JVM也使用了系统默认的GBK编码。  
所以开始的时候我的本地没有中文乱码问题的。而Linux系统默认编码不是GBK的，所以转码的时候出现了乱码。

然后我使用了`new String(bytes, "UTF-8")`指定了用UTF-8来将字节码转换。  
由于数据库保存的Blob是GBK格式的，转成了UTF-8就出现了乱码，所以我本地也就出现了乱码问题。

最后我通过代码插入UTF-8的Blob数据到数据库，解决了乱码问题。

### 经验
1. 不要直接在可视化工具里编辑含有Blob字段的库表。因为工具是无法确定编码的，工具帮你把中文转成blob的时候就有可能会有乱码问题。
2. Blob是无法指定编码的，因为里面存的是字节码，字符转字节码的过程要自己做，才能指定字符转字节的编码。不要在工具里做，这样才不会被坑。
3. 编码一定要统一。要了解乱码的本质。