---
title: ResponseEntity和@ResponseBody的区别
date: 2017-03-03 11:34:47
categories: Spring
---
## 问题
我在controller里定义了两个简单的handler来返回一个消息。
```java
@RequestMapping(value = "/message")
@ResponseBody
public Message get() {
    return new Message(penguinCounter.incrementAndGet() + " penguin!");
}
```
我也可以用下面这种形式
```java
@RequestMapping(value = "/message")
ResponseEntity<Message> get() {
    Message message = new Message(penguinCounter.incrementAndGet() + " penguin!");
    return new ResponseEntity<Message>(message, HttpStatus.OK);
}
```
那么这两种实现方式有什么区别呢？
## 答案
ResponseEntity可以更加灵活的在response里添加header信息。

通过[Spring官方文档](http://docs.spring.io/spring/docs/3.0.x/javadoc-api/org/springframework/http/ResponseEntity.html)里的第四个构造函数
```java
ResponseEntity(T body, MultiValueMap<String,String> headers, HttpStatus statusCode) 
```
比较常用的headers有Status,Content-Type和Cache-Control。

如果不需要自己设置，使用@ResponseBody会稍微简便一点。
