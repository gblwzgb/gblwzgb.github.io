---
title: java的八种基本类型~byte
date: 2017-02-28 18:42:47
categories: Java基础
---

1. byte可表示的位数
2. byte与int互转
3. byte的包装类
4. byte与字节流（TODO）


# byte可表示的位数

> 
> ```math
> -2^7
> ```
> 
> ```math
> \downarrow
> ```
> 
> ```math
>  2^7-1
> ```

 

byte在java中是一字节的，而一字节在内存中是8bit，而8bit中，  
第一位又是符号位，那只有7位可以表示数字，所以
- byte能表示的最大的正数在内存中是0111 1111，即 127
- byte能表示的最小的负数在内存中是1000 0000，即-128  

可以用byte的包装类Byte.java的静态常量来查看
```
public class Test01 {
    public static void main(String[] args) {
        System.out.println(Byte.MIN_VALUE + "~" + Byte.MAX_VALUE);  //输出：-128~127
        byte b = 128; //error，编译报错
        byte c = 127; //编译通过
        byte d = (byte)128; //编译通过
    }
}
```
  
# byte与int互转
- ## byte转int
好像也没什么需要注意的...
```
public class Test01 {
    public static void main(String[] args) {
        /**
         * int在byte范围内
         */
        byte b = 10;
        System.out.println(b + 10);  //输出： 20
        int b1 = b;
        System.out.println(b1);  //输出： 10
        /**
         * int不在byte范围内
         */
        byte c = 127;
        System.out.println(c + 10);  //输出： 137
        int d = c + 10;
        System.out.println(d);  //输出： 137，与上一句是一样的，java底层是int计算的。
    }
}
```

- ## int转byte
```
public class Test01 {
    public static void main(String[] args) {
        int i = 128;
        byte j = (byte) i;  //不强转编译报错
        System.out.println(j);  //输出： -128
        int m = -129;
        byte n = (byte) m;  //不强转编译报错
        System.out.println(n);  //输出： 127
        int a = 127;
        byte b = (byte) a;  //不强转编译报错
        System.out.println(b);  //输出： 127
        byte e = 127;  //这里字面量是int，为什么不用强转呢，jvm做了处理
        System.out.println(e);  //输出： 127
        byte c = 127;
        byte d = 1;
        byte e = c + d;  //error，编译报错，必须加上强转。因为java计算是通过int的。
    }
}
```
[TODO:计算一下为什么128变成了-128]

# byte的包装类
byte的包装类就是Byte，列举一下常见方法

```
- toString(byte b)  //静态的，调用了Integer.toString()
- ByteCache() //不是很懂，也不是静态方法啊
- parseByte(String s) //超过范围或者不是数字会报：NumberFormatException，调用了Integer.parseInt()
- valueOf() //把括号里的参数变成Byte对象，超过范围或者不是数字会报：NumberFormatException
- byteValue() //返回Byte里的byte
- shortValue() //返回short，代码里加了强转，平时不写是隐式类型转换，jvm实现的。
- intValue(),longValue() //同上
- floatValue(),doubleValue() //同上，但是不知道精度是否会丢失【TODO】
- toString() //源码里调用了Integer.toString，非静态
- hashCode() //返回这个类的hashCode
- equals() //直接比byte了，也不用比hashCode了
- compareTo(Byte anotherByte) //和另外一个Byte比较大小
- compare(byte x, byte y) //静态方法

```

ByteCache可以查看[128的hashCode共享](http://blog.csdn.net/mazhimazh/article/details/17681787)

```
public class Test01 {
    public static void main(String[] args) {
        System.out.println(Byte.toString((byte) 1));  //1
        System.out.println(Byte.parseByte("129")); //NumberFormatException：Value out of range. Value:"129" Radix:10
        System.out.println(Byte.parseByte("x")); //NumberFormatException：For input string: "x"
        System.out.println(Byte.parseByte("20")); //20，调用了重写的toString方法
        System.out.println(Byte.valueOf("20")); //20，与parseByte()的区别是返回类型不同，这个返回Byte
        System.out.println(Byte.valueOf((byte) 130)); //-126，与parseByte()的区别是返回类型不同，这个返回Byte
        Byte testByte = new Byte("-5");
        System.out.println(testByte.shortValue()); //-5
        System.out.println(testByte.intValue()); //-5
        System.out.println(testByte.longValue()); //-5
        System.out.println(testByte.floatValue()); //-5.0
        System.out.println(testByte.doubleValue()); //-5.0
    }
}
```

Byte是final类型的类，也就是不可变的。（其他的基本类型的包装类也都是final类型的）下面的例子可以验证。
```java
/**
 * 试验包装类的自增。
 */
public class Test001 {
    private static void add(Byte b) {
        b = b++;
    }

    public static void main(String[] args) {
        Byte a = 127;
        Byte b = 127;
        add(++a);
        System.out.println(a);  //-128，自动拆箱
        add(b);
        System.out.println(b);  //127
    }
}
```






