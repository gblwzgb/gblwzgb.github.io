---
title: java的内部类--静态内部类【4】
date: 2017-03-07 11:34:47
categories: Java基础
---

静态内部类和成员内部类差不多。
```java
public class InnerClassDemo07 {
    public static void main(String[] args) {
        Outer07.Inner07 inner07 = new Outer07.Inner07();
    }
}

class Outer07 {

    static class Inner07 {
        public Inner07() {
            System.out.println("init");
        }
    }
}
```

# 总结
- 静态内部类只能访问外部类的静态方法或变量
- 静态内部类的创建形式是：`OuterClass.InnerClass inner = new OuterClass.InnerClass()`