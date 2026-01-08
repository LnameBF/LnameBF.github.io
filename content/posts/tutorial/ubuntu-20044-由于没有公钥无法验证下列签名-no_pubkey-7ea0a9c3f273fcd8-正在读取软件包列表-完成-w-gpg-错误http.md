---
title: "Ubuntu 20.04.4 由于没有公钥，无法验证下列签名： NO_PUBKEY 7EA0A9C3F273FCD8 正在读取软件包列表... 完成 W: GPG 错误：https://downlo"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Ubuntu 20.04.4 由于没有公钥，无法验证下列签名： NO_PUBKEY 7EA0A9C3F273FCD8 正在读取软件包列表... 完成 W: GPG 错误：https://downlo"
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

1 错误信息
由于没有公钥，无法验证下列签名： NO_PUBKEY 7EA0A9C3F273FCD8 正在读取软件包列表… 完成
W: GPG 错误：https://download.docker.com/linux/ubuntu bionic InRelease: 由于没有公钥，无法验证下列签名：
NO_PUBKEY 7EA0A9C3F273FCD8 E: 仓库 “https://download.docker.com/linux/ubuntu bionic InRelease” 没有数字签名。

2 解决方案
~~~
$ cd /etc/apt/sources.list.d/
$ sudo rm -f docker.list
$ sudo rm -f docker.list.save
$ sudo apt update
~~~
