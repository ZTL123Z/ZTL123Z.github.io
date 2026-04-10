---
title: sql窗口函数
category: sql
tags:
  - sql
  - 数据分析
  - 窗口函数
published: 2026-04-09 09:43:30
---

# 窗口函数和聚合函数的区别

## 聚合函数

会压缩行数

```sql
SELECT SUM(amount) FROM payment
```

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409095432441.png" alt="image-20260409095432243" style="zoom: 50%;" />

## 窗口函数



```sql
SELECT payment_date,
	amount,
	SUM(amount) OVER () AS total_sum -- 没有分组，就算全部的和 
FROM payment
```

<img src="https://cdn.jsdelivr.net/gh/ZTL123Z/picgo-imgHub@main/20260409100122396.png" alt="image-20260409100122248" style="zoom:50%;" />

## 对比

| 对比项   | 聚合函数           | 窗口函数                                   |
| -------- | ------------------ | ------------------------------------------ |
| 行数影响 | 合并多行为一行     | 不减少行数                                 |
| 使用方式 | 一般与GROUP BY配合 | 与OVER()子句配合                           |
| 适用场景 | 汇总统计           | 分区统计、行间比较、累计计算、百分百计算等 |



# sql窗口函数

窗口函数是一个**统称**（也叫分析函数），它并不是一个具体的函数，而是一种 “函数使用方式”—— 把普通的聚合函数、排名函数等放到 `OVER()` 子句中，就变成了窗口函数。

窗口函数的结构是：函数名() OVER (窗口子句)，其中**函数名**可以替换成多种类型的函数，OVER() 子句定义 “计算窗口”（分组、排序、范围）

## 聚合类窗口函数（最常用）

1. SUM(): 分组累计求和（如累计利润）

```sql
SUM(profit) OVER (
    PARTITION BY shop_id 
    ORDER BY profit_date)
```

​	 2.AVG():分组滚动求平均（如班级平均分趋势）

```sql
	
AVG(score) OVER (
	PARTITION BY class 
	ORDER BY exam_date)
```

3. MAX()/MIN():分组内取极值（如每个学校的最高 GPA）

```sql
MAX(gpa) OVER (
    PARTITION BY university)
```



4. COUNT(): 分组内统计行数（如每个部门的人数）

```sql
	
COUNT(*) OVER (
    PARTITION BY dept)
```

​	

## 排名类窗口函数（仅限作为窗口）

这类函数只能作为窗口函数使用，无法单独用，核心用于生成排名、序号。

| 排名函数     | 用法示例                                                     | 作用                                  |
| ------------ | ------------------------------------------------------------ | ------------------------------------- |
| ROW_NUMBER() | `ROW_NUMBER() OVER (PARTITION BY university ORDER BY gpa DESC)` | 分组内连续排名（1,2,3…，无并列）      |
| RANK()       | `RANK() OVER (ORDER BY score DESC)`                          | 跳跃排名（1,2,2,4…，并列会跳过序号）  |
| DENSE_RANK() | `DENSE_RANK() OVER (ORDER BY score DESC)`                    | 连续排名（1,2,2,3…，并列不跳过序号）  |
| NTILE(n)     | `NTILE(4) OVER (ORDER BY sales)`                             | 分组内分桶（如把销售额分成 4 个等级） |

## 偏移类窗口函数（仅限作为窗口函数）

| 偏移函数      | 用法示例                                                     |               作用               |
| ------------- | ------------------------------------------------------------ | :------------------------------: |
| LAG()         | `LAG(profit, 1) OVER (ORDER BY profit_date)`                 | 获取上 1 行的利润（计算日环比）  |
| LEAD()        | `LEAD(profit, 7) OVER (ORDER BY profit_date)`                | 获取下 7 行的利润（计算周同比）  |
| FIRST_VALUE() | `FIRST_VALUE(gpa) OVER (PARTITION BY university ORDER BY gpa)` |  分组内第一行的 GPA（最低 GPA）  |
| LAST_VALUE()  | `LAST_VALUE(gpa) OVER (PARTITION BY university ORDER BY gpa)` | 分组内最后一行的 GPA（最高 GPA） |

