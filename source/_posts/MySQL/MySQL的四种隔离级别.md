---
title: MySQL的四种隔离级别
date: 2017-04-13 14:34:47
categories: MySQL
---

SQL标准定义了4类隔离级别，这是非常重要的知识点，是每个程序猿都应该熟练掌握的。

<!-- more -->

# 理论基础

### Read Uncommitted（读取未提交内容）

在该隔离级别，所有事务都可以看到其他未提交事务的执行结果。本隔离级别很少用于实际应用，因为它的性能也不比其他级别好多少。读取未提交的数据，也被称之为脏读（Dirty Read）。

### Read Committed（读取提交内容）

这是大多数数据库系统的默认隔离级别（但不是MySQL默认的）。它满足了隔离的简单定义：一个事务只能看见已经提交事务所做的改变。这种隔离级别 也支持所谓的不可重复读（Nonrepeatable Read），因为同一事务的其他实例在该实例处理其间可能会有新的commit，所以同一select可能返回不同结果。

### Repeatable Read（可重读）

这是MySQL的默认事务隔离级别，它确保同一事务的多个实例在并发读取数据时，会看到同样的数据行。不过理论上，这会导致另一个棘手的问题：幻读 （Phantom Read）。简单的说，幻读指当用户读取某一范围的数据行时，另一个事务又在该范围内插入了新行，当用户再读取该范围的数据行时，会发现有新的“幻影” 行。InnoDB和Falcon存储引擎通过多版本并发控制（MVCC，Multiversion Concurrency Control）机制解决了该问题。

### Serializable（可串行化）

这是最高的隔离级别，它通过强制事务排序，使之不可能相互冲突，从而解决幻读问题。简言之，它是在每个读的数据行上加上共享锁。在这个级别，可能导致大量的超时现象和锁竞争。


## 不同隔离级别产生的各种问题
隔离级别 | 脏读 | 不可重复读 | 幻读
---|--- | --- | ---
read uncommitted | √ | √ | √
read committed | × | √ | √
repeatable read | × | × | √或×
serializable |  × | × | × 
为什么第三行的幻读是不确定的呢？因为这取决你数据库内选择的是何种存储引擎，比如InnoDB就通过多版本并发控制（MVCC，Multiversion Concurrency Control）机制解决了幻读了问题。



## 脏读
事务T1可以看到事务T2未提交的内容。

**举例：**  
病人A的性别是男性，事务T1开启事务想查看病人A的性别，这个时候事务T2，先行一步，手滑把病人A的性别改成了女性，没有提交数据库，此时事务T1读到的病人A的性别是女性。事务T2发现改错了，回滚了事务，但是事务T1已经读到了“脏”数据。

## 不可重复读
由于事务T2对数据的修改，导致事务T1连续查询两次数据，发现两次查询出的数据不一致。

**举例：**  
病人A的性别是男性，事务T1开启事务第一次查看病人A的性别是男性，这时事务T2把病人A的性别改为女性，并提交。事务T1第二次查看病人A的性别，发现变成了女性。

## 幻读
事务T1按一定条件读取数据，这个时候事务T2如果在该条件内删除或新增数据，会导致事务T1再次读取数据的时候，会发现数据变多/少了。

**举例：**
1. 如果事务T1在第一次读取的时候对数据进行了更新，那么事务T2新增的幻读的数据就被漏改了。就像幻觉一样。
2. 如果事务T1在第一次读取的时候发现数据有5条，这时事务T2新增或删除了一条数据，事务T1再次查询的时候，发现记录不是5条了。就像幻觉一样。


# 动手操作环节

上面的概念不需要死记硬背，跟着下面的步骤试验一遍就能心里有数了。

以MySQL数据库为例。

## 准备库表与基础数据

