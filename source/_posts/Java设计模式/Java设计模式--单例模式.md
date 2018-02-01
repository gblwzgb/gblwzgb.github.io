---
title: Java设计模式--单例模式
date: 2017-03-03 18:42:47
categories: Java设计模式
---

## 单例模式介绍
使用单例可以减少不必要的系统开销，比如Spring里配置的数据源，创建和销毁耗费大量的资源，所以就用单例来保证项目加载的时候只生成一个实例，大家公用该实例。Spring的bean默认都是Singleton的。

单例模式有以下特点：
1. 单例类只能有一个实例。
2. 单例类必须自己创建自己的唯一实例。
3. 单例类必须给所有其他对象提供这一实例。

## 单例模式的写法
我画了张图来总结一下我所知道的单例模式的写法。
![image](http://ok7wlv1ee.bkt.clouddn.com/17-3-1/94738122-file_1488378897476_bff8.png)

具体代码演示

### 懒汉式
```java
public class SingletonDemo01 {

    private static SingletonDemo01 instance = null;

    private SingletonDemo01() {

    }

    public static SingletonDemo01 getInstance() {
        if (instance == null) {
            instance = new SingletonDemo01();
        }
        return instance;
    }

}
```
这个有线程安全问题：线程A判断完实例没有创建，刚刚准备创建对象的时候，时间片切换到第二个线程B，这个时候线程B也发现实例没有创建，然后两个线程就各创建了一个对象了。

### 饿汉式
```java
public class SingletonDemo02 {

    private static SingletonDemo02 instance = new SingletonDemo02();

    private SingletonDemo02() {
        //设置私有构造外部就不能通过new来新建实例了。
    }

    public static SingletonDemo02 getInstance() {
        return instance;
    }

}
```
**优点**：

饿汉模式简单，也没有线程安全问题。

了解过类加载机制的同学可能要问，如果线程A和线程B同时触发了类加载机制怎么办呢？会不会在线程切换间创建两个对象？没关系，在jvm里，类加载的时候会自动的加锁，并且有CAS保证了只有一个对象被创建。具体细节这里不展开。

**缺点**：

如果我的类里定义了其他静态方法或静态变量，我只是想用一下静态方法或变量，依据getstatic指令触发类的初始化，这个时候，instance变量会跟着一起初始化，也就创建了一个SingletonDemo02的对象，白白浪费了内存。

### 单检锁
```java
public class SingletonDemo03 {

    private static SingletonDemo03 instance = null;

    private SingletonDemo03() {

    }

    public synchronized static SingletonDemo03 getInstance() {
        if (instance == null) {
            instance = new SingletonDemo03();
        }
        return instance;
    }

}
```
**优点**:

线程安全。

在懒汉式上加上了synchronized关键字，也就解决了普通懒汉式线程不安全的问题。

**缺点**：

锁的粒度太粗，instance==null的判断并不需要加锁。

### 双检锁
```java
public class SingletonDemo04 {

    private static volatile SingletonDemo04 instance = null;

    private SingletonDemo04() {

    }

    public synchronized static SingletonDemo04 getInstance() {
        if (instance == null) {
            synchronized (SingletonDemo04.class) {
                if (instance == null) {
                    instance = new SingletonDemo04();
                }
            }
        }
        return instance;
    }

}
```
jvm创建对象的时候大致干三件事：  
1. 在堆上分配内存空间
2. 执行类的构造方法初始化参数
3. 把创建的对象指向分配的内存空间地址

在编译优化里，这几步的执行顺序并不是一定的，可能是123，也可能是132。

这里的volatile是为了防止132运行的时候，别的线程发现对象不为null，然后直接去操作对象里的某些参数。

**优点**:

线程安全，锁的粒度小

**缺点**：

多线程学的不扎实的可能很难理解。

### 静态内部类
```java
public class SingletonDemo05 {

    private SingletonDemo05() {

    }

    private static final class SingletonManager {
        private static SingletonDemo05 instance = new SingletonDemo05();
    }

    public static SingletonDemo05 getInstance() {
        return SingletonManager.instance;
    }

}
```
**优点**:

改进了饿汉式的缺点，可以发现，现在即使SingletonDemo05里有其他的静态方法，只要我不调用getInstance()方法，我就不会白白的创建一个对象了。

### 枚举
```
public enum SingletonDemo06 {

    INSTANCE;
    
    private Resource instance;

    SingletonDemo06() {
        instance = new Resource();
    }

    public Resource getInstance() {
        return instance;
    }

}

class Resource {
}
```
**优点**：

前面的几种方式都可以通过反射和反序列化破坏掉

由于枚举类型的特性，保证了线程安全、反射安全和反序列化安全

在《Effective Java》书中有一句话是：

> 单元素的枚举类型已经成为实现Singleton的最佳方法。