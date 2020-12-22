共用表表达式（CTE）允许使用命名的临时结果集，利用公用表可以提升查询效率。

## 1.非递归`CTE`

CTE它的声明会放在查询块之前，而不是FROM子句中。CTE可能在SELECT/UPDATE/DELETE之前，包括WITH derived AS (subquery)的子查询，例如：

- 普通方法

```mysql
SELECT
	q1.year, q2.year AS next_year, q1.sum, q2.sum AS next_sum, 100*(q2.sum - q1.sum)/q1.sum AS pct
FROM
	(SELECT year(from_date) as year, sum(salary) as sum FROM salaries GROUP BY year) AS q1,
	(SELECT year(from_date) as year, sum(salary) as sum FROM salaries GROUP BY year) AS q2
WHERE
	q1.year = q2.year - 1;
```

子查询本质上是相同的，但是会被执行两次，导致性能降低。

- 非递归CTE

```mysql
WITH CTE AS 
	(SELECT year(from_date) as year, sum(salary) as sum FROM salaries GROUP BY year)
SELECT
	q1.year, q2.year AS next_year, q1.sum, q2.sum AS next_sum, 100*(q2.sum - q1.sum)/q1.sum AS pct
FROM
	CTE AS q1, 
	CTE AS q2
WHERE
	q1.year = q2.year - 1;
```

使用非递归CTE，子查询只执行一次，查询时间比之前缩短了50%，可读性变好，而且可以被多次引用。

#### note：

子查询不能引用其他子查询，CTE可以引用其他CTE。

```mysql
SELECT ...
	FROM (SELECT ... FROM ...) AS d1, (SELECT ... FROM d1 ...) AS d2 ...
会报错
```

CTE可以正常引用

```mysql
WITH d1 AS (SELECT ... FROM ...), d2 AS (SELECT ... FROM d1)
SELECT ...
FROM d1,d2...
```

## 2.递归CTE

子查询会引用自己的名字，WITH子句必须以WITH RECURSIVE开头。递归CTE查询包括两部分：seed查询和recursive查询，由UNION [ALL]或UNION DISTINCT分隔。

seed查询被执行一次以创建初始数据子集；recursive被重复执行以返回数据的子集，直到获得完整的结果集。

例子，打印从1到5的所有数字

```mysql
WITH RECURSIVE cte (n) AS
(
    SELECT 1 /* SEED QUERY */
    UNION ALL
    SELECT n + 1 FROM cte WHERE n < 5  /* recursive query */
)
SELECT * from cte;
```

