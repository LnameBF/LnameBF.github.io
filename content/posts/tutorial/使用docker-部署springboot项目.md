---
title: "使用docker 部署springboot项目"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "使用docker 部署springboot项目"
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

本文主要讲如何将一个jar包打包成一个docker镜像，并运行起来，这里我以metabase.jar为例，这个jar包可以直接使用下面命令进行运行：
> java -jar shiyi-blog.jar

接下来将他打包成docker镜像，直接在docker中运行，第一步没有安装docker的首先要安装docker，安装docker不为本教程重点，可点击如下链接：[docker介绍和安装](https://blog.csdn.net/Jarbein/article/details/103627522)

### 一，编写dockerfile

1.这个jar包可以放到你想放的地方，然后在与jar包相同的文件夹中运用下边命令创建dockerfile文件并进行编辑
> touch DockerFile

2.添加如下内容，当然如果是其它jar包要记得更换名字，不要照抄  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/425bd75618ea12171df540260b794aff.png)

```prism language-java
#penjdk:8-jre 为基础镜像，来构建此镜像，可以理解为运行的需要基础环境
FROM openjdk:8-jre
#WORKDIR指令用于指定容器的一个目录， 容器启动时执行的命令会在该目录下执行。
WORKDIR /opt/docker/images/jar/
#将当前metabase.jar 复制到容器根目录下
ADD shiyi-blog.jar shiyi-blog.jar
#将依赖包 复制到容器根目录/libs下,metabase.jar已不再需要添加其它jar包
#ADD libs /libs
#暴露容器端口为3000 Docker镜像告知Docker宿主机应用监听了3000端口
EXPOSE 3000
#容器启动时执行的命令
CMD java -jar metabase.jar
```

创建后：  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f55d528b760a011c8703879808a5b798.png)

### 二，使用docker build构建镜像

*注意：这个命令是在我的DockerFile所在目录执行的，还有别忘记了命令后边的空格和点*

```prism language-java
//docker build
// . 表示当前目录 -f 参数指定Dockerfile文件  -t 表示 制作的镜像:tag  注意最后加上空格和.
docker build -f DockerFile  -t  test/shiyi-blog:1.0.0  .
```

构建完成后使用以下命令查看是否有我们的test/metabase镜像
> docker images

以下是我的查询结果：  
*openjdk是我们编写DockerFile时添加的依赖镜像，所以docker会自动根据dockerfile将openjdk镜像下拉下来*   
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4ff459b58095e925db572fecdfd84560.png)

### 三，运行docker镜像

> //-d 表示后台运行容器 ,-p表示将宿主机的3000端口映射到容器中的3000端口  
> docker run -d -p 3000:3000 test/metabase:1.0.0

一个镜像每run一次就会新增一个容器，也就是一个镜像可以对应多个容器  
然后我们查看容器发现已经有了docker容器，使用以下命令
> docker ps -a

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d2ffa752bd5b3a0515bbb7538108ff40.png)

### 四，启动停止容器

启动(containerid就是docker ps -a 查询出来的containerid)：
> docker start containerid

