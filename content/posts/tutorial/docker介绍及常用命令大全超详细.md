---
title: "docker介绍及常用命令大全（超详细）"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "docker介绍及常用命令大全（超详细）"
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

<br />

#### docker介绍及常用命令大全

* [Docker介绍](#Docker_5)
*
  * [1.什么是Docker](#1Docker_7)
  * [2.Docker的特点](#2Docker_15)
  * [3.Docker与虚拟机的区别](#3Docker_36)
* [Docker命令](#Docker_49)
*
  * [1.docker基础命令](#1docker_62)
  * [2.docker镜像命令](#2docker_121)
  * [3.docker容器命令](#3docker_182)

<br />

*** ** * ** ***

Docker介绍
------------------------------------

### 1.什么是Docker

Docker是一个开源的应用容器引擎，它基于go语言开发，并遵从Apache2.0开源协议。使用Docker可以让开发者封装他们的应用以及依赖包到一个可移植的容器中，然后发布到任意的Linux机器上，也可以实现虚拟化。Docker容器完全使用沙箱机制，相互之间不会有任何接口，这保证了容器之间的安全性。

Docker诞生于2013年初，目前有两个版本，Community Edition（CE，社区版）和Enterprise Edition（EE，企业版）。其中Docker社区版是免费开源的，对于个人和小团队来说是比较理想的选择；Docker企业版则是收费的，是专门为企业和大型IT团队提供的，用于要求比较严格的商业应用中。

对于初学者而言，使用Docker社区版就足以满足需求。

### 2.Docker的特点

在学习一项技术时，了解该技术的特点才能更好地实际运用。Docker作为当前主流的一个开源容器引擎，其主要特点如下。

**1．更快速的交付和部署**

开发者可以使用一个标准的Docker镜像来构建一套开发容器，开发完成之后，运维人员可以直接使用这个容器来部署代码。Docker可以快速创建容器以及快速迭代应用程序，并让整个过程全程可见，使团队中的其他成员更容易理解应用程序是如何创建和工作的。Docker容器轻，且启动速度快，可以大量地节约开发、测试和部署的时间。

**2．更高效的虚拟化**

Docker容器在运行时，不需要额外的虚拟机程序的支持。由于它是内核级的虚拟化，所以可以实现更高的性能和效率。

**3．更轻松的迁移和扩展**

Docker容器几乎可以在任意的平台上运行，包括物理机、虚拟机、公有云、私有云、个人电脑和服务器等。这种良好的兼容性可以让用户把一个应用程序从一个平台直接迁移到另外一个平台，十分有利于应用的迁移和扩展。

**4．更简单的管理**

使用Docker，只需要小小的修改，就可以替代以往大量的更新工作。所有的修改都以增量的方式被分发和更新，从而实现自动化并且高效的管理。

除上述几个特点外，Docker还具职责的逻辑分离、适合与面向服务的架构配合使用等特点，这里就不做具体说明了，读者在学习过程中可以深入体会。

### 3.Docker与虚拟机的区别

了解了Docker的概念和特点后，相信很多人对Docker与虚拟机的区别会产生疑惑，那么他们到底有什么不同呢？下面通过一张对比图来说明两者的主要区别，如图1所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/c6e5d9d23c044bd38d35c29a5d797f0f.png#pic_center)  
图1 Docker与虚拟机对比

<br />

从图1的对比中可以看出，虚拟机是运行在每个应用层级的客户端操作系统上的，这是资源密集型的。由于产生的磁盘镜像和应用程序的操作系统设置相互交叉，所以导致虚拟机对系统的依赖性很强，一旦系统出现问题，虚拟机依赖的文件以及安全补丁等都可能会出现文件丢失的情况。

Docker中的**容器是基于进程的隔离** ，多个容器可以共享单个内核，并且创建Docker容器的镜像所需要的配置并不依赖于宿主机系统。正是因为容器直接配置的**隔离性**，容器之间就没有配置交叉，所以Docker的应用可以运行在任何地方。

*** ** * ** ***

Docker命令
--------------------

docker中的镜像就像是咱们java 中的Class ，而容器呢 是基于这个镜像构建出的实例 类似于咱java 中 根据Class构造出的一个个实例对象 ，理解有误还请见谅...

按我理解 简言之

```prism language-java
docker 镜像： ----java中 class

docker容器 ： ----java中 class new 出来的实例对象
```

本篇文章不仅仅是生硬的命令大全，也是我个人对docker学习使用的一步步总结，通过一个个示例来加深对docker相关命令的理解以及记忆。

### 1.docker基础命令

**启动docker**

```prism language-shell
systemctl start docker
```

**关闭docker**

```prism language-shell
systemctl stop docker
```

**重启docker**

```prism language-shell
systemctl restart docker
```

**docker设置随服务启动而自启动**

```prism language-java
systemctl enable docker
```

**查看docker 运行状态**

------如果是在运行中输入命令后会看到绿色的active

```prism language-shell
systemctl status docker
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/cc750c6f52cf45c9804ee0dc7a198ec7.jpeg#pic_left)

**查看docker 版本号信息**

```prism language-shell
docker version
docker info
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/f32fe53fc1424988b7b3b53a6f838270.jpeg#pic_left)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/3578ca9deda8458cb57f4dc374ad46df.jpeg#pic_left)

**docker 帮助命令**

忘记了某些命令便可使用此进行查看与回顾

```prism language-shell
docker --help
```

```prism language-shell
docker pull --help
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/af1452f84aaa49d799c93eba47a6e3d2.jpeg#pic_left)

### {#2docker_121}2.docker镜像命令

> docker search 镜像id或name：在Docker Hub（或其他镜像仓库如阿里镜像）仓库中搜索关键字的镜像  
> docker pull 镜像id或name：从仓库中下载镜像，若要指定版本，则要在冒号后指定  
> docker images：列出已经下载的镜像，查看镜像  
> docker rmi 镜像id或name：删除本地镜像  
> docker rmi -f 镜像id或name: 删除镜像  
> docker build：构建镜像

**查看自己服务器中docker 镜像列表**

```prism language-shell
docker images
```

**搜索镜像**

```prism language-shell
docker search 镜像名
docker search --filter=STARS=9000 mysql 搜索 STARS >9000的 mysql 镜像
```

**拉取镜像** 不加tag(版本号) 即拉取docker仓库中该镜像的最新版本latest，加:tag则是拉取指定版本

```prism language-shell
docker pull 镜像名 
docker pull 镜像名:tag
```

拉取最新版 mysql（不指定版本号默认拉取最新版）

```prism language-c
docker pull mysql
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/f95eee5abf784b20bc4b9a211210fbb0.jpeg#pic_left)

咱再来拉取一个指定版本号 镜像 至于版本号呢 可以在docker hub中查看

[docker官方镜像搜索](https://hub.docker.com/search?type=image)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/6498c2b4fe544cb2a16851d54999424b.png#pic_left)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/4c0986a5068b48c5adf1a9c9626442dd.png#pic_center)  
比如：拉取 mysql 5.7.40

```prism language-c
docker pull mysql:5.7.40
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/53ed4c15d8874c7bacdc8c30ad4dc762.jpeg#pic_left)

**运行镜像** ----比如拉取一个tomcat 跑起来试一试

```prism language-powershell
docker pull tomcat
```

如下图所示：拉取了tomcat最新版本  
![在这里插入图片描述](https://img-blog.csdnimg.cn/bce36421dd69489883dd3284065ced73.jpeg#pic_center)

```prism language-shell
docker run 镜像名
docker run 镜像名:Tag
```

### 3.docker容器命令

> docker ps：列出运行中的容器  
> docker ps -a ： 查看所有容器，包括未运行  
> docker stop 容器id或name：停止容器  
> docker kill 容器id：强制停止容器  
> docker start 容器id或name：启动已停止的容器  
> docker inspect 容器id：查看容器的所有信息  
> docker container logs 容器id：查看容器日志  
> docker top 容器id：查看容器里的进程  
> docker exec -it 容器id /bin/bash：进入容器  
> exit：退出容器  
> docker rm 容器id或name：删除已停止的容器  
> docker rm -f 容器id：删除正在运行的容器  
> docker exec -it 容器ID sh :进入容器

