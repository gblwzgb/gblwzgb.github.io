---
title: java的内部类--成员内部类【1】
date: 2017-03-07 11:34:47
categories: Java基础
---
## 例子一
```java
public class InnerClassDemo01 {

    public static void main(String[] args) {
        OuterClass.InnerClass innerClass = new OuterClass().new InnerClass();
        innerClass.print();
        new Thread(innerClass).start();
    }

}

class OuterClass {

    private String name;

    public String getName() {
        return name;
    }

    public class InnerClass implements Runnable {

        public InnerClass() {
            name = "lxl";    //注意不要用this.name，this指的是内部类的。
        }

        public void run() {
            System.out.println(name);
        }

        public void print() {
            System.out.println(getName());
        }
    }
}
```
这个例子可以学到：  
- 内部类可以随意的使用外部类的属性或方法。即使是private的。  
- 如何获取成员内部类的实例，即：`OuterClass.InnerClass innerClass = new OuterClass().new InnerClass()`。
- 内部类也是可以继承或实现接口的。

## 例子二
```java
public class InnerClassDemo02 {
    public static void main(String[] args) {
        new OuterClass02().new InnerClass02(); //编译报错
    }
}

class OuterClass02 {

    private class InnerClass02 {
        static {
            //编译报错
        }
    }
    
    public OuterClass02 (){
        new InnerClass02();
    }
}
```
这个例子可以学到：
- 成员内部类和方法还有变量一样，也是被访问修饰符所限制住的。
    - 我试着在外部类加了个get方法，返回一个内部类的实例。但是返回了实例发现也调用不了内部类的public方法。而且声明一个内部类也是报错的。 
- 成员内部类里不能有static的属性，方法，静态块。

## 例子三
```java
public class InnerClassDemo03 {
    public static void main(String[] args) {
        OuterClass03 outer = new OuterClass03();
        OuterClass03.InnerClass03 inner = outer.new InnerClass03();
        System.out.println(outer == inner.getOuterClass());  //true
        System.out.println(outer == inner.getNewOuterClass());  //false
    }
}

class OuterClass03 {

    public class InnerClass03 {
        //获取外部类的引用
        public OuterClass03 getOuterClass() {
            return OuterClass03.this;
        }

        public OuterClass03 getNewOuterClass() {
            return new OuterClass03();
        }
    }
}
```
这个例子可以学到：
- 如何获取当前外部类的引用，貌似没什么卵用。  
补充：在ArrayList的源码中看到，迭代器使用了内部类，并且通过```ArrayList.this.elementData```来获取外部类的引用，并获取到外部类的值。

## 例子四
我们已经知道如何获取一个内部类的实例
```java
public static void main(String[] args) {
    new OuterClass().new InnerClass();
}
```
来看下反编译以后的代码
```java
public static void main(String[] args) {
    OuterClass var10002 = new OuterClass();
    var10002.getClass();
    new InnerClass(var10002);
}
```
由于没有声明外部类的名字，所以反编译出来的名字是java生成的。  

可以看到内部类其实是拿到了外部类的引用的。所以内部类可以轻松的访问到外部类的方法和属性。

## 例子五
```java
public class InnerClassDemo04 {
    public static void main(String[] args) {
        new OuterClass04();
    }
}

class OuterClass04 {

    private String name;

    //定义个内部类
    public class InnerClass04 {

        private String name;

        private void print() {
            System.out.println(name);
        }
    }

    public OuterClass04() {
        InnerClass04 innerClass = new InnerClass04();
        innerClass.name = "lxl";
        innerClass.print();  //输出：lxl
    }

}
```
这个例子可以学到：  
- 外部类要调用内部类的方法就必须要拿到一个内部类的实例。而且外部类可以调用内部类的private方法。
- 在外部类里声明内部类可以省略外部类的类名，java会自动加上。
- 内部类的属性可以和外部类的属性重名，java会默认加上this . name，如果想用父类的那个，就可以用例子三了。






