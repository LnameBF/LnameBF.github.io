---
title: "MySQL 关键字执行顺序"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "MySQL 关键字执行顺序"
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

在SQL语句中每个关键字都会按照顺序往下执行，而每一步操作，会生成一个**虚拟表**，最后的虚拟表就是最终结果。

基本sql 语句如下 :

```SQL hljs sql
(8)SELECT (9)DISTINCT <select_list>
(1)FROM <left_table>
(3) <join_type> JOIN <right_table>
(2)       ON <join_condition>
(4)WHERE <where_condition>
(5)GROUP BY <group_by_list>
(6)WITH{CUBE|ROLLUP}
(7)HAVING <having_condition>
(10)ORDER BY <order_by_list>
(11)LIMIT <limit_number>
```

> 常用的的执行顺序：

1. `FROM` ： 对FROM左边的表和右边的表计算**笛卡尔积**，产生虚表VT1；
2. `ON` ： 对虚拟表VT1进行ON筛选，只有那些符合条件的行才会被记录在虚拟表VT2中；
3. `JOIN` ：如果是OUT JOIN，那么将保留表中（如左表或者右表）未匹配的行作为外部行添加到虚拟表VT2中，从而产生虚拟表VT3；
4. `WHERE` ：对虚拟表VT3进行WHERE条件过滤，只有符合的记录才会被放入到虚拟表VT4；
5. `GROUP BY`：根据GROUP BY子句中的列，对虚拟表VT4进行分组操作，产生虚拟表VT5；
6. `CUBE|ROLLUP`：对虚拟表VT5进行CUBE或者ROLLUP操作，产生虚拟表VT6；
7. `HAVING` ：对虚拟表VT6进行 HAVING 条件过滤，只有符合的记录才会被插入到虚拟表VT7中；
8. `SELECT` ：执行SELECT操作，选择指定的列，插入到虚拟表VT8中；
9. `DISTINCT` ：对虚拟表VT8中的记录进行去重，产生虚拟表VT9；
10. `ORDER BY` ：将虚拟表VT9中的记录按照进行排序操作，产生虚拟表VT10；
11. `LIMIT` ：取出指定行的记录，产生虚拟表VT11，并将结果返回。

