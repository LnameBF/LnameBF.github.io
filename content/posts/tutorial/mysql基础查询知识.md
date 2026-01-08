---
title: "Mysql基础查询知识"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Mysql基础查询知识"
weight:
slug: ""
draft: false # 是否为草稿
comments: true # 本页面是否显示评论
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

### 排除非空的字段

is not null 和 is null 而不是 ！=null

### 模糊查询

不包含和包含 not like 和 like

~~~
请编写一条 SQL 查询语句，从名为 student 的数据表中选择出所有学生的姓名（name）和成绩（score），要求姓名（name）不包含 "李" 这个字。

select name,score from student where name not like '%李%'
~~~

### 逻辑运算

逻辑运算是一种在条件查询中使用的运算符，它允许我们结合多个条件来过滤出符合特定条件的数据。

在逻辑运算中，常用的运算符有：

- AND：表示逻辑与，要求同时满足多个条件，才返回 true。
- OR：表示逻辑或，要求满足其中任意一个条件，就返回 true。
- NOT：表示逻辑非，用于否定一个条件（本来是 true，用了 not 后转为 false）

### 去重

distinct

### 排序

order by desc 降序 asc 升序

### 分页

limit 1,3 注意，第一条数据的下标就是0，和数组类似

### 条件分支

~~~
case when 支持同时指定多个分支，示例语法如下：

CASE WHEN (条件1) THEN 结果1
	   WHEN (条件2) THEN 结果2
	   ...
	   ELSE 其他结果 END
	   
题目
假设有一个学生表 student，包含以下字段：name（姓名）、age（年龄）。请你编写一个 SQL 查询，将学生按照年龄划分为三个年龄等级（age_level）：60 岁以上为 "老同学"，20 岁以上（不包括 60 岁以上）为 "年轻"，20 岁以下为 "小同学"。

返回结果应包含学生的姓名（name）和年龄等级（age_level），并按姓名升序排序。

SELECT name, CASE WHEN (age > 60) THEN '老同学' WHEN (age > 20) THEN '年轻' ELSE '小同学' END AS age_level FROM student ORDER BY name asc;
~~~

