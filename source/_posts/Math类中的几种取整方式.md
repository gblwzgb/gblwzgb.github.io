---
title: Math类中的几种取整方式
date: 2017-02-28 18:42:47
categories: Java基础
---

方法名 | 作用
---|---
ceil | 天花板，即向上取整，结果大于等于原值
floor | 地板，即向下取整，结果小于等于原值
round | 四舍五入，算法为Math.floor(x+0.5)

### 代码示例
```java
public class test001 {

    public static void main(String[] args) {
        System.out.println(Math.floor(-10.4));  //-11.0
        System.out.println(Math.floor(-10.6));  //-11.0
        System.out.println(Math.floor(10.4));   //10.0
        System.out.println(Math.floor(10.6));   //10.0
        
        System.out.println(Math.ceil(-10.4));  //-10.0
        System.out.println(Math.ceil(-10.6));  //-10.0
        System.out.println(Math.ceil(10.4));   //11.0
        System.out.println(Math.ceil(10.6));   //11.0
        
        System.out.println(Math.round(-10.1));  //-10
        System.out.println(Math.round(-10.5));  //-10
        System.out.println(Math.round(-10.9));  //-11
        System.out.println(Math.round(10.1));   //10
        System.out.println(Math.round(10.5));   //11
        System.out.println(Math.round(10.9));   //11
    }

}
```