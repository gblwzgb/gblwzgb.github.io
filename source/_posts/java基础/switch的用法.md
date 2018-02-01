---
title: switch的用法
date: 2017-02-28 18:42:47
categories: Java基础
---

## 前言
switch-case语句完全可以与if-else语句互转，但通常来说，switch-case语句执行效率要高。

default在当前switch找不到匹配的case时执行。default并不是必须的。

一旦case匹配，就会顺序执行后面的程序代码，而不管后面的case是否匹配，直到遇见break。（所以这里有坑要避免）

# switch支持的类型
java7的switch支持一下几种类型

类型 |
---|---
char | 
Character | 
byte |
Byte |
int |
Integer |
short |
Short |
String(java7) |
enum(java5) |

# 代码实例
### default的使用
default在当前switch找不到匹配的case时执行。default并不是必须的。default并不一定要写在最后，但是好怪。。
```java
public class Test004 {
    public static void main(String[] args) {
        int i = 4;
        switch (i){
            case 1:      //这个值要和括号里变量的类型一样，不然编译报错
                System.out.print(1);
                break;
            default:
                System.out.print(0);
            case 5:
                System.out.print(5);
        }
    }
}
```
**输出结果:** 05  
因为default后面没有break，所以会执行case5。如前言里所说。

### switch使用枚举
```java
public class Test005 {

    static enum E {
        A, B, C, D
    }

    public static void main(String[] args) {
        E e = E.B;    //注意不要写成Enum e = E.b; 否则case那句会编译报错
        switch (e) {
            case A:
                System.out.println(1);
                break;
            default:
                System.out.println(0);
        }
    }

}
```

### 忘记写break的陷阱
```java
public class Test007 {
    public static void main(String[] args) {
        int i = 2;
        switch (i){
            case 1:
                System.out.print(1);
            case 2:
                System.out.print(2);
            case 3:
                System.out.print(3);
            default:
                System.out.print(0);
        }
    }
}
```
**输出结果:** 230  