---
title: "Docker 安装 Nginx 容器 (完整详细版)"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Docker 安装 Nginx 容器 (完整详细版)"
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

> [Docker 安装 (完整详细版)](https://blog.csdn.net/BThinker/article/details/123358697 "Docker 安装 (完整详细版)")
>
> [Docker 日常命令大全(完整详细版)](https://blog.csdn.net/BThinker/article/details/123355362 "Docker 日常命令大全(完整详细版)")

#### 说明： 

> [Docker](https://so.csdn.net/so/search?q=Docker&spm=1001.2101.3001.7020 "Docker")如果想安装软件 , 必须先到 [Docker](https://so.csdn.net/so/search?q=Docker&spm=1001.2101.3001.7020 "Docker") 镜像仓库下载镜像。

[Docker官方镜像](https://hub.docker.com/ "Docker官方镜像 ")

#### 1、寻找Nginx镜像

![](https://i-blog.csdnimg.cn/blog_migrate/5b067431092502af0634593c457c19f0.png)

![](https://i-blog.csdnimg.cn/blog_migrate/018947ab9a59dc9b83eb634c51b436a9.png)

#### 2、下载Nginx镜像

|          命令           |                          描述                          |
|-----------------------|------------------------------------------------------|
| docker pull nginx     | 下载最新版Nginx镜像 (其实此命令就等同于 : docker pull nginx:latest ) |
| docker pull nginx:xxx | 下载指定版本的Nginx镜像 (xxx指具体版本号)                           |

![](https://i-blog.csdnimg.cn/blog_migrate/b3fc0f9f1602f6b916f0faef2a3f05fb.png)
> 检查当前所有Docker下载的镜像

```java
docker images
```

#### 3、创建Nginx配置文件

> 启动前需要先创建Nginx外部挂载的配置文件（ /home/nginx/conf/nginx.conf）  
> 之所以要先创建 , 是因为Nginx本身容器只存在/etc/nginx 目录 , 本身就不创建 nginx.conf 文件  
> 当服务器和容器都不存在 nginx.conf 文件时, 执行启动命令的时候 docker会将nginx.conf 作为目录创建 , 这并不是我们想要的结果 。

```java
# 创建挂载目录
mkdir -p /home/nginx/conf
mkdir -p /home/nginx/log
mkdir -p /home/nginx/html
```

> 容器中的nginx.conf文件和conf.d文件夹复制到宿主机

```java
# 生成容器
docker run --name nginx -p 9001:80 -d nginx
# 将容器nginx.conf文件复制到宿主机
docker cp nginx:/etc/nginx/nginx.conf /home/nginx/conf/nginx.conf
# 将容器conf.d文件夹下内容复制到宿主机
docker cp nginx:/etc/nginx/conf.d /home/nginx/conf/conf.d
# 将容器中的html文件夹复制到宿主机
docker cp nginx:/usr/share/nginx/html /home/nginx/
```

![](https://i-blog.csdnimg.cn/blog_migrate/4682ca15c7b501b4c473c68ab93c153a.png)

#### 4、创建Nginx容器并运行

> Docker 创建Nginx容器

```java
# 直接执行docker rm nginx或者以容器id方式关闭容器
# 找到nginx对应的容器id
docker ps -a
# 关闭该容器
docker stop nginx
# 删除该容器
docker rm nginx

# 删除正在运行的nginx容器
docker rm -f nginx
```

```java
docker run \
-p 9002:80 \
--name nginx \
-v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/conf/conf.d:/etc/nginx/conf.d \
-v /home/nginx/log:/var/log/nginx \
-v /home/nginx/html:/usr/share/nginx/html \
-d nginx:latest
```

|                          命令                          |                  描述                  |
|------------------------------------------------------|--------------------------------------|
| --name nginx                                         | 启动容器的名字                              |
| -d                                                   | 后台运行                                 |
| -p 9002:80                                           | 将容器的 9002(后面那个) 端口映射到主机的 80(前面那个) 端口 |
| -v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf | 挂载nginx.conf配置文件                     |
| -v /home/nginx/conf/conf.d:/etc/nginx/conf.d         | 挂载nginx配置文件                          |
| -v /home/nginx/log:/var/log/nginx                    | 挂载nginx日志文件                          |
| -v /home/nginx/html:/usr/share/nginx/html            | 挂载nginx内容                            |
| nginx:latest                                         | 本地运行的版本                              |
| \\                                                   | shell 命令换行                           |

![](https://i-blog.csdnimg.cn/blog_migrate/68ac49a72c1955f71dcb31d6f4761b72.png)
> 单行模式

```java
docker run -p 9002:80 --name nginx -v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/conf/conf.d:/etc/nginx/conf.d -v /home/nginx/log:/var/log/nginx -v /home/nginx/html:/usr/share/nginx/html -d nginx:latest
```

#### 5、结果检测

![](https://i-blog.csdnimg.cn/blog_migrate/1fe1e2ebf9e0fd53c0f557eaec0935e9.png)

#### ![](https://i-blog.csdnimg.cn/blog_migrate/990044fce5cab83d7afc9e6d77c3c56f.png)

#### 6、修改内容进行展示

![](https://i-blog.csdnimg.cn/blog_migrate/42c8c4971ea3acc6f5a4b8e20870ea73.png)

```java
# 重启容器
docker restart nginx
```

![](https://i-blog.csdnimg.cn/blog_migrate/4002474f3ab6f2ff5db31838b5348eb5.png)





