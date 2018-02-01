---
title: COUNT,GROUP BY,CASE WHEN混用
date: 2017-04-11 12:34:47
categories: MySQL
---
今天在使用COUNT的时候学习了新的写法。

也就是COUNT结合CASE WHEN使用。

<!-- more -->

**统计病人表不同年龄段各有多少人。**

```
SELECT
	pv.age,
	COUNT(pv.age) AS 'count'
FROM
	pv_patient_visit pv
GROUP BY
	pv.age
```

**统计病人表不同年龄段各有多少男人，女人。**

```
SELECT
	pv.age,
	COUNT(case when pv.sex='0' then 1 else null end ) AS 'man',
	COUNT(case when pv.sex='1' then 1 else null end ) AS 'woman'
FROM
	pv_patient_visit pv
GROUP BY
	pv.age
```

## 后记
`COUNT( CASE WHEN pv.sex='0' THEN 1 ELSE NULL END )`这种写法，只适合表达式内字段属性很少的情况。比如是否删除，男女等。