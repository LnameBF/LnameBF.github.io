---
title: "Mysql优化-索引"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Mysql优化-索引"
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

## 索引

## 1.1 索引分类

### 1.1.1 主键索引

1.表中的列设定为主键后，数据库会自动建立主键索引。

2.单独创建和删除主键索引语法：

创建主键索引语法： alter table 表名 add primary key (字段);

删除主键索引语法： alter table 表名 drop primary key;

### 1.1.2 唯一索引

1. 表中的列创建了唯一约束时，数据库会自动建立唯一索引。
2. 单独创建和删除唯一索引语法：

创建唯一索引语法：alter table 表名 add unique 索引名(字段)；

或 create unique index 索引名 on 表名(字段)；

删除唯一索引语法：drop index 索引名 on 表名;

### 1.1.3 单值索引

 即一个索引只包含单个列，一个表可以有多个单值索引。

1. 建表时可随表一起建立单值索引
2. 单独创建和删除单值索引：

创建单值索引： alter table 表名 add index 索引名(字段);

或  create index 索引名 on 表名(字段);

删除单值索引：drop index 索引名 on 表名;

### 1.1.4 复合索引

 即一个索引包含多个列：

1. 建表时可随表一起建立复合索引
2. 单独创建和删除复合索引：

创建复合索引：create index 索引名 on 表名(字段1,字段2);

或 alter table 表名 add index 索引名(字段,字段2);

  删除复合索引: drop index 索引名 on 表名; 

## 1.2. 性能分析

## 1.2.1 MySQL常见瓶颈

SQL中对大量数据进行比较、关联、排序、分组时CPU的瓶颈。

实例内存满足不了缓存数据或排序等需要，导致产生大量的物理IO。查询数据时扫描过多数据行，导致查询效率低。

## 1.2.2 Explain

使用EXPLAIN关键字可以模拟优化器执行SQL查询语句，从而知道MYSQL是如何处理SQL语句的。可以用来分析查询语句或是表的结构的性能瓶颈。其作用:

1）表的读取顺序

2）哪些索引可以使用

3）数据读取操作的操作类型

4）那些索引被实际使用

5）表之间的引用

6）每张表有多少行被优化器查询

 EXPLAIN关键字使用起来比较简单： explain + SQL语句：

 

![1658924620692](https://file.heiok.top/note/202405081733047.png)

## 1.3 Explain重要字段名

### 1.3.1 id

select查询的序列号，表示查询中执行select子句或操作表的顺序。

id相同时，执行顺序由上至下。

id不同，如果是子查询，id的序号会递增，id值越大优先级越高，则先被执行。

id相同和不同都存在时，id相同的可以理解为一组，从上往下顺序执行，所有组中，id值越大，优先级越高越先执行。

### 1.3.2 select_type

查询的类型，常见值有：

SIMPLE :简单的 select 查询,查询中不包含子查询或者UNION。

PRIMARY:查询中若包含任何复杂的子部分，最外层查询则被标记为Primary。

DERIVED:在FROM列表中包含的子查询被标记为DERIVED(衍生),MySQL会递归执行这些子查询, 把结果放在临时表里。

SUBQUERY: 在SELECT或WHERE列表中包含了子查询。

### 1.3.3 table

显示这一行的数据是关于哪张表的。

### 1.3.4 type

访问类型排序:

![1658925226295](https://file.heiok.top/note/202405081733050.png)

System：表只有一行记录（等于系统表），这是const类型的特列，平时不会出现，这个也可以忽略不计。

Const：表示通过索引一次就找到了,const用于比较primary key或者unique索引。因为只匹配一行数据，所以很快，如将主键置于where列表中，MySQL就能将该查询转换为一个常量。

eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描。

Ref：非唯一性索引扫描，返回匹配某个单独值的所有行。本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体。

Range：只检索给定范围的行,使用一个索引来选择行。key 列显示使用了哪个索引

一般就是在你的where语句中出现了between、<、>、in等的查询这种范围扫描索引扫描比全表扫描要好，因为它只需要开始于索引的某一点，而结束语另一点，不用扫描全部索引。

Index：Full Index Scan，index与ALL区别为index类型只遍历索引树。这通常比ALL快，因为索引文件通常比数据文件小。也就是说虽然all和Index都是读全表，但index是从索引中读取的，而all是从硬盘中读的。

All：Full Table Scan，将遍历全表以找到匹配的行。

从最好到最差依次是:system>const>eq_ref>ref>range>index>All 。一般来说，最好保证查询能达到range级别，最好能达到ref。

