---
title: "Docker Dockerfile指令大全"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Docker Dockerfile指令大全"
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

### 什么是 Dockerfile？

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。使用`docker build`命令，用户可以创建基于基础镜像的自定义镜像。

### Dockerfile格式

```java
# Comment 
INSTRUCTION arguments  # INSTRUCTION表示指令（不止一条）

```

### Dockerfile指令详解

#### FROM-指定基础镜像

指定基础镜像，并且Dockerfile中第一条指令必须是FROM指令，且在同一个Dockerfile中创建多个镜像时，可以使用多个FROM指令。

语法格式如下：

```java
FROM <image> 
FROM <image>:<tag> 

```

其中`<tag>`是可选项，如果没有选择，那么默认值为`latest`。

如果不以任何镜像为基础，那么写法为：`FROM scratch`。

#### RUN-运行指定的命令

运行指定的命令。

包含两种语法格式，如下所示：

```java
# shell格式：就像在命令行中输入的Shell脚本命令一样。
RUN <command> 

# exec格式：就像是函数调用的格式。
RUN ["executable", "param1", "param2"] 

```

第一种后边直接跟shell命令。

* 在linux操作系统上默认为`/bin/sh -c`
* 在windows操作系统上默认为`cmd /S /C`

第二种是类似于函数调用。可将executable理解成为可执行文件，后面就是两个参数。

样例：

```java
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'

RUN ["/bin/bash", "-c", "echo hello"] 

```

> 注意：
>
> 多行命令不要写多个RUN，原因是Dockerfile中每一个指令都会建立一层。多少个RUN就构建了多少层镜像，会造成镜像的臃肿、多层，不仅仅增加了构件部署的时间，还容易出错。RUN书写时的换行符是`\`

#### CMD-容器启动时要运行的命令

容器启动时要运行的命令。

包含三种语法格式，如下所示：

```java
# 第一种就是shell这种执行方式和写法
CMD command param1 param2

# 第二种是可执行文件加上参数的形式（推荐）
CMD ["executable","param1","param2"] 

# 该写法是为 ENTRYPOINT 指令指定的程序提供默认参数
CMD ["<param1>","<param2>",...]  

```

推荐使用第二种格式，执行过程比较明确。第一种格式实际上在运行的过程中也会自动转换成第二种格式运行，并且默认可执行文件是`sh`。

样例：

```java
CMD [ "sh", "-c", "echo $HOME" ]

CMD [ "echo", "$HOME" ] 

```

> 注意：
>
> 这里边包括参数的一定要用双引号,不能是单引号。千万不能写成单引号。原因是参数传递后，docker解析的是一个JSON array.

#### ENTRYPOINT-为容器指定默认运行程序

类似CMD指令的功能，用于为容器指定默认运行程序，从而使得容器像是一个单独的可执行程序。

在写Dockerfile时, ENTRYPOINT或者CMD指令会自动覆盖之前的ENTRYPOINT或者CMD指令。即ENTRYPOINT或者CMD指令只能写一条，如果写了多条，那么，ENTRYPOINT或者CMD都只有最后一条生效。

在Docker镜像运行时, 用户也可以在命令行指定具体命令, 覆盖在Dockerfile里的命令。

与CMD不同的是，由ENTRYPOINT启动的程序不会被`docker run`命令行指定的参数所覆盖，而且，这些**命令行参数会被当作参数传递给ENTRYPOINT指令指定的程序** ，不过，`docker run`命令的`--entrypoint`选项的参数可覆盖ENTRYPOINT指令指定的程序。

其语法如下：

```java
# exec格式（推荐）
ENTRYPOINT ["executable", "param1", "param2"] 

# shell格式
ENTRYPOINT command param1 param2

