---
title: "在Ubuntu 22.04 LTS 上安装 MySQL两种方式：在线方式和离线方式"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "在Ubuntu 22.04 LTS 上安装 MySQL两种方式：在线方式和离线方式"
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

Ubuntu安装MySQL {#content_views}
----------------------------------------------

介绍：

[Ubuntu](https://cn.ubuntu.com/) 是一款基于Linux操作系统的免费开源发行版，广受欢迎。它以稳定性、安全性和用户友好性而闻名，适用于桌面和服务器环境。Ubuntu提供了大量的软件包和应用程序，拥有庞大的社区支持和活跃的开发者社区。它的长期支持（LTS）版本获得5年的安全更新，为企业和个人提供了可靠的选择。 Ubuntu的桌面版本具有直观的用户界面，而服务器版本则适用于构建强大的Web服务器和云计算平台。  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/61b19448b3cf469f1aca350fefc13226.png)  
当你需要在Ubuntu上安装MySQL时，有两种主要的方式：在线安装和离线安装。在线安装是通过Ubuntu软件包管理器直接下载和安装MySQL，而离线安装则涉及手动下载MySQL安装包并在离线环境中进行安装。

### Ubuntu和MySQL默认版本对照

以下是一个以表格形式列出了不同Ubuntu版本和它们通常默认安装的MySQL版本：

|    Ubuntu 版本     | 默认 MySQL 版本 |
|------------------|-------------|
| Ubuntu 22.04 LTS | MySQL 8.0   |
| Ubuntu 20.04 LTS | MySQL 8.0   |
| Ubuntu 18.04 LTS | MySQL 5.7   |
| Ubuntu 16.04 LTS | MySQL 5.7   |
| Ubuntu 14.04 LTS | MySQL 5.5   |
| Ubuntu 12.04 LTS | MySQL 5.5   |

### 在线安装MySQL

#### 步骤1：更新软件包列表

在进行任何软件安装之前，请确保你的系统的软件包列表是最新的。打开终端并运行以下命令：

```prism language-bash
sudo apt update
```

#### 步骤2：安装MySQL服务器

在更新软件包列表后，这里我们可以查看一下可使用的MySQL安装包：

```prism language-bash
# 查看可使用的安装包
sudo apt search mysql-server
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/866b2ffec9a5f9eabbc6323b732a1b5b.png)

接下来可以使用以下命令安装MySQL服务器：

```prism language-bash
# 安装最新版本
sudo apt install -y mysql-server
# 安装指定版本
sudo apt install -y mysql-server-8.0
```

如果不加`-y` 会在安装过程中，系统将提示你设置MySQL的root密码。确保密码足够强，且记住它，因为你将在以后需要用到它。

#### 步骤3：启动MySQL服务

安装完成后，MySQL服务会自动启动，未启动则使用以下命令启动MySQL服务：

```prism language-bash
sudo systemctl start mysql
```

并将MySQL设置为开机自启动：

```prism language-bash
sudo systemctl enable mysql
```

#### 步骤4：检查MySQL状态

你可以使用以下命令来检查MySQL是否正在运行：

```prism language-bash
sudo systemctl status mysql
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ac75e0e71daf863c252f590642ae6b50.png)

至此，你已经成功在线安装了MySQL服务器。

#### 步骤5：修改密码、权限

默认安装是没有设置密码的，需要我们自己设置密码。

```prism language-sql
# 登录mysql，在默认安装时如果没有让我们设置密码，则直接回车就能登录成功。
mysql -uroot -p
# 设置密码 mysql8.0
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
# 设置密码 mysql5.7
set password=password('新密码');
# 配置IP 5.7
grant all privileges on *.* to root@"%" identified by "密码";
# 刷新缓存
flush privileges;
```

**注意** ：配置8.0版本参考：我这里通过这种方式没有实现所有IP都能访问；我是通过直接修改配置文件才实现的，MySQL8.0版本把配置文件 `my.cnf` 拆分成`mysql.cnf `和`mysqld.cnf`，我们需要修改的是`mysqld.cnf`文件：

如果遇到s not allowed to connect to this MySQL server 的报错
~~~
1、执行登陆MySQL mysql -u root -p 密码
2、执行use mysql;
3、执行update user set host = '%' where user = 'root';
4、执行flush privileges;
~~~

```prism language-bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

修改 `bind-address`，保存后重启MySQL即可。

```prism language-bash
bind-address            = 0.0.0.0
```

重启MySQL重新加载一下配置：

```prism language-bash
sudo systemctl restart mysql
```

### 离线安装MySQL

如果你需要在没有互联网连接的环境中安装MySQL，可以在另一台联网的计算机上下载MySQL的安装包，然后将其传输到目标Ubuntu机器上进行安装。

#### 步骤1：下载MySQL安装包

在联网的计算机上，访问 [MySQL官方网站](https://dev.mysql.com/downloads/mysql/) 或者MySQL的软件仓库，然后下载适合你系统版本的MySQL安装包（通常是.deb文件）。将下载的文件保存到一个可移动的存储设备上。

#### 步骤2：传输安装包到目标机器

将下载的MySQL安装包从联网计算机传输到目标Ubuntu机器。你可以使用USB驱动器、外部硬盘、网络传输等方法来完成这个步骤。

#### 步骤3：安装MySQL

在目标Ubuntu机器上，使用以下命令来安装MySQL：

##### deb.文件安装方法

```prism language-bash
sudo dpkg -i mysql-package-name.deb
```

请将 `mysql-package-name.deb` 替换为你下载的MySQL安装包的文件名。

##### .tar.gz 文件安装方法

```prism language-bash
# 解压文件
tar -zxvf mysql-package.tar.gz 
# 安装
cd /tmp/mysql-package/bin/
./mysql_install_db
```

请将 mysql-package.tar.gz 替换为你实际下载的MySQL安装包的文件名。

#### 步骤4：配置MySQL

安装完成后，你可以使用以下命令启动MySQL服务：

```prism language-bash
sudo systemctl start mysql
```

然后将MySQL设置为开机自启动：

```prism language-bash
sudo systemctl enable mysql
```

#### 步骤5：检查MySQL状态

使用以下命令来检查MySQL是否正在运行：

```prism language-bash
sudo systemctl status mysql
```

至此，你已经成功在离线环境中安装了MySQL服务器。

总结起来，你可以根据你的网络连接情况选择在线或离线安装MySQL。在线安装更加简单，但离线安装可以让你在没有网络连接的情况下进行安装。希望这篇博客对你有所帮助，顺利安装MySQL！如果你需要更详细的步骤或进一步的配置，请查阅MySQL官方文档或相关教程。

