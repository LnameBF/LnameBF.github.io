---
title: "MacOS上Homebrew 安装、配置、更改国内镜像源及使用教程"
date: 2026-01-07T20:41:46+08:00
lastmod: 2026-01-07T20:41:46+08:00
author: ["wait"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- 教程
description: "MacOS上Homebrew 安装、配置、更改国内镜像源及使用教程"
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
Homebrew笔记 
----------------------------------------

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6130dc8c429e43f6993239736763a11a.png)

### 1. 介绍

> **官网：** [**https://brew.sh/**](https://brew.sh/)

对于习惯了使用命令来完成一切的程序员来说，安装软件这种小事，自然是能够用命令解决，就不用图形界面选择。但是在 `Linux` 中，我们有 `yum`、`apt`、`dnf`、`pkg`等命令来完成软件的安装，`macOS` 却并未为我们提供一个好用的包管理器，帮助我们更好的使用 `macOS`。

好在，虽然官方没有提供，我们却可以使用 Homebrew 这一神器，来完成类似的工作，就如同 `Homebrew` 的 Slogan ："The missing package manager for macOS (or Linux)"

`Homebrew` 由开发者 Max Howell 开发，并基于 BSD 开源，是一个非常方便的包管理器工具。在早期， `Homebrew` 仅有 `macOS` 的版本，后续随着用户的增多，`Homebrew` 还提供了 `Linux` 的版本，帮助开发者在 Linux 同样使用 `Homebrew` 来配置环境。

#### 1.1 组成部分

`Homebrew` 是一款包管理工具，目前支持 `macOS` 和 `linux` 系统。主要有四个部分组成: `brew`、`homebrew-core` 、`homebrew-cask`、`homebrew-bottles`。

| 名称                   | 说明                     |
|:---------------------|:-----------------------|
| **brew**             | Homebrew 源代码仓库         |
| **homebrew-core**    | Homebrew 核心源           |
| **homebrew-cask**    | 提供 macOS 应用和大型二进制文件的安装 |
| **homebrew-bottles** | 预编译二进制软件包              |

#### 1.2 核心概念

在正式介绍 `Homebrew` 的使用之前，我先为你介绍一下 `Homebrew` 中的一些核心的概念，了解这些概念，就可以帮助你更好的去使用 `Homebrew`。

| 词汇              | 含义                                                  |
|:----------------|:----------------------------------------------------|
| **formula (e)** | 安装包的描述文件，formulae 为复数                               |
| **cellar**      | 安装好后所在的目录                                           |
| **keg**         | 具体某个包所在的目录，keg 是 cellar 的子目录                        |
| **bottle**      | 预先编译好的包，不需要现场下载编译源码，速度会快很多；官方库中的包大多都是通过 bottle 方式安装 |
| **tap**         | 下载源，可以类比于 Linux 下的包管理器 repository                   |
| **cask**        | 安装 macOS native 应用的扩展，你也可以理解为有图形化界面的应用。             |
| **bundle**      | 描述 Homebrew 依赖的扩展                                   |

其中，最关键的是 `tap` 、`cask`，我们在后续会经常用到。

### 2. 安装

复制下方命令到终端一键执行即可，我这里用的第一个，是一个知乎博主分享的。

**苹果电脑 常规安装脚本（推荐 完全体 几分钟安装完成）**：

```prism language-bash
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

**苹果电脑 极速安装脚本（精简版 几秒钟安装完成）：**

```prism language-shell
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)" speed
```

**苹果电脑 卸载脚本：**

```prism language-shell
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"
```

**常见错误** 去下方[地址](https://link.zhihu.com/?target=https%3A//gitee.com/cunkai/HomebrewCN/blob/master/error.md)查看

```prism language-http
https://gitee.com/cunkai/HomebrewCN/blob/master/error.md
```

**Linux电脑** 安装脚本：

```prism language-bash
rm Homebrew.sh ; wget https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh ; bash Homebrew.sh
```

**Linux电脑** 卸载脚本：

```prism language-bash
rm HomebrewUninstall.sh ; wget https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh ; bash HomebrewUninstall.sh
```

### 3. 更换为国内镜像源

本教程以更改中科大源为例。

**中科大源地址** ：<https://mirrors.ustc.edu.cn/>

* `Homebrew` 源使用帮助 <http://mirrors.ustc.edu.cn/help/brew.git.html>
* `Homebrew Core` 源使用帮助 <http://mirrors.ustc.edu.cn/help/homebrew-core.git.html>
* `Homebrew Cask` 源使用帮助 <http://mirrors.ustc.edu.cn/help/homebrew-cask.git.html>
* `Homebrew Bottles` 源使用帮助 <http://mirrors.ustc.edu.cn/help/homebrew-bottles.html>

最终效果查看，最下面的两行 `origin` 表示，按照 `3.1` 教程演示即可

```prism language-shell
# 切换当前工作目录到 Homebrew 的仓库目录
cd "$(brew --repo)" 

