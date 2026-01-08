---
title: "centOS7 安装tailscale并启用子网路由"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "centOS7 安装tailscale并启用子网路由"
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

###1、在centOS7上安装Tailscale客户端 {#content_views}

```prism language-shell
#安装命令所在官网位置：https://tailscale.com/download/linux
#具体命令为：
curl -fsSL https://tailscale.com/install.sh | sh
#命令执行后如下图所示
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7dd0f7db0e9d4e47ba9cb6a37fd558ae.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/5c69dc10271c4301aca2a1cbcdd5fac4.png)

### 2、设置允许IP转发和IP伪装。

安装后，您可以启动（或重新启动）Tailscale 作为子网路由器：此功能需要启用 IP 转发。

执行下面命令启用IP转发 ：

```prism language-shell
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

执行以下命令启用IP伪装(**重要**)：

```prism language-shell
firewall-cmd --permanent --add-masquerade
```

### 3、通告子网路由

```java
sudo tailscale up --advertise-routes=192.168.0.0/24,192.168.1.0/24
#如果只有一个子网就只写一个
sudo tailscale up --accept-routes --advertise-routes=192.168.0.0/24
```

将上例中的子网替换为适合您的网络的子网。支持 IPv4 和 IPv6 子网。

上面命令执行后会打印登录地址：

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f6fe65e0cdac493ead9e758a44042be4.png)

###4、登录成功后从管理控制台启用子网路由 ，启用后就可以通过内网地址进行访问了。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c345949fc98b4e9eb1bc3ba6cf933d5a.png)

