---
title: "Git"
date: 2026-01-07T18:23:26+08:00
lastmod: 2026-01-07T18:23:26+08:00
author: ["Wait"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- mysql
description: ""
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
# 数据库语法

## DDL--创建表

操作表也就是对表进行增（Create）删（Retrieve）改（Update）查（Delete）。

* 查询当前数据库下所有表名称

```sql
show databases;--单行注释 /*多行注释*/
```

* 创建数据库

  ```sql
  create databases 数据库名称;
  ```

* 删除

  ```sql
  drop databases 数据库名称;
  ```

* 使用数据库

  ```sql
  select database();--查看当前使用的数据库
  ```

  ```sql
  use 数据库名称;
  ```

##DDl--操作表

***

### 查询表

* 查询当前数据库下所有的表名称

  ```sql
  show tables;--先进入表（use 数据库名称）在使用
  ```

* 查询表结构

  ```sql
  desc 表结构;
  ```

### 创建表

最后一行不加逗号

```sql
CREATE TABLE 表名 (
	字段名1 数据类型1,
	字段名2 数据类型2,
	…
	字段名n 数据类型n
);
```

### 数据类型

* 数值

  ```sql
  tinyint : 小整数型，占一个字节
  int ： 大整数类型，占四个字节
  eg ： age int
  double ： 浮点类型
  使用格式： 字段名 double(总长度,小数点后保留的位数)
  eg ： score double(5,2)
  ```

* 日期

  ```sql
  date ： 日期值。只包含年月日
  eg ：birthday date ：
  datetime ： 混合日期和时间值。包含年月日时分秒
  ```

* 字符串

  ```sql
  char ： 定长字符串。
  优点：存储性能高
  缺点：浪费空间
  eg ： name char(10) 如果存储的数据字符个数不足10个，也会占10个的空间
  varchar ： 变长字符串。
  优点：节约空间
  缺点：存储性能底
  eg ： name varchar(10) 如果存储的数据字符个数不足10个，那就数据字符个数是几就占几个的空间
  ```

### 删除表

* 删除表

  ```sql
  drop table 表名;
  ```

* 删除表时判断表是否存在

  ```sql
  drop table if exists 表名;
  ```

### 修改表

* 修改表名

  ```sql
  alter table 表名 rename to 新的表名;
  -- 将表名student修改为stu
  alter table student rename to stu;
  ```

* 添加一列

  ```sql
  alter table 表名 add 列名 数据类型;
  -- 给stu表添加一列address，该字段类型是varchar(50)
  alter table stu add address varchar(50);
  
  ```

* 修改数据类型

  ```sql
  alter table 表名 modify 列名 新数据类型
  -- 将stu表中的address字段名改为 addr，类型改为varchar(50)
  alter table stu change address addr varchar(50);
  ```

* 修改列名和数据类型

  ```sql
  alter table 表名 change 列名 新列名 新数据类型;
  -- 将stu表中的address字段名改为 addr，类型改为varchar(50)
  alter table stu change address addr varchar(50);
  ```

* 删除列

  ```sql
  alter table 表名 drop 列名
  -- 将stu表中的addr字段 删除
  alter table stu drop addr;
  ```

  

## DML--数据操作

### 添加数据

1. 给指定列添加数据

   ```sql
   INSERT INTO 表名(列名1,列名2,…) VALUES(值1,值2,…);
   ```

2. 给全部列添加数据

   ```sql
   INSERT INTO 表名 VALUES(值1,值2,…);
   ```

3. 批量添加数据

   ```sql
   INSERT INTO 表名(列名1,列名2,…) VALUES(值1,值2,…),(值1,值2,…),(值1,值2,…)…;
   INSERT INTO 表名 VALUES(值1,值2,…),(值1,值2,…),(值1,值2,…)…;
   ```

4. eg

   ```sql
   -- 给指定列添加数据
   INSERT INTO stu (id, NAME) VALUES (1, '张三');
   -- 给所有列添加数据，列名的列表可以省略的
   INSERT INTO stu (id,NAME,sex,birthday,score,email,tel,STATUS) VALUES (2,'李四','男','1999-11-
   11',88.88,'lisi@itcast.cn','13888888888',1);
   INSERT INTO stu VALUES (2,'李四','男','1999-11-11',88.88,'lisi@itcast.cn','13888888888',1);
   -- 批量添加数据
   INSERT INTO stu VALUES
   (2,'李四','男','1999-11-11',88.88,'lisi@itcast.cn','13888888888',1),
   (2,'李四','男','1999-11-11',88.88,'lisi@itcast.cn','13888888888',1),
   (2,'李四','男','1999-11-11',88.88,'lisi@itcast.cn','13888888888',1);
   ```

### 修改数据

1. 修改表数据

   ```sql
   UPDATE 表名 SET 列名1=值1,列名2=值2,… [WHERE 条件] ;
   ```

   ***

   > 1. 修改语句中如果不加条件，则将所有数据都修改
   > 2.  像上面的语句中的中括号，表示在写sql语句中可以省略这部分

    将张三的性别改为女

   ```sql
   1 update stu set sex = '女' where name = '张三';
   ```

   将张三的生日改为 1999-12-12 分数改为99.99

   ```sql
   update stu set birthday = '1999-12-12', score = 99.99 where name = '张三';
   
   ```

###删除数据

1. 删除数据

   ```sql
    DELETE FROM 表名 [WHERE 条件] ;
   ```

   ```sql
   -- 删除张三记录
   delete from stu where name = '张三';
   -- 删除stu表中所有的数据
   delete from stu;
   
   ```

## DQL--查询数据

```sql
SELECT
字段列表
FROM
表名列表
WHERE
条件列表
GROUP BY
分组字段
HAVING
分组后条件
ORDER BY
排序字段
LIMIT
分页限定
```

### 基础查询

* 查询多个字段

  ```sql
  SELECT 字段列表 FROM 表名;
  SELECT * FROM 表名; -- 查询所有数据
  ```

* 去除重复记录

  ```sql
  1 SELECT DISTINCT 字段列表 FROM 表名;
  ```

* 起别名

  ```sql
  AS: AS 也可以省略
  ```

  #### 练习

* 查询name、age两列

  ```sql
  select name,age from stu;
  ```

* 查询所有列的数据，列名可用*代替

  ```sql
  select * from stu;
  ```

* 去除重复记录

  ```sql
  select distinct address from stu;
  ```

* 查询姓名、数学成绩、英语成绩。并通过as给math和english起别名（as关键字可以省略）

  ```sql
  select name,math as 数学成绩,english as 英文成绩 from stu;
  select name,math 数学成绩,english 英文成绩 from stu;
  ```

* 范围查询

```sql
select * from emp where sal in(1500,3000,5000);
```



### 条件查询

* 语法

  ```sql
   SELECT 字段列表 FROM 表名 WHERE 条件列表;
  ```

* 条件

  ![image-20220819120939175](./img/image-20220819120939175.png)

  #### 练习

* 查询年龄大于20岁的学员信息

  ```sql
  1 select * from stu where age > 20;
  
  ```

* 查询年龄大于等于20岁的学员信息

  ```sql
  select * from stu where age >= 20;
  ```

* 查询年龄大于等于20岁 并且 年龄 小于等于 30岁 的学员信息

  ```sql
  select * from stu where age >= 20 && age <= 30;
  select * from stu where age >= 20 and age <= 30;
  ```

  > 上面语句中 && 和 and 都表示并且的意思。建议使用 and 。 也可以使用 between ... and 来实现上面需求

  ```sql
  select * from stu where age BETWEEN 20 and 30;
  ```

* 查询入学日期在'1998-09-01' 到 '1999-09-01' 之间的学员信息

  ```sql
  select * from stu where hire_date BETWEEN '1998-09-01' and '1999-09-01';
  ```

* 查询年龄等于18岁 或者 年龄等于20岁 或者 年龄等于22岁的学员信息

  ```sql
  select * from stu where age = 18 or age = 20 or age = 22;
  select * from stu where age in (18,20 ,22);
  ```

* 查询英语成绩为 null的学员信息 null值的比较不能使用 = 或者 != 。需要使用 is 或者 is not

  ```sql
  select * from stu where english = null; -- 这个语句是不行的
  select * from stu where english is null;
  select * from stu where english is not null;
  ```

### 模糊查询

  >模糊查询使用like关键字，可以使用通配符进行占位: （1）_ : 代表单个任意字符 
  >
  >（2）% : 代表任意个数字符

* 查询姓'马'的学员信息

  ```sql
  select * from stu where name like '马%';
  
  ```

* 查询第二个字是'花'的学员信息

  ```sql
  select * from stu where name like '_花%';
  ```

* 查询名字中包含 '德' 的学员信息

  ```sql
  select * from stu where name like '%德%';
  
  ```

### 排序查询

* 语法

  ```sql
  select字段列表 from 表名 order by 排序字段名1 [排序方式1],排序字段名2 [排序方式2] …;
  ```

* >ASC ： 升序排列 （默认值） 
  >
  >DESC ： 降序排列
  >
  >注意：如果有多个排序条件，当前边的条件值一样时，才会根据第二条件进行排序

* 查询学生信息，按照年龄升序排列

  ```sql
  select * from stu order by age ;
  ```

* 查询学生信息，按照数学成绩降序排列，如果数学成绩一样，再按照英语成绩升序排列

  ```sql
  select * from stu order by math desc , english asc ;
  ```

### 聚合函数

1. 概念

   将一列数据作为一个整体，进行纵向计算。

2. 分类

   <img src="./Myimg/image-20220819140020417.png" alt="image-20220819140020417" style="zoom:80%;" />

3. 聚合函数语法

   ```sql
   select聚合函数名(列名) from 表;
   ```

   >注意：null 值不参与所有聚合函数运算

   #### 练习

   * 统计班级一共有多少个学生(后面可以加条件查询语句)

     ```sql
     select count   from stu;
     select count(english) from stu;
     ```

     >上面语句根据某个字段进行统计，如果该字段某一行的值为null的话，将不会被统计。所以可以在count(*) 来实现。* 表 示所有字段数据，一行中也不可能所有的数据都为null，所以建议使用 count(*)

     ```sql
     1 select count(*) from stu;
     ```

   * 查询数学成绩的最高分

     ```sql
      select max(math) from stu;
     
     ```
###分组查询

* 语法

  ```sql
  SELECT 字段列表 FROM 表名 [WHERE 分组前条件限定] GROUP BY 分组字段名 [HAVING 分组后条件过滤];
  ```

  >注意：分组之后，查询的字段为聚合函数和分组字段，查询其他字段无任何意义
  
  #### 练习

  * 查询男同学和女同学各自的数学平均分

    ```sql
    select sex, avg(math) from stu group by sex;
    ```

    >注意：分组之后，查询的字段为聚合函数和分组字段，查询其他字段无任何意义

  * 查询男同学和女同学各自的数学平均分，以及各自人数

    ```sql
    select avg(age) '年龄',count(*) '性别' from xsjbxxb group by xb;
    ```

  * 查询男同学和女同学各自的数学平均分，以及各自人数，要求：分数低于70分的不参与分组

    ```sql
    select sex, avg(math),count(*) from stu where math > 70 group by sex;
    ```

  * 查询男同学和女同学各自的数学平均分，以及各自人数，要求：分数低于70分的不参与分组，分组之后人数大于2个的

    ```sql
    select sex, avg(math),count(*) from stu where math > 70 group by sex having count(*) > 2;
    ```

    >执行时机不一样：where 是分组之前进行限定，不满足where条件，则不参与分组，而having是分组之后对结果进行过 滤。 
    >
    >可判断的条件不一样：where 不能对聚合函数进行判断，having 可以。

### 分页查询

* 语法

  ```sql
  SELECT 字段列表 FROM 表名 LIMIT 起始索引 , 查询条目数;
  ```
  
* 从0开始查询，查询3条数据（这里0就是起始索引）

  ```sql
  select * from stu limit 0 , 3;
  ```

* 公式

  ```sql
  起始索引 = (当前页码 - 1) * 每页显示的条数
  ```

  









