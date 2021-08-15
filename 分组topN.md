## 问题：

例如查询成绩表中按科目分组后每门课成绩前n的所有同学的信息。

#### 分组Top_1:

1. 用表连接

```mysql
SELECT a.*,b.max_score from sc a 
INNER JOIN (
SELECT CId,max(score) max_score
from sc
GROUP BY CId ) b 
on a.CId= b.CId and a.score= b.max_score;
```

2. 使用子查询：数据量大的时候会导致很慢、

```mysql
SELECT * from sc a 
where score = (SELECT max(score) from sc  where a.CId=CId );
```

3. 使用exist：这种方法一定要保证，对比的列不为NULL，否则不能返回正确答案。

```mysql
SELECT * from sc a where 
not EXISTS 
(SELECT 1 from sc where a.score <score and a.CId= CId);
```

4. 使用窗口函数：MySQL_8.0之后

```mysql
SELECT SId ,CId,score from (
SELECT SId,CId,score,dense_rank() over (partition by CId ORDER BY score desc) as ranking from sc ) new 
where ranking=1;
```



#### 分组Top_n:

1. 用自身左连接，相同分数算多次

```mysql
SELECT SId,CId,score from (
SELECT A.* 
from sc a left  JOIN sc b 
on a.CId= b.CId and a.score <b.score  ORDER BY a.score DESC) new
GROUP BY SId,CId,score
HAVING count(CId)<2
ORDER BY CId;
```



2. 用子查询：数据量大时不能用

```mysql
SELECT * from sc a
where 2 > (SELECT count(*) from sc where a.CId=CId and  a.score <score)
ORDER BY CId;
```

3. 窗口函数

```mysql
SELECT SId ,CId,score from (
SELECT SId,CId,score,dense_rank() over (partition by CId ORDER BY score desc) as ranking from sc ) new 
where ranking <= 2;
```



