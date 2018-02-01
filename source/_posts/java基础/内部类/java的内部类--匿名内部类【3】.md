---
title: java的内部类--匿名内部类【3】
date: 2017-03-07 11:34:47
categories: Java基础
---

匿名内部类也就是没有名字的内部类

正因为没有名字，所以匿名内部类只能使用一次，它通常用来简化代码编写

但使用匿名内部类还有个前提条件：必须继承一个父类或实现一个接口

### 实例1:不使用匿名内部类来实现抽象方法

```java
abstract class Person {
    public abstract void eat();
}
 
class Child extends Person {
    public void eat() {
        System.out.println("eat something");
    }
}
 
public class Demo {
    public static void main(String[] args) {
        Person p = new Child();
        p.eat();
    }
}
```
**运行结果：** eat something

可以看到，我们用Child继承了Person类，然后实现了Child的一个实例，将其向上转型为Person类的引用

但是，如果此处的Child类只使用一次，那么将其编写为独立的一个类岂不是很麻烦？

这个时候就引入了匿名内部类

### 实例2：匿名内部类的基本实现
```java
abstract class Person {
    public abstract void eat();
}
 
public class Demo {
    public static void main(String[] args) {
        Person p = new Person() {
            public void eat() {
                System.out.println("eat something");
            }
        };
        p.eat();
    }
}
```
**运行结果：** eat something

可以看到，我们直接将抽象类Person中的方法在大括号中实现了

这样便可以省略一个类的书写

并且，匿名内部类还能用于接口上

### 实例3：在接口上使用匿名内部类
```java

interface Person {
    public void eat();
}
 
public class Demo {
    public static void main(String[] args) {
        Person p = new Person() {
            public void eat() {
                System.out.println("eat something");
            }
        };
        p.eat();
    }
}
```
由上面的例子可以看出，只要一个类是抽象的或是一个接口，那么其子类中的方法都可以使用匿名内部类来实现

最常用的情况就是在多线程的实现上，因为要实现多线程必须继承Thread类或是继承Runnable接口

### 实例4：在匿名内部类中自定义方法
```java
public class InnerClassDemo06 {
    public static void main(String[] args) {
        new Thread(){
            @Override
            public void run() {
                get();
            }

            private void get(){
                System.out.println(111);
            }
        }.get();
    }
}
```
**运行结果：** 111

可以看到可以定义自己的方法，并且就算是private的方法也可以访问到。   
但是别向上转型，即`Thread t = new Thread(){};`  
因为Thread里并没有get()方法。

# 总结
1. 匿名内部类必须是基于一个抽象类或接口的。
2. 匿名内部类是可以定义其他方法的。
3. 匿名内部类是局部内部类的一种，所以局部内部类的所有限制都对其生效。
4. 匿名内部类不能有static的变量，方法。
5. 匿名内部类不能有构造方法，但是可以通过构造块{}来初始化。