#显示当前工作目录的路径
pwd

# 查看当前 Git 仓库所配置的远程仓库的详细信息
git remote -v
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/927fb0376b9b42eabf59c19848477656.png)

`git remote -v` 命令用于查看当前 `Git` 仓库的远程仓库信息。

输出结果显示 `origin https://mirrors.ustc.edu.cn/brew.git (fetch)` 和 `origin https://mirrors.ustc.edu.cn/brew.git (push)` 。这表明当前 `Homebrew` 仓库的远程源（名为`origin`）的 `URL` 是 `https://mirrors.ustc.edu.cn/brew.git` 。`(fetch)`表示从这个 `URL` 拉取代码更新，`(push)`表示向这个 `URL` 推送代码更改。

**以上为最终效果，大家最后有时间看就行**

#### 3.1 更换 brew.git 源

执行下方命令，并看到 `ORIGNN` 地址已经更新

```prism language-shell
# 切换到 Homebrew 的仓库目录
cd "$(brew --repo)"

# 设置 Homebrew 仓库的远程源 URL 为中国科学技术大学的镜像地址
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git

# 显示当前 Homebrew 的配置信息，如各种配置参数，如版本号、安装路径、镜像地址等
brew config
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/81dba4170f694034a57376976c998b2a.png)

重置为官方镜像：

```prism language-shell
cd "$(brew --repo)"

git remote set-url origin https://github.com/Homebrew/brew.git
```

#### 3.2 更换 homebrew-core.git 源

通过下方命令发现错误，这个错误信息表明 `Git` 认为在 "`/opt/homebrew/Library/Taps/homebrew/homebrew-core`" 这个路径下的仓库所有权存在可疑情况。这通常是由于安全机制引起的，`Git` 可能担心该仓库不是在安全的环境下或者可能存在恶意操作的风险。

```prism language-shell
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"

git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/eb2b257552c746a09291dc225c2f2865.png)

按照要求执行命令，这个命令会将指定的目录添加到 `Git` 的安全目录列表中，告诉 `Git` 这个目录是安全的，可以进行操作。这样，在后续执行与这个仓库相关的 `Git` 操作时，就不会再出现这个错误了。

```prism language-shell
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-core

git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3d552d8914014c9ebd8f6dedb279cc8f.png)

```prism language-shell
# 重置为官方地址：
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core
```

默认不安装 `cask` 有需要的可以替换（`Homebrew cask` 软件仓库，提供 `macOS` 应用和大型二进制文件），这个我没弄，我就不演示了。

```prism language-shell
cd "$(brew --repo)"/Library/Taps/homebrew/homebrew-cask
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
# git remote set-url origin git://mirrors.ustc.edu.cn/homebrew-cask.git

# 重置为官方地址：
cd "$(brew --repo)"/Library/Taps/homebrew/homebrew-cask
git remote set-url origin https://github.com/Homebrew/homebrew-cask
```

#### 3.3 替换 Homebrew-bottles 源

用以下命令查看终端使用的是 `Bash` 还是 `Zsh`

```prism language-shell
echo $SHELL
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e9c942258fa047fda38abf01377c3afa.png)

* `bash` 用户

```prism language-shell
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile

source ~/.bash_profile
```

* `zsh` 用户

```prism language-shell
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc

source ~/.zshrc
```

