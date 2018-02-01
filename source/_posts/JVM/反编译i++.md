---
title: 反编译i++
date: 2017-03-10 11:34:47
categories: JVM
---
通过反编译来研究一下java的指令集

## i = i++
```java
public class Test {
    public static void main(String... args) {
        int i = 0;
        i = i++;
        System.out.println(i);  //0
    }
}
```
使用javac编译后再使用`javap -c Test`反编译这个类查看它的字节码，如下（只摘取main方法）：
```java
public static void main(java.lang.String[]);
Code:
0: iconst_0
1: istore_1
2: iload_1
3: iinc 1, 1
6: istore_1
7: getstatic #2; //Field java/lang/System.out:Ljava/io/PrintStream;
10: iload_1
11: invokevirtual #3; //Method java/io/PrintStream.println:(I)V
14: return
```

这里，我从第0行开始分析（分析中【】表示栈，栈的底端在左边，顶端在右边）：

- 0：将常数0压入栈，栈内容：【0】
- 1：将栈顶的元素弹出，也就是0，保存到局部变量区索引为为1（也就是变量i）的地方。栈内容：【】
- 2：将局部变量区索引为1（也就是变量i）的值压入栈，栈内容：【0】
- 3：将局部变量区索引为1（也就是常量i）的值加一，此时局部变量区索引为1的值（也就是i的值）是1。栈内容：【0】
- 6：将栈顶元素弹出，保存到局部变量区索引为1（也就是i）的地方，此时i又变成了0。栈内容：【】
- 7：获取常量池中索引为2所表示的类变量，也就是System.out。栈元素：【】
- 10：将局部变量区索引为1的值（也就是i）压入栈。栈元素：【0】
- 11：调用常量池索引为3的方法，也就是System.out.println
- 14：返回main方法

## i = ++i
```java
public class Test {
    public static void main(String... args) {
        int i = 0;
        i = ++i;
        System.out.println(i);  //1
    }
}
```
同样`javap -c Test`
```java
  public static void main(java.lang.String...);
    Code:
       0: iconst_0   //0入栈，【0】
       1: istore_1   //栈顶出栈，并赋值给索引为1的变量，【】
       2: iinc          1, 1    //索引为1的变量+1，【】
       5: iload_1    //索引为1的变量入栈，【1】
       6: istore_1   //栈顶出栈，并赋值给索引为1的变量，【】
       7: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      10: iload_1
      11: invokevirtual #3                  // Method java/io/PrintStream.println:(I)V
      14: return
```

## i = i++ + ++i
```java
public class Test {
    public static void main(String... args) {
        int i = 0;
        i = i++ + ++i;
        System.out.println(i);  //2
    }
}
```
同样`javap -c Test`
```java
  public static void main(java.lang.String...);
    Code:
       0: iconst_0   //0入栈，【0】
       1: istore_1   //栈顶出栈，并赋值给索引为1的变量，【】
       2: iload_1    //索引为1的变量入栈，【0】
       3: iinc          1, 1    //索引为1的变量+1，【0】
       6: iinc          1, 1    //索引为1的变量+1，【0】
       9: iload_1    //索引为1的变量入栈，【0|2】
      10: iadd       //栈顶的两个整型相加并入栈，【2】
      11: istore_1   //栈顶出栈，并赋值给索引为1的变量，【】
      12: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      15: iload_1
      16: invokevirtual #3                  // Method java/io/PrintStream.println:(I)V
      19: return
```

## 通过图片理解指令
int a = 100;

int b = 98;

int c = a+b;

![image](http://images.cnitblog.com/i/535328/201403/111017360158953.jpg)
