---
title: "ubuntu 系统出现 文件系统只读Read-only file system 与  docker容器出现error creating overlay mount to /var/lib/docker/overlay2/f"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "ubuntu 系统出现 文件系统只读Read-only file system 与  docker容器出现error creating overlay mount to /var/lib/docker/overlay2/f"
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

## 出现情况


1. 虚拟机的Ubuntu 不知道什么原因，在命令行用sudo 改一个文件的时候的提示Read-only file system，其他各种sudo 操作都是如此。
2. docker 出现Error response from daemon: No such container: image
Error response from daemon: container cbb03abddd7ae4c4cce8268c58f1e2259aca340728949439bf748f1797607c54: driver "overlay2" failed to remove root filesystem: unlinkat /var/lib/docker/overlay2/3f881869f57f34946585aae218b8bab784fcbd59a181d7d824a8ce8aa8167b42: read-only file system
3 .docker启动出现 docker start vaultwarden
Error response from daemon: error creating overlay mount to /var/lib/docker/overlay2/f3631bb1f6dccd0a8640f01b2c124acf13ef99373c2e21df981d94615ae7dc3c/merged: invalid argument
Error: failed to start containers: 
**可能的原因：**

重启系统，提示根分区error，原因是磁盘出现文件系统错误。

**解决方法：**

使用fsck手动修复，具体操作如下：

重启以后如果能切换到root模式就用root 用户，如果不能就在安全恢复模式下 查看是否有fsck.ext4 指令。

然后运行fsck.ext4 -y /dev/sda1

注意： 要根据自己的实际硬盘情况 改变是sda1 还是sdb1 之类的。可以通过df 指令查看

**查看系统分区信息命令**：df -T-h

![技术分享图片](https://i-blog.csdnimg.cn/blog_migrate/a9acff955b11659a67b715a74182f39c.png)