`brew4.x` 系列有个 `api` 加速环境变量，是如下命令。**阿里云镜像** 是上下两个变量`HOMEBREW_BOTTLE_DOMAIN` 和 `HOMEBREW_API_DOMAIN` 都有的，只需要吧里面的 `ustc` 换成 `aliyun`即可 。然后这里我使用中科大 `api` 加速。后续 `Homebrew` 在使用 `API` 时会通过中科大的镜像加速地址来提高访问速度。

* `bash` 用户

```prism language-bash
echo 'export HOMEBREW_API_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/api'>>~/.bash_profile

source ~/.bash_profile
```

* `zsh` 用户

```prism language-bash
echo 'export HOMEBREW_API_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/api'>>~/.zshrc

source ~/.zshrc
```

### 4. 常用命令

#### 4.1 安装卸载软件

1. `brew --version` 或者 `brew -v` 显示 `brew` 版本信息
2. `brew install <formula>` 安装指定软件
3. `brew uninstall <formula>` 卸载指定软件
4. `brew list` 显示所有的已安装的软件
5. `brew search text` 搜索本地远程仓库的软件，已安装会显示绿色的勾
6. `brew search /text/` 使用正则表达式搜软件
7. `brew info <formula>` 显示指定软件信息
8. `brew reinstall <formula>` 重新安装指定软件，先卸载后安装
9. `brew install <formula> --build-from-source` 源码安装指定软件，可以给定指定参数
10. `brew commands` 列出所有可用命令

#### 4.2 升级软件相关

1. `brew update` 自动升级 homebrew （从 github 下载最新版本）
2. `brew outdated` 检测已经过时的软件
3. `brew upgrade` 升级所有已过时的软件，即列出的以过时软件
4. `brew upgrade <formula>` 升级指定的软件
5. `brew pin <formula>` 禁止指定软件升级
6. `brew unpin <formula>` 解锁禁止升级
7. `brew upgrade --all` 升级所有的软件包，包括未清理干净的旧版本的包
8. `brew edit <formula>` 编辑软件，不会的情况下慎用
9. `brew tap` 列出本地资源仓库，其中 homebrew 是默认仓库，其它都是第三方仓库
10. `brew tap <user/repo>` 添加第三方仓库，命名的规则按照 github 来定的。[使用](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.brew.sh%2FTaps)
11. `brew untap <user/repo>` 删除仓库
12. `brew deps <formula>` 查看指定软件依赖于哪些软件
13. `brew uses <formula>` 查看指定软件被哪些软件所依赖

#### 4.3 清理相关

`homebrew` 再升级软件时候不会清理相关的旧版本，在软件升级后我们可以使用如下命令清理

1. `brew cleanup -n` 列出需要清理的内容
2. `brew cleanup <formula>` 清理指定的软件过时包
3. `brew cleanup` 清理所有的过时软件
4. `brew unistall <formula>` 卸载指定软件
5. `brew unistall <fromula> --force` 彻底卸载指定软件，包括旧版本

通过 `brew` 安装的文件会自动设置环境变量，所以不用担心命令行不能启动的问题。  
比如安装好了 `gradle`，即可运行 `gradle -v`

#### 4.4 管理后台服务

`macOS` 使用 `launchctl` 命令加载开机自动运行的服务，`brew service` 可以简化 `lauchctl` 的操作。

以 `<font style="color:#ECAA04;">MySQL</font>` 为例，使用 `<font style="color:#ECAA04;">launchctl</font>` 启动:

```prism language-shell
ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

如使用 `brew service` 可以简化为:

```prism language-shell
brew services start mysql
```

* `services` 常用命令

1. `brew services list` # 查看使用brew安装的服务列表
2. `brew services run formula|`--all # 启动服务（仅启动不注册）
3. `brew services start formula|`--all # 启动服务，并注册
4. `brew services stop formula|`--all # 停止服务，并取消注册
5. `brew services restart formula|`--all # 重启服务，并注册
6. `brew services cleanup` # 清除已卸载应用的无用的配置

* 配置文件目录

```prism language-shell
/Library/LaunchDaemons # 开机自启，需要sudo
~/Library/LaunchAgents # 用户登录后自启
```

以 `homebrew.mxcl.kafka.plist` 为例：

```prism language-xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>homebrew.mxcl.kafka</string>
    <key>WorkingDirectory</key>
    <string>/usr/local</string>
    <key>ProgramArguments</key>
    <array>
      <string>/usr/local/opt/kafka/bin/kafka-server-start</string>
      <string>/usr/local/etc/kafka/server.properties</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>StandardErrorPath</key>
    <string>/usr/local/var/log/kafka/kafka_output.log</string>
    <key>StandardOutPath</key>
    <string>/usr/local/var/log/kafka/kafka_output.log</string>
  </dict>
