---
title: "Git三种方法从远程仓库拉取指定的某一个分支"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Git三种方法从远程仓库拉取指定的某一个分支"
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

**目录**

[一、git clone 命令获取]
[二、git fetch 命令获取]
[三、 git checkout -b 命令获取]

*** ** * ** ***

平时在做项目的时候，我们很多时候都是使用Git来管理我们项目代码的版本。当从远程仓库拉取代码的时候一般都是使用git clone \<url\>命令来获取，这样我们是将项目整个都克隆到我们本地仓库，并且分支还是主分支maser。当然有时候我们只需拉取远程仓库指定的某一个分支，那么用git命令该怎么拉取指定某一个分支呢？下面楼主记录了三种方法供大家参考。

#### 一、git clone 命令获取

使用git管理代码版本的时候，本地分支默认与远程同名分支建立追踪关系。文章开始也提到git clone \<url\>命令默认将整个远程版本库克隆到本地，但是git clone -b \<分支名称\>命令可以将指定的某一个远程分支拉取到我们本地，而且拉取的本地分支自动和远程同名分支建立追踪关系，它不会将新创建的HEAD指向克隆仓库中HEAD指向的maser主分支，而是指向我们刚拉取下来的分支；示例如下：

```java
$ git clone -b dev开发分支 https://github.com/521/springboot-rabbitmq.git
```

分支拉取结束以后，执行git branch -a 命令，结果如下：

```java
$ git branch -a
* dev开发分支
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev开发分支
  remotes/origin/master
  remotes/origin/test测试分支
```

从执行结果可以看出，当前分支为"dev开发分支" ，剩余的四个都是远程分支；

注意：用git clone -b \<分支名称\> \<url\>拉取指定的某一个分支时，我们本地是没有克隆url对应的远程仓库代码，也就是说进入某个路径的文件夹后直接使用该命令去拉取指定分支，而不是先执行git clone \<url\>将远程代码库克隆到本地以后进入项目目录再执行该命令去拉取指定分支。否则，拉取下来的还是master主分支。

#### 二、git fetch 命令获取 {#%E4%BA%8C%E3%80%81git%20fetch%20%E5%91%BD%E4%BB%A4%E8%8E%B7%E5%8F%96}

git fetch命令从远程仓库拉取指定某一个分支时，和上面的git clone -b命令时不一样。使用git fetch命令时，需要先将远程的仓库克隆到本地，然后在执行git fetch命令。该命令执行完以后还是处在master主分支的，如果进去目录没有发现想要拉取的仓库文件。此时并不是git fetch命令没有执行，而是我们还没有切换到我们想要拉取的分支上，需要我们执行git checkout命令切换到我们想要拉取的分支上。示例如下：

1.拉取整个远程代码库

```java
$ git clone https://github.com/521/springboot-rabbitmq.git
```

2.进入项目目录，也就是进入master主分支

```java
$ cd springboot-rabbitmq/
```

3.执行git fetch命令，将远程仓库的所有分支拷贝到本地仓库

```java
$ git fetch
```

4.执行git checkout \<分支名称\>命令，切换到我们想要拉取的指定某一个分支的本地分支

```java
$ git checkout dev开发分支
```

这是就会看到本地仓库的dev开发分支和远程仓库的dev开发分支一样的内容， 默认情况下，git fetch下载的分支和远程的分支名相同。

5.执行git branch -a命令查看所有分支情况

```java
$ git branch -a
* dev开发分支
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev开发分支
  remotes/origin/master
  remotes/origin/test测试分支
```

可以看到本地有两个分支，目前所在的分支为dev开发分支，还有三个远程分支。到此通过git fetch命令来获取远程仓库某一个指定分支的步骤就执行完毕了。

#### 三、 git checkout -b 命令获取 

第三种获取远程仓库某一个指定分支的方法和第二种方法有点类似，都是将远程仓库克隆到本地仓库，然后执行git checkout -b \<本地分支名称\> origin/\<远程分支名称\>，具体步骤如下：

1.进入某个文件夹执行克隆远程仓库的git

```java
$ git clone https://github.com/521/springboot-rabbitmq.git
```

此时远程仓库就克隆到了本地仓库。

2. 进入项目目录，也就是进入master主分支

```java
$ cd springboot-rabbitmq/
```

3.执行git branch -a查看所有分支名称，\* 号表示当前分支

```java
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev开发分支
  remotes/origin/master
  remotes/origin/test测试分支
```

4.执行git checkout -b \<本地分支名称\> origin/\<远程分支名称\>，拉取指定的某一个分支

```java
$ git checkout -b dev开发分支 origin/dev开发分支
```

该命令的作用是：checkout远程仓库origin的分支"dev开发分支"，在本地起名为"dev开发分支"分支，并切换到本地的"dev开发分支"分支。

5.拉取该分支的最新代码

```java
$ git pull origin dev开发分支
```

至此，通过三种方法从远程仓库拉取指定某一分支就总结完了，如果有不同见解请指出。

