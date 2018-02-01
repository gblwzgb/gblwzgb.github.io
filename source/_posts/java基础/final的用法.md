---
title: final的用法
date: 2017-02-28 18:42:47
categories: Java基础
---

用final可以修饰类，方法，变量。分别表示类不可被继承，方法不可重写，变量不可变（不可变指的是引用地址不可变，内部的值还是可以变的）。

# 代码实例
### final修饰的成员变量
被final修饰的类变量就变成了常量，必须要进行初始化，有两次机会可以初始化，一是声明的时候直接初始化，或者在构造函数里初始化。
```java
public class Test008 {

    final int PI;  //编译报错，未初始化

    public Test008(){
        
    }

    public Test008(int i){
        PI = i;
    }

    public static void main(String[] args) {

    }
}
```
这段代码是有编译报错的，因为new Test008()的时候，PI是没有被初始化的。所以要嘛在无参构造里也加上初始化，要嘛删掉这个无参构造。

### final修饰的局部变量
```java
public class Test008 {
    public static void main(String[] args) {
        final int i;
        System.out.println(i);  //编译报错，未初始化
    }
}
```
局部变量和成员变量不一样在，可以声明成final且不初始化，但是在使用的地方会编译报错。

### final定义的基本类型的运算
Java表达式转型规则由低到高转换：

1. 所有的byte,short,char型的值将被提升为int型；
2. 如果有一个操作数是long型，计算结果是long型；
3. 如果有一个操作数是float型，计算结果是float型；
4. 如果有一个操作数是double型，计算结果是double型；
5. 被final修饰的两个常量运算会直接在编译期间获得值；

####  例一
```java
public class Test {
    public static void main(String... args) {
        byte b1 = 1, b2 = 2, b3;
        b3 = (byte) (b1 + b2);  //需要强转，因为计算结果为int
    }
}
```
反编译结果
```
  public static void main(java.lang.String[]);
    Code:
       0: iconst_1   //整数1入栈，操作数栈【1】
       1: istore_1   //弹出栈顶元素，赋值给索引为1的变量，操作数栈【】
       2: iconst_2   //整数1入栈，操作数栈【2】
       3: istore_2   //弹出栈顶元素，赋值给索引为2的变量，操作数栈【】
       4: iload_1    //索引为1的变量的值入栈，操作数栈【1】
       5: iload_2    //索引为2的变量的值入栈，操作数栈【1|2】
       6: iadd       //栈顶的两个元素相加，操作数栈【3】
       7: i2b        //把int型转成byte型，操作数栈【3】
       8: istore_3   //弹出栈顶元素，赋值给索引为3的变量，操作数栈【】
       9: return

```

####  例二
```java
public class Test {
    public static void main(String... args) {
        final byte b1 = 1, b2 = 2;
        byte b3 = b1 + b2;  //不需要强转，因为这就是byte b3 = 3;
    }
}
```
反编译结果
```
  public static void main(java.lang.String[]);
    Code:
       0: iconst_1 //将一个int型常量值推送至栈顶，操作数栈【1】
       1: istore_1 //弹出栈顶元素，赋值给索引为1的变量，操作数栈【】
       2: iconst_2 //将一个int型常量值推送至栈顶，操作数栈【2】
       3: istore_2 //弹出栈顶元素，赋值给索引为2的变量，操作数栈【】
       4: iconst_3 //将一个int型常量值推送至栈顶，操作数栈【3】
       5: istore_3 //弹出栈顶元素，赋值给索引为3的变量，操作数栈【】
       6: return

```

####  例三
```java
public class Test {
    public static void main(String... args) {
        final byte b1 = 127, b2 = 3;
        byte b3 = (byte) (b1 + b2);  //需要强转，因为这就是byte b3 = 130;这是不合法的
    }
}
```
反编译结果
```
  public static void main(java.lang.String[]);
    Code:
       0: bipush        127  //将一个byte型常量值推送至栈顶，操作数栈【127】
       2: istore_1           //弹出栈顶元素，赋值给索引为1的变量，操作数栈【】
       3: iconst_3           //将一个int型常量值推送至栈顶，操作数栈【3】
       4: istore_2           //弹出栈顶元素，赋值给索引为2的变量，操作数栈【】
       5: bipush        -126 //将一个byte型常量值推送至栈顶，操作数栈【-126】
       7: istore_3           //弹出栈顶元素，赋值给索引为3的变量，操作数栈【】
       8: return

```
这里为什么是bipush呢？因为-1 ~ 5使用iconst_m1 ~ iconst_5来入栈的。

#### 例四
```java
public class Test0 {
    public static void main(String[] args) {
        final byte b1 = 2;
        byte b2 = 3;
        byte b3 = (byte) (b1 + b2);  //需要强转
    }
}
```

```
  public static void main(java.lang.String[]);
    Code:
       0: iconst_2 //将一个int型常量值推送至栈顶，操作数栈【2】
       1: istore_1 //弹出栈顶元素，赋值给索引为1的变量，操作数栈【】
       2: iconst_3 //将一个int型常量值推送至栈顶，操作数栈【3】
       3: istore_2 //弹出栈顶元素，赋值给索引为2的变量，操作数栈【】
       4: iconst_2 //将一个int型常量值推送至栈顶，操作数栈【2】
       5: iload_2  //索引为1的变量的值入栈，操作数栈【2|3】
       6: iadd     //栈顶的两个元素相加，操作数栈【5】
       7: i2b      //把int型转成byte型，操作数栈【5】
       8: istore_3 //弹出栈顶元素，赋值给索引为3的变量，操作数栈【】
       9: return
```

可以发现，例二和例三少了一个iadd的过程，final直接在编译阶段就计算出来了。

从例四可以看出，一个final类型和非final类型的数相加，final类的数是从常量池里取了以后压入栈的，而非final类的数是从变量里load了以后压入栈的。

#### 例五
```java
public class Test {
    public static void main(String[] args) {
        int i = 10;
        double d = 7.0;
        float f = (float) (i % d);
        System.out.println(f);
    }
}
```
反编译后
```
  public static void main(java.lang.String[]);
    Code:
       0: bipush        10
       2: istore_1
       3: ldc2_w        #2                  // double 7.0d  //将long或double型常量值从常量池中推送至栈顶（宽索引）
       6: dstore_2
       7: iload_1
       8: i2d       //栈顶int值强转double值，并且结果进栈
       9: dload_2
      10: drem      //栈顶两double型数值作取模运算，并且结果进栈
      11: d2f       //栈顶double值强转float值，并且结果进栈
      12: fstore        4  //这里的4指的是第四个变量，fstore_1指的是第二个变量。
      14: getstatic     #4                  // Field java/lang/System.out:Ljava/io/PrintStream;
      17: fload         4
      19: invokevirtual #5                  // Method java/io/PrintStream.println:(F)V
      22: return
}
```
可以看到，运算的时候，把int转成了double的，符合了小转大的规则；