</plist>
```

在这里可以找到服务路径、启动参数、日志路径等

#### 4.5 brew cask

`Homebrew Cask` 是 `Homebrew` 的扩展，借助它可以方便地在 `macOS` 上安装图形界面程序，即我们常用的各类应用。`Homebrew` 中文含义为自制、自酿酒，`Cask` 中文含义为桶、木桶，桶装酒是一种成品，也就是说每一个 `homebrew cask` 都可以直接使用的，比如 `Atom` 的 `Cask` 名称为 `atom`，那么就可以使用如下命令安装：

```prism language-bash
brew cask install atom
```

甚至也可以提交新的 `Cask`，比如假设有桌面客户端少数派，则用 `brew cask create sspai` 创建新的名称为 `sspai` 的 `Cask`，当然还要提供官网下载链接、官方主页、应用版本等信息，可以参照 [官方教程](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FHomebrew%2Fhomebrew-cask%2Fblob%2Fmaster%2FCONTRIBUTING.md)，此处就不再细说。
> **安装 Homebrew Cask【以后新版不需要安装】**   
> 安装好 brew 后，就可以执行命令 `brew tap caskroom/cask` 获取 Homebrew Cask。
>
> **注意：**   
> Caskroom 的 Git 地址在 2018 年 5 月 25 日从 [https://github.com/caskroom/homebrew-cask](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fcaskroom%2Fhomebrew-cask) 迁移到了 [https://github.com/Homebrew/homebrew-cask](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FHomebrew%2Fhomebrew-cask)  
> 所以，以后就不需要安装 Homebrew Cask

#### 4.6 使用 Homebrew Cask

`github` 使用：[https://github.com/Homebrew/homebrew-cask/blob/master/USAGE.md](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FHomebrew%2Fhomebrew-cask%2Fblob%2Fmaster%2FUSAGE.md)

1. `brew cask install <formula>` 安装指定图形界面软件
2. `brew cask uninstall <formula>` 卸载软件
3. `brew cask uninstall --force <formula>` 卸载软件，带参数
4. `brew cask search text` 搜索软件
5. `brew cask list` 列出所有通过 cask 安装的软件
6. 其它可以参考：[https://github.com/Homebrew/homebrew-cask/blob/master/USAGE.md](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FHomebrew%2Fhomebrew-cask%2Fblob%2Fmaster%2FUSAGE.md)

举例可安装的软件

```prism language-shell
# 安装chrome
brew cask install google-chrome

# 安装LaunchRocket
brew cask install launchrocket
```

### 5. 图像化工具 cakebrew

**官方地址** ：<https://www.cakebrew.com/>

`Github` **仓库地址** ：<https://github.com/brunophilipe/Cakebrew>

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b9cec48762634451979b939d087ef14d.png)

点击上方导航栏的 `Download`，再点击 `Download Now` 就下载安装包，然后解压的程序拖到应用程序即可。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6f95833ce98b4773ba9afdfe4b829079.png)

或者通过 `Homebrew` 命令安装：

```prism language-bash
brew install --cask cakebrew
```

打开后，有点不太稳定，显示不了界面

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4551fbb3cae24de8b34e9c5b109f5431.png)

大家可以到网上搜搜使用教程，比命令行方便些。

### 参考

> 1. <https://zhuanlan.zhihu.com/p/111014448> 安装
> 2. <https://sspai.com/post/56009#!> 1.2 核心概念、3
> 3. <https://blog.csdn.net/Amio_/article/details/109694893> 1.1 组成部分
> 4. <https://cloud.tencent.com/developer/article/1867824>
> 5. <https://zhuanlan.zhihu.com/p/366354601> 介绍 Homebrew 图形化工具 cakebrew





