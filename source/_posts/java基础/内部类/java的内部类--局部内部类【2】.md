---
title: java的内部类--局部内部类【2】
date: 2017-03-07 11:34:47
categories: Java基础
---

```java
public class InnerClassDemo05 {
    public static void main(String[] args) {
        new Outer05().printLocalClass(true);
    }
}

class Outer05 {

    private String outerClassName = "Outer05";

    public void printLocalClass(final boolean isPrintOuterClassName) {
        //局部内部类前面不能有任何访问修饰符
        class LocalClass implements Runnable {
            private String localClassName = "localClass";

            public void run() {
                while (true) {
                    try {
                        Thread.sleep(3000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    /**
                     * 局部内部类可以访问外部类域和局部变量，局部变量必须是final类型的
                     */
                    if (isPrintOuterClassName) {
                        System.out.println(outerClassName + " " + localClassName);
                    } else {
                        System.out.println(localClassName);
                    }
                }
            }
        }

        new Thread(new LocalClass()).start();
    }
}
```
为什么方法一结束，程序还在不停的输出呢。  
我们用`javap -private Outer05\$1LocalClass.class`来反编译看看
![image](http://ok7wlv1ee.bkt.clouddn.com/17-1-23/77864015-file_1485159477248_128b7.png)  
我们看到了创建了一个局部变量的副本。  
并且这个副本会被当作参数传入局部内部类的构造方法中。  
为什么要final类型的局部变量呢？（在java8里面可以省略不写final）  
因为外部方法随时会结束掉，而内部类如果引用了局部变量，那不就出错了嘛。  
所以内部类创建了一个副本，而假设局部变量如果改变了的话，内部类的变量没变（副本），那用户不是很迷惑，所以要统一就用final。（不过改变引用对象内部的值那也是没办法统一的。。比如改变数组里的某个值）