![image-20240510205743265](https://file.heiok.top/note/202405102057340.png)

### 时间函数

在 SQL 中，时间函数是用于处理日期和时间的特殊函数。它们允许我们在查询中操作和处理日期、时间、日期时间数据，从而使得在数据库中进行时间相关的操作变得更加方便和灵活。

常用的时间函数有：

- DATE：获取当前日期
- DATETIME：获取当前日期时间
- TIME：获取当前时间

示例

假设有一个订单表 `orders`，包含以下字段：`order_id`（订单号）、`order_date`（下单日期）、`order_time`（下单时间）。数据如下：

| order_id | order_date | order_time |
| -------- | ---------- | ---------- |
| 1        | 2023-08-01 | 12:30:45   |
| 2        | 2023-08-01 | 14:20:10   |
| 3        | 2023-08-02 | 09:15:00   |
| 4        | 2023-08-02 | 18:05:30   |

使用时间函数获取当前日期、当前日期时间和当前时间：

```sql
-- 获取当前日期
SELECT DATE() AS current_date;

-- 获取当前日期时间
SELECT DATETIME() AS current_datetime;

-- 获取当前时间
SELECT TIME() AS current_time;
```

### 字符串处理 

upper 将字符串转成大写

length 计算长度

lower 小写

~~~
~~~

### 聚合函数

max

min

count

avg

sum

### 分组聚合 - 单字段分组

假设有一个学生表 `student`，包含以下字段：`id`（学号）、`name`（姓名）、`class_id`（班级编号）、`score`（成绩）。请你编写一个 SQL 查询，统计学生表中每个班级的平均成绩（avg_score）。

~~~sql
select class_id, avg(score) as avg_score from student group by class_id
~~~

### 分组聚合 - 多字段分组

### 分组聚合 - having 子句

>HAVING 子句与条件查询 WHERE 子句的区别在于，WHERE 子句用于在 **分组之前** 进行过滤，而 HAVING 子句用于在 **分组之后** 进行过滤。

假设有一个学生表 `student`，包含以下字段：`id`（学号）、`name`（姓名）、`class_id`（班级编号）、`score`（成绩）。请你编写一个 SQL 查询，统计学生表中班级的总成绩超过 150 分的班级编号（class_id）和总成绩（total_score）。

~~~sql
-- 请在此处输入 SQL
select class_id,sum(score) as total_score from student group by class_id having total_score>150
~~~

### 查询进阶 - 关联查询 - cross join

假设有一个学生表 `student` ，包含以下字段：id（学号）、name（姓名）、age（年龄）、class_id（班级编号）；还有一个班级表 `class` ，包含以下字段：id（班级编号）、name（班级名称）。

请你编写一个 SQL 查询，将学生表和班级表的所有行组合在一起，并返回学生姓名（student_name）、学生年龄（student_age）、班级编号（class_id）以及班级名称（class_name）。

~~~sql
-- 请在此处输入 SQL

select s.name as student_name, s.age as student_age,s.class_id as class_id,c.name  as class_name from student s join class c
~~~

### 查询进阶 - 子查询

>
>
>子查询是指在一个查询语句内部 **嵌套** 另一个完整的查询语句，内层查询被称为子查询。子查询可以用于获取更复杂的查询结果或者用于过滤数据。
>
>当执行包含子查询的查询语句时，数据库引擎会首先执行子查询，然后将其结果作为条件或数据源来执行外层查询。
>
>打个比方，子查询就像是在一个盒子中的盒子，外层查询是大盒子，内层查询是小盒子。执行查询时，我们首先打开小盒子获取结果，然后将小盒子的结果放到大盒子中继续处理。

假设有一个学生表 `student`，包含以下字段：`id`（学号）、`name`（姓名）、`age`（年龄）、`score`（分数）、`class_id`（班级编号）。还有一个班级表 `class`，包含以下字段：`id`（班级编号）、`name`（班级名称）。

请你编写一个 SQL 查询，使用子查询的方式来获取存在对应班级的学生的所有数据，返回学生姓名（`name`）、分数（`score`）、班级编号（`class_id`）字段。

~~~sql
select name,score, class_id from student where student.class_id in (select distinct id from class) 
~~~

### 查询进阶 - 子查询 - exists

>
>
>之前的教程讲到，子查询是一种强大的查询工具，它可以嵌套在主查询中，帮助我们进行更复杂的条件过滤和数据检索。
>
>其中，子查询中的一种特殊类型是 "exists" 子查询，用于检查主查询的结果集是否存在满足条件的记录，它返回布尔值（True 或 False），而不返回实际的数据。

假设有一个学生表 `student`，包含以下字段：`id`（学号）、`name`（姓名）、`age`（年龄）、`score`（分数）、`class_id`（班级编号）。还有一个班级表 `class`，包含以下字段：`id`（班级编号）、`name`（班级名称）。

请你编写一个 SQL 查询，使用 exists 子查询的方式来获取 **不存在对应班级的** 学生的所有数据，返回学生姓名（`name`）、年龄（`age`）、班级编号（`class_id`）字段。

~~~sql
-- 请在此处输入 SQL
select name,age,class_id from student where not exists (select id from class where student.class_id = class.id)
~~~

### 查询进阶 - 组合查询

>
>
>在 SQL 中，组合查询是一种将多个 SELECT 查询结果合并在一起的查询操作。
>
>包括两种常见的组合查询操作：UNION 和 UNION ALL。
>
>1. UNION 操作：它用于将两个或多个查询的结果集合并， **并去除重复的行** 。即如果两个查询的结果有相同的行，则只保留一行。
>2. UNION ALL 操作：它也用于将两个或多个查询的结果集合并， **但不去除重复的行** 。即如果两个查询的结果有相同的行，则全部保留。

假设有一个学生表 `student`，包含以下字段：`id`（学号）、`name`（姓名）、`age`（年龄）、`score`（分数）、`class_id`（班级编号）。还有一个新学生表 `student_new`，包含的字段和学生表完全一致。

请编写一条 SQL 语句，获取所有学生表和新学生表的学生姓名（`name`）、年龄（`age`）、分数（`score`）、班级编号（`class_id`）字段，要求保留重复的学生记录。

~~~sql
-- 请在此处输入 SQL
select name,age,score,class_id from student union all select name,age,score,class_id from student_new
~~~

### 查询进阶 - 开窗函数 - sum over

>
>
>在 SQL 中，开窗函数是一种强大的查询工具，它允许我们在查询中进行对分组数据进行计算、 **同时保留原始行的详细信息** 。
>
>开窗函数可以与聚合函数（如 SUM、AVG、COUNT 等）结合使用，但与普通聚合函数不同，开窗函数不会导致结果集的行数减少。
>
>打个比方，可以将开窗函数想象成一种 "透视镜"，它能够将我们聚焦在某个特定的分组，同时还能看到整体的全景。
>
>本节我们先讲第一个开窗函数：sum over。
>
>该函数用法为：
>
>```sql
>SUM(计算字段名) OVER (PARTITION BY 分组字段名)
>```

假设有一个学生表 `student`，包含以下字段：`id`（学号）、`name`（姓名）、`age`（年龄）、`score`（分数）、`class_id`（班级编号）。

请你编写一个 SQL 查询，返回每个学生的详细信息（字段顺序和原始表的字段顺序一致），并计算每个班级的学生平均分（class_avg_score）。

~~~sql
-- 请在此处输入 SQL
select id, name, age, score, class_id , avg(score) over (partition by class_id) as class_avg_score from student
~~~

### 查询进阶 - 开窗函数 - sum over order by

> 之前的教程中，我们讲到了 sum over 开窗函数，并且用它实现了分组统计。
>
> 本节教程我们将学习 sum over 函数的另一种用法：sum over order by，可以实现同组内数据的 **累加求和** 。
>
> 示例用法如下：
>
> ```sql
> SUM(计算字段名) OVER (PARTITION BY 分组字段名 ORDER BY 排序字段 排序规则)
> ```
>
> 举一个应用场景：老师在每个班级里依次点名，每点到一个学生，老师都会记录当前已点到的学生们的分数总和。

假设有一个学生表 `student`，包含以下字段：`id`（学号）、`name`（姓名）、`age`（年龄）、`score`（分数）、`class_id`（班级编号）。

请你编写一个 SQL 查询，返回每个学生的详细信息（字段顺序和原始表的字段顺序一致），并且按照分数升序的方式累加计算每个班级的学生总分（class_sum_score）。

~~~sql
-- 请在此处输入 SQL
select id,name,age,score,class_id ,sum(score) over (partition by class_id order by score asc) as class_sum_score from student
~~~

### 查询进阶 - 开窗函数 - rank

### 查询进阶 - 开窗函数 - row_number

Row_Number 开窗函数是 SQL 中的一种用于为查询结果集中的每一行 **分配唯一连续排名** 的开窗函数。

它与之前讲到的 Rank 函数，Row_Number 函数为每一行都分配一个唯一的整数值，不管是否存在并列（相同排序值）的情况。每一行都有一个唯一的行号，从 1 开始连续递增。

Row_Number 开窗函数的语法如下（几乎和 Rank 函数一模一样）：

~~~sql

ROW_NUMBER() OVER (
  PARTITION BY column1, column2, ... -- 可选，用于指定分组列
  ORDER BY column3 [ASC|DESC], column4 [ASC|DESC], ... -- 用于指定排序列及排序方式
) AS row_number_column

~~~

其中，PARTITION BY子句可选，用于指定分组列，将结果集按照指定列进行分组。ORDER BY 子句用于指定排序列及排序方式，决定了计算 Row_Number 时的排序规则。AS row_number_column 用于指定生成的行号列的别名。

**eg**

假设有一个学生表 `student`，包含以下字段：`id`（学号）、`name`（姓名）、`age`（年龄）、`score`（分数）、`class_id`（班级编号）。

请你编写一个 SQL 查询，返回每个学生的详细信息（字段顺序和原始表的字段顺序一致），并且按照分数降序的方式给每个班级内的学生分配一个编号（row_number）。

~~~sql
-- 请在此处输入 SQL
select id,name,age,score,class_id,Row_number() over (partition by class_id order by score desc) as row_number from student
~~~

### 查询进阶 - 开窗函数 - lag / lead


