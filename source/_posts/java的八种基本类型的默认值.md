---
title: java的八种基本类型的默认值
date: 2017-02-28 18:42:47
categories: Java基础
---

### 参见表格
基本类型 | 默认值
---|---
byte|	0
short|	0
int|	0
long|	0L
float|	0.0f
double|	0.0d
char|	'\u0000'
boolean|	false

### 代码
```java
/**
 * 实验基本类型的默认值
 */
public class Test002 {

    private static byte defaultByte; //如果是final类型的话就必须初始化了，没有默认值
    private static short defaultShort;
    private static int defaultInt;
    private static long defaultLong;
    private static float defaultFloat;
    private static double defaultDouble;
    private static char defaultChar;
    private static boolean defaultBoolean;
    private static String str;

    public static void main(String[] args) {
        System.out.println(defaultByte);
        System.out.println(defaultShort);
        System.out.println(defaultInt);
        System.out.println(defaultLong);
        System.out.println(defaultFloat);
        System.out.println(defaultDouble);
        System.out.println(defaultChar);
        System.out.println(defaultBoolean);
        System.out.println("输出" + str);
    }
}
```
**输出**  
0  
0  
0  
0  
0.0  
0.0  
 
false  
输出null

### 注意点
默认值只有成员变量才有，局部变量是没有默认值的。


