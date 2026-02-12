---
title: "Git Merge 合并分支问题总结"
date: 2026-02-12T09:39:04+08:00
lastmod: 2026-02-12T09:39:04+08:00
author: ["Sulv"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- 
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
# Git Merge 合并分支问题总结

## 问题描述

执行 `git merge fea/douguan` 合并分支时，没有提示解决冲突，提交记录中也没有合并日志。

## 原因分析

当目标分支（当前分支）没有新的提交时，Git 默认会执行 **快进合并（Fast-forward）**，直接将指针移动到源分支的最新提交。

### 快进合并示意图

```
合并前:
A---B---C  (当前分支)
         \
          D---E  (fea/douguan)

合并后（直接移动指针，无合并提交）:
A---B---C---D---E  (当前分支)
```

## 如何验证合并状态

```bash
# 查看当前分支
git branch

# 查看提交历史
git log --oneline -10

# 查看图形化提交历史
git log --oneline --graph -10
```

## 强制产生合并提交

使用 `--no-ff` 参数禁用快进合并：

```bash
# 撤销刚才的合并（如果还没推送）
git reset --hard HEAD@{1}

# 强制创建合并提交
git merge --no-ff fea/douguan
```

### 合并提交示意图

```
A---B---C---M  (当前分支，M 是合并提交)
         \ /
          D---E  (fea/douguan)
```

## 冲突触发条件

冲突只在 **两个分支修改了同一文件的同一部分** 时才会出现：

| 情况 | 结果 |
|------|------|
| 修改不同文件 | 自动合并，无冲突 |
| 修改同一文件的不同部分 | 自动合并，无冲突 |
| 修改同一文件的同一部分 | 提示解决冲突 |

## 合并结果对照表

| 场景 | 结果 |
|------|------|
| 当前分支无新提交 | 快进合并，无合并提交 |
| 当前分支有新提交，无冲突 | 自动合并，有合并提交 |
| 当前分支有新提交，有冲突 | 提示解决冲突 |
| 使用 `--no-ff` 参数 | 强制产生合并提交 |





