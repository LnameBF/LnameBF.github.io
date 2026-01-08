---
title: "内网穿透 篇一：通过 Tailscale 异地组网 实现远程访问局域网"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "内网穿透 篇一：通过 Tailscale 异地组网 实现远程访问局域网"
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

> 本文首发于只抄博客，欢迎点击[原文链接](https://zhichao.org/posts/tailscale)了解更多内容。

### 前言

Tailscale 是一款基于 WireGuard 的异地组网工具，它可以将不同网络环境的设备组成一个虚拟局域网，使其可以互相访问。我们只需要在路由器或者 Nas 上安装 Tailscale 进行组网，就可以实现以下效果：

* 连接到 Tailscale 的设备，可以直接使用内网 IP 访问家庭局域网
* 内网设备可以直接使用 Tailscale 分配的 IP 来访问连接到 Tailscale 的设备
* 支持多个局域网互相访问，每个局域网只需一台设备安装 Tailscale ( 每个局域网的网段不能相同 )

### 安装步骤

1. 打开 [Tailscale 官网](https://tailscale.com/)，点击右上角的 Log in，可以直接选择 Google 或者 Microsoft 授权登录
2. 根据提示下载对应平台的客户端，更多平台下载请参考[官方文档](https://tailscale.com/kb/installation/)
   * [Windows](https://tailscale.com/download/windows)
   * [Android](https://play.google.com/store/apps/details?id=com.tailscale.ipn) (国区 Google Play 无法直接下载，可以在 [APKMirror](https://www.apkmirror.com/?post_type=app_release&searchtype=apk&s=tailscale) 下载)
   * Linux 一键安装脚本 `curl -fsSL https://tailscale.com/install.sh | sh`
   * **卸载** ：`apt-get remove tailscale` `rm -rf /var/lib/tailscale/tailscaled.state`
3. 有图形化界面的平台，直接登录账号即可；而在无图形化界面的平台上，需要输入 `tailscale up`

![tailscale1](https://img-blog.csdnimg.cn/img_convert/d72032d3828bc1b622c09ba0329c5b26.webp?x-oss-process=image/format,png)

4. 打开出现的链接，进行登录即可
5. 默认情况下，会开启 Key Expiry，设备一段时间后会自动失效，可以在设备列表中选择 Disable Key Expiry

![tailscale2](https://img-blog.csdnimg.cn/img_convert/b603f9198d4763f135a86916185ac94d.webp?x-oss-process=image/format,png)

6. 完成以上步骤后，启动客户端，即可使用列表中的 IP 进行直接访问

### 访问局域网

此时，只有连接到 Tailscale 的设备可以互相访问，并不能直接使用内网 IP 来访问整个局域网，我们还需要进行以下配置

#### 开启 IP 转发

1. 编辑 `sysctl.conf` 文件 `vim /etc/sysctl.conf`

2. 将以下代码取消注释

```prism language-txt
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1
```

3. 加载内核参数 `sysctl -p`

#### {#_45}添加子网路由

1. 例如我的路由器地址为 10.0.0.1，光猫地址为 192.168.1.1，则需要配置的网段为 10.0.0.0/24 与 192.168.1.0/24

```prism language-sh
tailscale up --advertise-routes=10.0.0.0/24,192.168.1.0/24
```

2. 在 Tailscale 控制面板中，将刚才添加的子网路由进行开启

![tailscale3](https://img-blog.csdnimg.cn/img_convert/5dc628ed665f9119148eec7f7c851993.webp?x-oss-process=image/format,png)

![tailscale4](https://img-blog.csdnimg.cn/img_convert/c3474cc407bbe0e9daabdbbebd83edd5.webp?x-oss-process=image/format,png)

### {#_Tailscale__59}访问 Tailscale 设备

通过上面的设置，已经实现了连接到 Tailscale 的设备使用内网 IP 访问局域网，但是局域网内的设备仍然无法使用 Tailscale 分配的 IP 来访问那些连接到 Tailscale 的设备，我们还需要在路由器中配置静态路由

* **网络 / 主机 IP**: Tailscale 的网段 100.64.0.0
* **网络掩码**: 255.192.0.0
* **网关**: 安装 Tailscale 设备的内网 IP

![tailscale5](https://img-blog.csdnimg.cn/img_convert/71cc84f9f7575d7c5f9ee0e2d322184c.webp?x-oss-process=image/format,png)

### {#_69}无法打开网页

部分设备在连接 Tailscale 后，可能存在无法打开网页的问题，只需要在 Tailscale 控制面板中的DNS设置中，将 MagicDNS 进行关闭即可

![tailscale6](https://img-blog.csdnimg.cn/img_convert/29f2b825b16d49b2ccc60df70113b0ac.webp?x-oss-process=image/format,png)

