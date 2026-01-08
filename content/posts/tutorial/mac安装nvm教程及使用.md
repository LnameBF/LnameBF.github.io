---
title: "Mac安装nvm教程及使用"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Mac安装nvm教程及使用"
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

#### 目录

* [1、直接通过brew安装](#1brew_3)
* [2、配置环境变量](#2_15)
*
  * [1) 执行命令：open -e \~/.bash_profile](#1_open_e_bash_profile_16)
  * [2）执行：open -e \~/.zshrc](#2open_e_zshrc_25)
  * [3）分别执行](#3_33)
  * [4）注意：以上3步执行完还不行，再继续这一步](#43_42)
* [3、nvm 常用命令](#3nvm__60)

<br />

nvm 是 node 版本管理器，也就是说一个 nvm 可以管理多个 node 版本（包含 npm 与 npx），可以方便快捷的安装、切换 不同版本的 node。

 1、直接通过brew安装
---------------------------------------

执行命令：`brew install nvm`

PS： 如果没有安装brew，可参考：[brew安装小白教程](https://blog.csdn.net/ganyingxie123456/article/details/132182152?spm=1001.2014.3001.5502)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/18d32b52d2e5cc2ab8871882a947f06a.png)

安装成功显示如下：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c9b3d100bbd5d7c3d08c8b09c5995d5e.png)

2、配置环境变量
---------------

### 执行命令：open -e \~/.bash_profile

添加

```prism language-javascript
export NVM_DIR=~/.nvm 
source $(brew --prefix nvm)/nvm.sh
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0d1e3cc886dd16a5330d3cfd78c0e105.png)  
\*\*注意：\*\*如果打开`.bash_profile`这一步报错`.bash_profile does not exist`，需要建立`.bash_profile` 文件，使用`touch .bash_profile`命令

### 执行：open -e \~/.zshrc

添加

```prism language-javascript
export NVM_DIR=~/.nvm 
source $(brew --prefix nvm)/nvm.sh
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/fa2975f991b4c1b165a49f08707dbff1.png)

### 分别执行

```prism language-javascript
source ~/.bash_profile 
source ~/.zshrc
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a35cdcb55ec5e2033bd07d720957af59.png)  
到这一步，基本上nvm就可以正常使用了，通过 `nvm -v` 可查看到安装的版本  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/77e851c48c8c25c24a69503ac47cf2bc.png)

### 注意：以上3步执行完还不行，再继续这一步

继续执行命令： `open -e ~/.profile`

添加：

```prism language-javascript
export NVM_DIR=~/.nvm
source $(brew --prefix nvm)/nvm.sh
```

再分别执行以下命令：

```prism language-javascript
source ~/.bash_profile 
source ~/.zshrc 
source ~/.profile
```

3、nvm 常用命令
---------------------

```prism language-javascript
// 查看当前使用的node版本
nvm current

// 查看所有已经在本地安装的node版本
nvm ls

// 查看所有可下载的版本
nvm list available

// 安装最新版 node
nvm install stable 

// 使用最新版本的node
nvm use node

// 安装指定版本的node
nvm install node版本号（如：nvm install 14.16.0）

// 使用指定版本的node
nvm use node版本号

// 卸载指定版本的node
nvm uninstall node版本号

// 设置node别名
nvm alias node别名 node版本号

// 取消别名
nvm unalias node别名

// 设置默认使用的node版本
nvm alias default node版本号
```


