---
title: "Linux基础命令"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Linux基础命令"
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

# linux命令

## ps -ef|grep详解
ps命令将某个进程显示出来

grep命令是查找

中间的|是管道命令 是指ps命令与grep同时执行

PS是LINUX下最常用的也是非常强大的进程查看命令

grep命令是查找，是一种强大的文本搜索工具，它能[使用正则表达式](https://www.baidu.com/s?wd=使用正则表达式&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1d9uWD3PhP9n1b4m1nduAcz0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPjfvrHnzPWT3)搜索文本，并把匹配的行打印出来。

grep全称是Global Regular Expression Print，表示全局正则表达式版本，它的使用权限是所有用户。

以下这条命令是检查java 进程是否存在：ps -ef |grep java

字段含义如下：
UID    PID    PPID    C   STIME   TTY    TIME     CMD

zzw   14124  13991   0   00:38   pts/0   00:00:00  grep --color=auto dae

## cd，ls命令使用

ls

**-a**列出目录下的所有文件，包括以 . 开头的隐含文件。

**-A** 显示指定目录下所有的子目录及文件包括隐藏文件，但不显示".",".."

**-c** 文件修改时间排序

列出 usr目录下的所有文件

```
ls -a /usr
```

![image-20230410150003090](https://file.heiok.top/note/202405072158346.png)

多个命令接上参数即可

##  文件夹命令

### 1.mkdir命令

命令格式：mkdir [选项] 目录...；

命令功能：通过mkdir命令可以实现在指定位置创建以 (指定的文件名)命名的文件夹或目录，要创建文件夹或目录的用户必须对所创建的文件夹的父文件夹具有写权限；

-m 创建文件夹得同时设定权限

在usr文件夹内创建文件夹aaa

```shell
mkdir -m usr/aaa
```

-p 新建多级文件夹，当路径中摸个目录文件夹存在时，跳过该目录继续创建下一级目录

-v 显示创建目录的过程信息

### 2.rm命令

删除当前目录下的文件夹

```sh
rmdir aaa
```

### 3.cp

复制文件命令 

* 复制文件test.txt到/usr/local目录

```
cp test.txt /usr/local
```

* 复制文件夹 yyTest到/usr/local目录

```
cp -r yyTest/ /usr/local
```

* 再次复制文件test.txt到/usr/local目录，强制覆盖

```
cp -f test.txt /usr/local
```

* 复制文件tests.txt到/usr/local目录，并把修改时间和访问权限也复制

```
cp -p test.txt /usr/local
```

### 4.ping命令

* 发送10个数据包

~~~
ping ip -c count count指定数据包数量
~~~

* 追踪路由信息

~~~
traceroute
-4 iPv4
-6 ipv6
~~~

* 查看路由信息

~~~
 netstat -r
和
route -v
~~~

## 浏览文件类命令

### 1.cat命令

用于滚动显示文件内容或者将多个文件合并成一个文件

~~~linux
cat [参数] 文件名
~~~

-b 对输出内容非空行标注行号

-n 对所有行标注行号·

![image-20230410150831015](https://file.heiok.top/note/202405072159886.png)

* 还可以用于合并多个文件夹

### 2.more命令

使用cat命令时，由于文件过长只能看到文件的最后一部分，可以使用more命令分页查看

进入more命令后，**enter**向下移动，使用**空格**翻页

* 参数 

-num：指定分页时每页显示的行数

+num：指定文件从第num行开始显示

~~~
 cat -n docker.sh | more +232
 查看docker.sh文件，从第232行开始分页查看
~~~

### 3.less命令

用法和more基本相同，但是支持向上向下翻页

**b按键**向上移动一页

**q键**退出less命令

还支持查找功能，先按/ ,再输入要查找的字符，会在文本中查找，并且把找到的第一个目标高亮，再次查找按下/再次输入字符

### 4.head命令

### 5.tail命令

## 目录操作类命令

### 1.mkdir命令 

创建一个目录

---make directory

~~~
mkdir [参数] 目录名
~~~

-p 如果创建文件的父目录不存在，创建该目录时，同时创建父级目录

### 2.rmkdir

--remove directory

删除空目录

~~~
rmkdir [参数] 目录名
~~~

-p删除目录时连同父级目录一起删除，必须保证父级目录中没有其他目录及文件

### 3.cp命令

主要用于复制文件或目录的复制

~~~
cp [参数] 源文件 目标文件
~~~

-a 将文件状态，权限等都尽可能全部复制

-f 如果目标文件或者目录存在，则覆盖目标文件

-i 提示是否覆盖文件

-r递归复制目录，包含目录下的子文件

* 在复制时，要保证目标文件的父级目录存在

~~~shell
[root@iZf8z2hzdbx98heub8antpZ ~]# cp -r ./git /home
复制文件到home目录下
[root@iZf8z2hzdbx98heub8antpZ ~]# ls -l /root/git
drwxr-xr-x 2 root root     4096 4月   6 14:45 conf
-rw-r--r-- 1 root root  5713067 4月   6 14:43 linux_amd64_server.tar.gz
-rwxr-xr-x 1 1001  121 12308480 4月   8 2021 nps
drwxr-xr-x 4 root root     4096 4月   6 14:45 web
[root@iZf8z2hzdbx98heub8antpZ ~]# ls -l /home/git
drwxr-xr-x 2 root root     4096 4月  10 16:43 conf
-rw-r--r-- 1 root root  5713067 4月  10 16:43 linux_amd64_server.tar.gz
-rwxr-xr-x 1 root root 12308480 4月  10 16:43 nps
drwxr-xr-x 4 root root     4096 4月  10 16:43 web
~~~

~~~sh
cp -r ./git /home/bor
移动文件到home目录下并更名为bor
~~~

## 文件操作类命令

### 1.mv命令

移动文件或者目录

~~~
mv [参数] 源文件或目录 目标文件或目录
~~~

参数

-i 如果目标文件或者目录存在提示是否覆盖

-f 直接覆盖不提示

### 2.rm

删除文件或者目录

~~~
rm [参数] 文件名或路径名
~~~

参数

* -i 删除文件目录时提示用户
* -f强制删除，不提示
* -r递归删除目录，如果要删除的目录中有1万个子目录或子文件，那么普通的rm删除最少需要确认1万次。所以，在真正删除文件的时候，我们会选择强制删除

rm -fr cptest 组合命令 递归强制删除

~~~sh
[root@iZf8z2hzdbx98heub8antpZ ~]# cp -r ./git /home/cptest
[root@iZf8z2hzdbx98heub8antpZ ~]# cd /home
[root@iZf8z2hzdbx98heub8antpZ home]# ls
cptest  git
[root@iZf8z2hzdbx98heub8antpZ home]# rm -fr cptest
[root@iZf8z2hzdbx98heub8antpZ home]# ls git
~~~

### 3.touch命令

建立文件或者更新文件的修改日期

### 4.ln命令

用于建立两个文件之间的链接关系

-s 建立（符号）软连接

不使用参数默认硬链接

~~~
in [参数]  源文件或目录 连接名
~~~

* 硬链接

~~~
[root@iZf8z2hzdbx98heub8antpZ ~]# ln aa.txt cc.txt
[root@iZf8z2hzdbx98heub8antpZ ~]# ls
aa.txt  bb.txt  cc.txt  git  Pager

~~~

执行命令后会建立一个文件cc.txt文件，修改aa.txt内容cc.txt的内容一会随着改变

~~~sh
[root@iZf8z2hzdbx98heub8antpZ ~]# ll aa.txt
-rw-r--r-- 2 root root 7 4月  11 15:46 aa.txt
[root@iZf8z2hzdbx98heub8antpZ ~]# ll cc.txt
-rw-r--r-- 2 root root 7 4月  11 15:46 cc.txt

~~~

建立链接后，aa，cc的链接数都变成了2，表示这块硬盘空间有aa，bb指向它

**只能对文件建立硬链接，目录不行**

* 软连接

一个文件指向另一个文件的文件名

首先查看bb.txt信息

~~~sh
[root@iZf8z2hzdbx98heub8antpZ ~]# ll bb.txt
-rw-r--r-- 1 root root 7 4月  11 15:42 bb.txt

~~~

~~~shell
[root@iZf8z2hzdbx98heub8antpZ ~]# ln -s bb.txt dd.txt
[root@iZf8z2hzdbx98heub8antpZ ~]# ls
aa.txt  bb.txt  cc.txt  dd.txt  git  Pager
[root@iZf8z2hzdbx98heub8antpZ ~]# ll bb.txt dd.txt
-rw-r--r-- 1 root root 7 4月  11 15:42 bb.txt
lrwxrwxrwx 1 root root 6 4月  11 15:59 dd.txt -> bb.txt
~~~

可以看出dd文件是指向bb文件的一个符号链接，而指向储存bb文件内容的那块银蓝空间的文件仍然只有bb一个文件，dd文件只不过是指向了bb的文件名而已，所以bb文件的文件连接数还是1

* 可以对文件或者目录创建软连接

~~~sh
[root@iZf8z2hzdbx98heub8antpZ ~]# cat dd.txt
bbbbbb
~~~

使用cat命令查看dd.txt文件的内容时，发现dd.txt文件是一个符号链接文件，就可以根据dd文件查找到bb文件

然后将bb文件的内容显示出来

### 5.gzip和gunzip命令

对文件进行压缩，生成以.gz结尾的压缩包，而gunzip是对压缩包进行解压

~~~
gzip -v  文件名
gunzip -v 文件名
~~~

* -v 显示被压缩文件的压缩比或解压时的信息

~~~sh
[root@iZf8z2hzdbx98heub8antpZ test]# gzip -v bb.txt
bb.txt:  14.3% -- replaced with bb.txt.gz
[root@iZf8z2hzdbx98heub8antpZ test]# ls
aa.txt  bb.txt.gz  cc.txt  dd.txt
[root@iZf8z2hzdbx98heub8antpZ test]# gunzip -v bb.txt.gz
bb.txt.gz:       14.3% -- replaced with bb.txt
[root@iZf8z2hzdbx98heub8antpZ test]# ls
aa.txt  bb.txt  cc.txt  dd.txt

~~~

### 6.tar命令

~~~sh
tar [参数] 档案文件 文件列表
~~~

* -c 生成档案文件
* -v 列出解档归档详细过程
* -f 指定档案文件名称
* -r 将文件追加到档案文件末尾
* -z 以gzip格式压缩或者解压缩文件
* -j 以bzip2格式压缩或解压缩文件
* -d 比较档案与当前目录中的文件
* -x 解开档案文件



~~~sh
将文件zz.tar.gz 解压到 /root/test1目录下
[root@iZf8z2hzdbx98heub8antpZ home]# tar -xzvf zz.tar.gz  -C /root/test1

~~~

### 7.rpm命令

用于对rpm包进行管理

~~~
rpm [参数] 软件包名
~~~

* -q 查询 指定软件包是否已安装，-qa（q +all）查看所有的软件包

* -i 用于安装指定的rpm包（安装时需要接上后缀名.rpm）
* -e 删除指定的软件包，（卸载时不用接后缀.rpm）
* -h 显示进度

可以和之前的命令链接

eg

~~~sh
rpm -qa | cat -b | less -20
~~~

查看系统中安装的所有软件包，显示行数，并且分页显示，每页显示20行

### 8.whereis

### 9.find命令

用于文件查找

~~~sh
find [路径] [匹配表达式]
~~~

参数

* -name filename 查找指定名称的文件
* -user username 查找指定用户的文件
* -group grpname 查找指定组的文件
* -print 显示查找结果
*  

## 使用类命令

### 1.nohup

后台运行命令，并且指定日志输出路径

nohup [command] [args...] & >hello.log &

参数说明

command ：需要执行的命令

args:一些参数，可以指定文件输出的位置

&：使命令后台运行