在`test`库下新增表`test`，表结构如下
```
CREATE TABLE `test` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `num` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
插入三条基础数据
```
INSERT INTO test (num) VALUES(1);
INSERT INTO test (num) VALUES(2);
INSERT INTO test (num) VALUES(3);
```

进入MySQL命令行，不知道如何进入的[点击这里](../WINDOWS如何进入MySQL命令行)

通过`use test;`进入具体的库

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/91904121-file_1491981549970_ef19.png)

通过`show tables;`查看所有表

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/83849583-file_1491981624968_14ec8.png)

下面开两个窗口，A窗口代表事务T1，B窗口代表事务T2，通过修改A窗口的事务隔离级别来试验。
## 1. 将A的隔离级别设置为read uncommitted(读未提交)

**A：设置隔离级别**

`set session transaction isolation level read uncommitted;`

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/131570-file_1491981956792_3200.png)

**A：查看隔离级别**

`select @@tx_isolation;`

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/88154747-file_1491982098863_13c02.png)

**B：查看隔离级别**

A的隔离级别设置只对当前连接有效，查看B的隔离级别，可以发现，还是MySQL默认的隔离级别`REPEATABLE-READ`。

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/96102531-file_1491982208328_151be.png)

这里B窗口就使用默认的隔离级别就可以了，不需要修改。

**A：进入事务，并查询**

`start transaction;`

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/28486851-file_1491982546947_1225b.png)

**B：进入事务，并更新一条记录**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/58489290-file_1491982763692_11f8a.png)  
注意，此时B窗口还没有提交事务。

**A：再次查询**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/69209462-file_1491982875153_e7dd.png)  
可以看到，A读到了B没有提交的事务。

**B：回滚事务**

通过`rollback;`来回滚事务。  

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/4245094-file_1491983073673_14dd2.png)

**A：再次查询**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/35919306-file_1491983128743_9686.png)


如果B新增/删除记录，不提交的话。A也是能看到的。这里就不演示了。

## 2. 将客户端A的事务隔离级别设置为read committed(读已提交)

**A：设置隔离级别**

`set session transaction isolation level read committed;`

**A：开启事务，并查询**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/28486851-file_1491982546947_1225b.png)

**B：开启事务，并修改**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/58489290-file_1491982763692_11f8a.png)

**A：查询**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/35919306-file_1491983128743_9686.png)  
我们发现A没有读取到B没有提交的事务。

**B：提交事务**

`commit;`

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/31618607-file_1491986479178_aad2.png)

**A：查询**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/69209462-file_1491982875153_e7dd.png)

可以看到脏读问题解决了，但是出现了不可重复读的问题。也就是A窗口第一次查询和第二次查询的结果不一样。

## 3. 将A的隔离级别设置为repeatable read(可重复读)

**A：设置隔离级别**

`set session transaction isolation level repeatable read;`

**A：开启事务并查询**

![](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/76572583-file_1491987406629_6333.png)

**B：开启事务，更新记录但不提交**

![](http://ok7wlv1ee.bkt.clouddn.com/17-4-13/21971864-file_1492045056348_6aba.png)

**A：查询**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/35919306-file_1491983128743_9686.png)

**B：提交**

![](http://ok7wlv1ee.bkt.clouddn.com/17-4-13/31142551-file_1492045141256_35c5.png)

**A：查询**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/35919306-file_1491983128743_9686.png)  
即使B修改了记录并提交，但是A两次的查询结果都是一样的，说明可以重复查询了。

**B：新增记录**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-13/66448146-file_1492045547790_728.png)

**A：查询**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-12/35919306-file_1491983128743_9686.png)

并没有新增的数据，幻读问题也没有出现，因为我的MySQL用的是InnoDB作为存储引擎。

## 4. 将A的隔离级别设置为Serializable(可串行化)

**A：设置隔离级别，开启事务并查询**

`set session transaction isolation level serializable;`

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-13/98104812-file_1492045804404_17c5.png)

**B：开启事务，新增记录**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-13/66351991-file_1492045858068_a810.png)  
发现卡住了，因为必须等A的事务执行完毕才行。

**A：提交事务**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-13/79104071-file_1492045922444_1a76.png)

**B：插入记录成功。**

![image](http://ok7wlv1ee.bkt.clouddn.com/17-4-13/34114634-file_1492045961893_10a51.png)





