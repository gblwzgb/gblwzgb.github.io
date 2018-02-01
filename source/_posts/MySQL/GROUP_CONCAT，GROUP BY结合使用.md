---
title: GROUP_CONCAT，GROUP BY结合使用
date: 2017-04-11 12:34:47
categories: MySQL
---
今天做查询拼接的时候学习使用了`GROUP_CONCAT`。

<!-- more -->

**统计各年龄段所有人员的名字，并放在一个字段内**

```
SELECT
	pv.age,
	GROUP_CONCAT(DISTINCT pv.patient_name) AS 'all_name'
FROM
	pv_patient_visit pv
GROUP BY
	pv.age
```

因为用了`GROUP BY`,所以使用`GROUP_CONCAT`，而不是使用`CONCAT`。

因为名字可能重复，所以加上`DISTINCT`。

[参考资料](http://blog.csdn.net/gggxin/article/details/5282672)