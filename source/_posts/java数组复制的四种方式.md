---
title: java数组复制的四种方式
date: 2017-02-28 18:42:47
categories: Java基础
---

![image](http://ok7wlv1ee.bkt.clouddn.com/17-2-9/19026684-file_1486612180846_1715d.png)

我测试出来的效率如图。

但是clone方法和Arrays.copyOf谁快不好说，貌似和数据量也有关系。

反正System.arrayCopy肯定是最快的，因为是native的方法。ArrayList里用的也是这个方法。

Arrays.copyOf底层用的其实也是System.arrayCopy

**测试代码如下**

```java
public class ArrayCopyTest {
    private static String[] src
            = {"Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff",
            "Aaaaaaaaaa", "Vvvvvvv", "Bbbb", "Cccc", "Dddd", "Eddeee", "FFFFFFffffffff"};

    private static String[] dst;

    public static void main(String[] args) {
        int num = 5000000;
        System.out.println(forCopy(num));
        System.out.println(cloneCopy(num));
        System.out.println(systemJNICopy(num));
        System.out.println(ArraysToolCopy(num));
    }

    private static long forCopy(int num) {
        long start = System.currentTimeMillis();
        while (num-- > 0) {
            int size = src.length;
            dst = new String[size];
            for (int i = 0; i < size; i++) {
                dst[i] = src[i];
            }
        }
        long end = System.currentTimeMillis();
        return end - start;
    }

    private static long cloneCopy(int num) {
        long start = System.currentTimeMillis();
        while (num-- > 0) {
            dst = src.clone();
        }
        long end = System.currentTimeMillis();
        return end - start;
    }

    private static long systemJNICopy(int num) {
        long start = System.currentTimeMillis();
        while (num-- > 0) {
            System.arraycopy(src, 0, dst, 0, src.length);
        }
        long end = System.currentTimeMillis();
        return end - start;
    }

    private static long ArraysToolCopy(int num) {
        long start = System.currentTimeMillis();
        while (num-- > 0) {
            dst = Arrays.copyOf(src, src.length);
        }
        long end = System.currentTimeMillis();
        return end - start;
    }
}
```