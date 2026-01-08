---
title: ".NET core 5大核心接口"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: ".NET core 5大核心接口"
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

在 .NET Core 5 中，接口扮演着非常重要的角色，它们定义了类或结构必须实现的方法、属性、事件等契约。这些核心接口使得 .NET Core 应用程序更加模块化，易于测试和维护。虽然没有明确的“5大核心接口”这样的分类，但是我可以提供一些在 .NET Core 中常用的重要接口：

IApplicationBuilder - 用于构建应用程序的请求管道。
IServiceCollection - 用于定义和配置应用程序所需的服务。
IHostingEnvironment - 提供有关应用程序的宿主环境的信息。
ILogger - 用于在应用程序中实现日志记录。
IConfiguration - 用于读取应用程序的配置设置。