```

第一种就是可执行文件加参数。与`CMD`指令一样，`ENTRYPOINT`也更加推荐使用`exec`格式。

第二种就是shell格式。

样例：

```java
FROM ubuntu 
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/* 
ENTRYPOINT ["curl", "-s", "http://ip.cn"]

```

```java
# 将其构建成镜像ubuntu:v1.2，下面我们创建并启动容器：
docker run -it ubuntu:v1.2

# 将会在控制台输出我们相应的公网IP信息！
# 此时，如果我们还需要获取HTTP头信息时，我们可以这样：
docker run -it ubuntu:v1.2 -i

```

##### CMD与ENTRYPOINT的异同点

这俩命令非常像，而且还可以配合使用。

相同点：

* 只能写一条，如果写了多条，那么只有最后一条生效。
* 容器启动时才运行，运行时机相同。

不同点：

* ENTRYPOINT不会被运行的command覆盖，而CMD则会被覆盖。
* 如果我们在Dockerfile中同时写了ENTRYPOINT和CMD，并且CMD指令不是一个完整的可执行命令，那么CMD指定的内容将会作为ENTRYPOINT的参数。如下所示：

```java
FROM ubuntu 
ENTRYPOINT ["rm", "docker2"] 
CMD ["-rf"]

```

它真正执行的命令将会是：`rm docker2 -rf`。

* 如果我们在Dockerfile种同时写了ENTRYPOINT和CMD，并且CMD是一个完整的指令，那么它们两个会互相覆盖，谁在最后谁生效。如下所示：

```java
FROM ubuntu
ENTRYPOINT ["top", "-b"]  
CMD ls -al

```

那么将执行`ls -al`,`top -b`不会执行。

下表显示了针对不同的ENTRYPOINT/CMD进行组合后执行的命令：

|                                  |       No ENTRYPOINT        |   ENTRYPOINT exec_entry p1_entry   |      ENTRYPOINT \["exec_entry", "p1_entry"\]       |
|----------------------------------|----------------------------|------------------------------------|----------------------------------------------------|
| **No CMD**                       | *error, not allowed*       | /bin/sh -c exec_entry p1_entry     | exec_entry p1_entry                                |
| **CMD \["exec_cmd", "p1_cmd"\]** | exec_cmd p1_cmd            | \~/bin/sh -c exec_entry p1_entry\~ | exec_entry p1_entry exec_cmd p1_cmd                |
| **CMD \["p1_cmd", "p2_cmd"\]**   | p1_cmd p2_cmd              | \~/bin/sh -c exec_entry p1_entry\~ | exec_entry p1_entry p1_cmd p2_cmd                  |
| **CMD exec_cmd p1_cmd**          | /bin/sh -c exec_cmd p1_cmd | \~/bin/sh -c exec_entry p1_entry\~ | \~exec_entry p1_entry /bin/sh -c exec_cmd p1_cmd\~ |

虽然这2个指令功能类似，但是这两个指令不是互斥的. 在很多情况下, 你可以组合ENTRYPOINT和CMD指令, 提升最终用户的体验。同时我们从上表也发现**在使用SHELL去执行ENTRYPOINT时，ENTRYPOINT会无视从CMD传来的任何参数，所以CMD只传参数是无效的。**
> 组合ENTRYPOINT和CMD指令的最佳实践：
>
> 组合使用ENTRYPOINT和CMD命令式, 确保你一定用的是exec表示法. 如果有其中一个用的是shell表示法, 或者一个是shell表示法, 另一个是exec表示法, 你永远得不到你预期的效果。只有当ENTRYPOINT和CMD都用exec表示法, 才能得到预期的效果。

#### COPY-复制文件或者目录到容器里指定路径

从上下文目录中复制文件或者目录到容器里指定路径。

语法格式如下：

```java
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]

```

参数说明：

* **\[--chown=:\]** ：可选参数，用户改变复制到容器内文件的拥有者和属组。
* **\<源路径\>** ：源文件或者源目录，这里可以是通配符表达式，其通配符规则要满足 Go 的 filepath.Match 规则。例如：`COPY hom* /mydir/`，`COPY hom?.txt /mydir/`。源路径必须是build上下文中的路径，不能是其父目录中的文件。如果源路径是目录，则其内部文件或子目录会被递归复制，但**源路径目录自身不会被复制** 。如果指定了多个源路径，或在源路径中使用了通配符，则目标路径必须是一个目录，且必须以`/`结尾。
* **\<目标路径\>** ：容器内的指定路径，建议为目标路径使用绝对路径，否则，COPY指令则以WORKDIR为其起始路径；**如果目标路径事先不存在，它将会被自动创建**，这包括其父目录路径。

样例：

```java
# 复制宿主机文件index.html到容器/data/html/index.html
COPY index.html /data/html/index.html   

# 复制宿主机data目录下文件（包括子目录）到容器/data/目录下，并不会复制目录本身
COPY data  /data/    

```

#### ADD-复制文件或者目录到容器里指定路径

ADD 指令和 COPY 的使用类似 **（同样需求下，官方推荐使用 COPY）** ，功能也类似。除此之外，ADD还支持使用TAR文件和URL路径，并且会将tar压缩文件（gzip, bzip2以及 xz格式）解压缩，如果指定的是url，会从指定的url下载文件放到目录中（ 如果url下载的文件为tar文件，则不会展开）。

其语法格式如下：

```java
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]

```

样例：

```java
ADD /data/src/nginx-1.14.0.tar.gz /data/src/

```

```java
# 构建镜像
docker build -t nginx:v1.1  .

# 创建容器
docker run --rm --name nginx -it  nginx:v1.1  ls /data/src

# 我们可以发现已经解压了nginx-1.14.0.tar.gz文件

```

#### LABEL-为镜像指定标签

为镜像指定标签，其格式为：

```java
LABEL <key1>=<value1> <key2>=<value2> ...

```

`LABEL`后面是键值对，多个键值对以空格进行隔开，如果value中包含空格，请使用`""`将value进行圈起来。

```java
# 如果太长需要换行的话，则使用\符号
LABEL name=test \
  description="a container is used to test"

```

我们可以使用`docker inspect`命令，来查看镜像的标签：

```java
docker inspect --format '{{json .Config.Labels}}' test | python3 -m json.tool

```

其中，"test"为容器名称，"python3 -m json.tool"为将其格式化为JSON输出。

#### MAINTAINER-指定生成镜像的作者名称

用于指定生成镜像的作者名称，其格式为：

```java
MAINTAINER <name>

```

`MAINTAINER`指令已经被弃用，可以使用`LABEL`指令进行替代，样例如下：

```java
LABEL maintainer='Stephen Chow'

```

> 说明：
>
> LABEL会继承基础镜像中的LABEL，如遇到key相同，则值覆盖。

#### EXPOSE-为容器打开指定要监听的端口以实现与外部通信

用于为容器打开指定要监听的端口以实现与外部通信，这个只是声明，真正要暴露这个端口需要再构建容器的时候使用"-P"选项。其格式为：

```java
EXPOSE <port> [<port>/<protocol>...]

```

示例：

```java
EXPOSE 80/tcp
EXPOSE 80/udp

```

如果想使得容器与主机的端口有映射关系，需要在启动容器时指定`-P`.

```java
# 这里的"-P"选项是关键，在启动容器的使用使用-P，
# Docker会自动分配一个端口和转发指定的端口，
# 使用-p可以具体指定使用哪个本地的端口来映射对外开放的端口。
docker run --rm --name nginx -itd -P nginx:v1.4

```

#### ENV-设置环境变量

设置环境变量，无论是接下来的指令（如`ENV`、`ADD`、`COPY`等，其调用格式为`$variable_name`或`${variable_name}`），还是在容器中运行的程序，都可以使用这里定义的环境变量。

它有两种语法格式，如下所示：

```java
ENV <key> <value> 

ENV <key1>=<value1> <key2>=<value2> ... 

```

两者的区别就是第一种是一次设置一个，第二种是一次设置多个。

示例：

```java
ENV word hello
ENV guodong xxxx    
ENV name1=ping name2=on_ip

RUN echo $word

```

> 注意：
>
> 如果你想通过CMD或者ENTRYPOINT指令的exec格式来打印环境，就像下面这样：
>
> `CMD ["echo", $MODE]`
>
> `CMD ["echo", "$MODE"]`
>
> 这样都是不能正确输出环境变量的值的，你可以改成`exec格式`来执行`shell命令`，如下所示：
>
> `CMD ["sh", "-c", "echo $MODE"]`
>
> 如此，就能正确输出环境变量的值了！

#### {#ARG_366}ARG-设置环境变量

构建参数`ARG`和`ENV`指令一样，都是设置环境变量。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 `docker build` 的过程中有效，构建好的镜像内不存在此环境变量。即在将来容器运行时是不会存在这些环境变量的。但是不要因此就用`ARG`来保存密码之类的信息，因为通过`docker history`还是能够看得到的。

ARG构建命令在`docker build` 中可以用 `--build-arg <参数名>=<值>` 来覆盖。

语法格式如下：

```java
ARG <参数名>[=<默认值>]

```

示例：

```java
FROM ubuntu:16.04
ARG app="python-pip"
RUN apt-get update && apt-get install -y $app && rm -rf /var/lib/apt/lists/*

# 我们可以定义多个参数
FROM busybox
ARG user1
ARG buildno

# 也可以给参数一个默认值
FROM busybox
ARG user1=someuser
ARG buildno=1

# 如果我们给ARG定义了参数默认值，那么当build镜像时，如果没有指定参数值，将会使用这个默认值。

```

#### WORKDIR-指定工作目录

用于为Dockerfile中所有的RUN、CMD、ENTRYPOINT、COPY和ADD指令设定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。（WORKDIR 指定的工作目录，必须是提前创建好的）。

`docker build` 构建镜像过程中的，每一个 RUN 命令都是新建的一层。只有通过 WORKDIR 创建的目录才会一直存在。

在Dockerfile文件中，WORKDIR指令可出现多次，其路径也可以为相对路径，不过，它的路径是相对此前一个WORKDIR指令指定的路径。另外，WORKDIR也可调用由ENV指定定义的变量。

格式：

```java
WORKDIR <工作目录路径>

```

样例：

```java
WORKDIR /var/log 

# 解析环境变量
ENV DIRPATH /path
WORKDIR $DIRPATH

# 也可以设置多次
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
# pwd执行的结果是/a/b/c

```

#### VOLUME-定义匿名数据卷

定义匿名数据卷，可实现挂载功能，可以将内地文件夹或者其他容器中得文件夹挂在到这个容器中。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。

作用：

* 避免重要的数据，因容器重启而丢失，这是非常致命的。（容器使用的是AUFS，这种文件系统不能持久化数据，当容器关闭后，所有的更改都会丢失。）
* 避免容器不断变大。

语法格式如下：

```java
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>

```

在启动容器 docker run 的时候，我们可以通过 -v 参数修改挂载点。

样例：

```java
# 定义一个匿名卷
FROM ubuntu:16.04
VOLUME /data

# 定义多个匿名卷
FROM ubuntu:16.04
VOLUME ["/data", "/command"]

```

这里的`/data`和`/command`目录在容器运行时会自动挂载为匿名卷，任何向`/data`和`/command`目录中写入的信息都不会记录进容器存储层，从而保证了容器存储层的无状态化！容器匿名卷目录指定可以通过`docker run`命令中指定`-v`参数来进行覆盖。

#### USER-指定执行后续命令的用户和用户组

用于指定执行后续命令的用户和用户组，这里只是切换后续命令执行的用户（用户和用户组必须提前已经存在）。在USER命令之前可以使用RUN命令创建需要的用户。

默认情况下，容器的运行身份为root用户。

语法格式如下：

```java
# 可以指定用户名或者UID，组名或者GID
USER <user>[:<group>]
USER <UID>[:<GID>]

```

`USER`指令还可以在`docker run`命令中使用`-u`参数进行覆盖。

样例：

```java
RUN groupadd -r docker && useradd -r -g docker docker

USER docker

```

#### ONBUILD-用于延迟构建命令的执行

用于延迟构建命令的执行。简单的说，就是 Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行（假设镜像为 test-build）。当有新的 Dockerfile 使用了之前构建的镜像 `FROM test-build` ，这时执行新镜像的 Dockerfile 构建时候，会执行 test-build 的 Dockerfile 里的 ONBUILD 指定的命令。

`ONBUILD`是一个特殊的指令，它后面跟着的是其他指令，比如`COPY`、`RUN`等，而这些命令在当前镜像被构建时，并不会被执行。只有以当前镜像为基础镜像去构建下一级镜像时，才会被执行。

`Dockerfile`中的其他指令都是为了构建当前镜像准备的，只有`ONBUILD`指令是为了帮助别人定制而准备的。

语法格式如下：

```java
ONBUILD <其他指令>

```

样例：

```java
from ubuntu:16.04
WORKDIR /data
ONBUILD RUN mkdir test

```

#### STOPSIGNAL

设置**将发送到容器以退出** 的系统调用信号。这个信号可以是一个有效的无符号数字，与内核的`syscall`表中的位置相匹配，如`9`，或者是`SIGNAME`格式的信号名，如`SIGKILL`。

语法格式如下：

```java
STOPSIGNAL signal

```

默认的停止信号是`SIGTERM`，在`docker stop`的时候会给容器内PID为1的进程发送这个signal，通过`--stop-signal`可以设置自己需要的signal，主要的目的是为了让容器内的应用程序在接收到signal之后可以先做一些事情，实现容器的平滑退出。如果不做任何处理，容器将在一段时间之后强制退出，会造成业务的强制中断，这个时间默认是10s。

#### HEALTHCHECK-容器健康状况检查

用于指定某个程序或者指令来监控 docker 容器服务的运行状态是否正常。

语法格式如下：

```java
# 设置检查容器健康状况的命令
HEALTHCHECK [OPTIONS] CMD <命令>

# 如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令
HEALTHCHECK NONE


```

OPTIONS参数说明：

* **--interval=\<间隔\>** ：两次检查的时间间隔，默认为30s
* **--timeout=\<时长\>** ：健康检查命令运行超时时间，如果超过这个时间，本次健康检查将会判定为失败，默认为30s
* **--retries=\<次数\>** ：当连续失败指定次数之后，则将容器状态视为`unhealthy`，默认为3次。
* **--start-period=DURATION** ：启动时间，默认为0s， 如果指定这个参数， 则必须大于 0s ；`--start-period` 为需要启动的容器提供了初始化的时间段， 在这个时间段内如果检查失败， 则不会记录失败次数。 如果在启动时间内成功执行了健康检查， 则容器将被视为已经启动， 如果在启动时间内**再次**出现检查失败， 则会记录失败次数。

在 `CMD` 关键字之后的 `command` 可以是一个 shell 命令（例如： `HEALTHCHECK CMD /bin/check-running`）或者一个 exec 数组（与其它 Dockerfile 命令相同， 参考 \[ENTRYPOINT\]）。

该命令的返回值说明了容器的状态， 可能是值为：

* 0: healthy - 容器健康，可以使用；
* 1: unhealthy - 容器工作不正常，需要诊断；
* 2: reserved - 保留，不要使用这个返回值；

当容器的健康状态发生变化时，会生成一个带有新状态的 `health_status` 事件。

样例：

```java
HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://localhost/ || exit 1

```

> 注意：
>
> 和`CMD`、`NETRYPOINT`一样，`HEALTHCHECK`指令只可以出现一次，如果有多个`HEALTHCHECK`指令，那么只有最后一个才会生效！

#### SHELL-重写xxx指令的shell格式所使用的默认shell

`SHELL`指令允许重写xxx指令的shell格式所使用的默认shell。关于默认的`shell`，`Linux`是`["/bin/sh", "-c"]`，`Windows`是`["cmd", "/S", "/C"]`。

`SHELL` 指令必须在`dockerfile`中以`JSON`格式编写。

`SHELL` 指令在 Windows 上特别有用，Windows 有两种常用且截然不同的本机 shell：`cmd` 和 `powershell`，以及可用的备用 shell，包括 `sh`。

`SHELL` 指令可以出现多次。 每条 SHELL 指令都会覆盖所有先前的 `SHELL` 指令，并影响所有后续指令。

样例：

```java
FROM microsoft/windowsservercore

# Executed as cmd /S /C echo default
RUN echo default

# Executed as cmd /S /C powershell -command Write-Host default
RUN powershell -command Write-Host default

# Executed as powershell -command Write-Host hello
SHELL ["powershell", "-command"]
RUN Write-Host hello

# Executed as cmd /S /C echo hello
SHELL ["cmd", "/S", "/C"]
RUN echo hello

```
