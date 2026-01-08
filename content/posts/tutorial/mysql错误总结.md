---
title: "Mysql错误总结"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Mysql错误总结"
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

## 错误总结
~~~sql
SELECT a.*,avg(b.s_score) as avgsc FROM student a left JOIN score b on a.s_id = b.s_id WHERE avgsc >60
~~~

这段SQL查询语句存在几个问题：

聚合函数的使用：在WHERE子句中使用聚合函数（如AVG）是不允许的。聚合函数只能在SELECT子句中使用，并且需要配合GROUP BY子句。

缺少GROUP BY子句：由于你在SELECT子句中使用了聚合函数AVG(b.s_score)，你需要使用GROUP BY子句来指定按哪些列进行分组。

别名在WHERE子句中的使用：在WHERE子句中使用别名（如avgsc）是不允许的，因为WHERE子句在SELECT子句之前执行。
~~~sql

SELECT   a.*, avg(b.s_score) as avgsc 
FROM student a 
LEFT JOIN score b on  a.s_id = b.s_id 
GROUP BY a.s_id
HAVING avgsc > 60;
~~~
