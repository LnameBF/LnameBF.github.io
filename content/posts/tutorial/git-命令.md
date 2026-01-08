---
title: "Git 命令"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Git 操作记录"
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


## Git rebase
git rebase -i 提交id ,操作提交id后的提交 ，参数：
修改提交信息：将pick替换为reword    
合并提交：将多个pick替换为squash 
删除提交：直接删除对应的行。
注意：删除至少需要保留一行pick，
需要删除所有的提交可以使用 
1. git reset --hard 提交id ，如果已经提交到远端，可以使用git push --force 强制提交
2. git rebase --onto 提交id  删除提交id后所有的提交
