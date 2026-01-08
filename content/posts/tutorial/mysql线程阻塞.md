---
title: "Mysql线程阻塞"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Mysql线程阻塞"
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

### 今日在执行Mysql修改和删除时出现报错，Lockwaittimeoutexceeded;try restarting transaction
后面经过排查和搜索发现是出现了线程阻塞的情况
select * from information_schema.innodb_trx;
kill 57288

可以使用命令查询，如果记录的trx_rows_locked这一列，如果大于０的话，说是有阻塞住了，然后查对应的trx_mysql_thread_id的值，　
然后执行
kill thread_id 杀死这个线程就可以了，
### 为什么会发生这个问题，暂时还不知道，后续将进行排查
