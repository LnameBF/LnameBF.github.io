---
title: "Jenkins自动部署教程"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Jenkins自动部署教程"
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

##后端Dockerfile
~~~
# 使用 ASP.NET 运行时镜像（根据项目版本调整）
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app

# 设置环境变量（可选）
#ENV ASPNETCORE_ENVIRONMENT=Production
ENV ASPNETCORE_ENVIRONMENT=Development
ENV ASPNETCORE_URLS=http://+:5001

# 暴露端口
EXPOSE 5001

# 复制已编译的文件
COPY bin/Release/net6.0/ /app/

# 设置入口点
ENTRYPOINT ["dotnet", "Myblog.WebApi.dll"]
~~~
## shell
~~~shell
#!/bin/bash
# 将dockerfile复制过来
cd /var/jenkins_home/workspace/MyBlog/Myblog.WebApi/
#cd /tools/1panel/1panel/apps/jenkins/jenkins/data/workspace/MyBlog/Myblog.WebApi/bin/Release/net6.0/
#cp /tools/1panel/1panel/apps/jenkins/jenkins/data/workspace/Dotnet/Dockerfile ./Dockerfile
cp /var/jenkins_home/workspace/Dotnet/Dockerfile ./Dockerfile
# 指定镜像名和容器名
IMAGE_NAME="myblogimg"
CONTAINER_NAME="myblog"

# 检查并删除已存在的容器
if [ "$(docker ps -aq -f name=$CONTAINER_NAME)" ]; then
    echo "删除已存在的容器: $CONTAINER_NAME"
    docker rm -f $CONTAINER_NAME
else
    echo "容器 $CONTAINER_NAME 不存在"
fi

# 检查并删除已存在的镜像
if [ "$(docker images -q $IMAGE_NAME)" ]; then
    echo "删除已存在的镜像: $IMAGE_NAME"
    docker rmi -f $IMAGE_NAME
else
    echo "镜像 $IMAGE_NAME 不存在"
fi
pwd
# 构建新的 Docker 镜像
echo "构建新的 Docker 镜像: $IMAGE_NAME"
docker build -t $IMAGE_NAME .

# 创建并运行新的 Docker 容器
echo "创建并运行容器: $CONTAINER_NAME"
#docker run -d --name $CONTAINER_NAME --restart=on-failure:5 -p 5001:5001 $IMAGE_NAME
docker run -d \
  --name $CONTAINER_NAME \
  --restart=on-failure:5 \
  -p 5001:5001 \
  -v /tools/myblog/logs/:/app/logs/ \
  $IMAGE_NAME
~~~

## 前端-后台Dockerfile
~~~
FROM nginx:latest
EXPOSE 80
WORKDIR /app
# 替换nginx配置
COPY nginx.conf /etc/nginx/conf.d/default.conf
# 将第一阶段的静态文件复制到nginx中
RUN rm -rf /usr/share/nginx/html
RUN mkdir /usr/share/nginx/html
COPY dist /usr/share/nginx/html

# 运行
CMD ["nginx", "-g", "daemon off;"]
~~~
## nginx.conf
~~~
server {
  listen       80;
  listen  [::]:80;
  server_name  localhost;

  access_log  /var/log/nginx/host.access.log  main;

  location / {
      root   /usr/share/nginx/html;
      index  index.html index.htm;

      # 新增下面这句，其他是默认nginx配置
      # 解决部分前端框架的路由问题，在浏览器刷新报错404
      try_files $uri $uri/ /index.html;
  }

  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
      root   /usr/share/nginx/html;
  }
}
~~~
## shell
~~~
#!/bin/bash
# 将dockerfile复制过来
cd /var/jenkins_home/workspace/BlogWeb-admin-test/
cp /var/jenkins_home/workspace/Dotnet/front_Dockerfile ./Dockerfile
cp /var/jenkins_home/workspace/Dotnet/nginx.conf ./nginx.conf
cp /var/jenkins_home/workspace/Dotnet/front_admin/dist.zip ./dist.zip
unzip -d ./dist dist.zip
# 指定镜像名和容器名
IMAGE_NAME="myblog_web_admin_img"
CONTAINER_NAME="myblog_web_admin"

# 检查并删除已存在的容器
if [ "$(docker ps -aq -f name=$CONTAINER_NAME)" ]; then
    echo "删除已存在的容器: $CONTAINER_NAME"
    docker rm -f $CONTAINER_NAME
else
    echo "容器 $CONTAINER_NAME 不存在"
fi

# 检查并删除已存在的镜像
if [ "$(docker images -q $IMAGE_NAME)" ]; then
    echo "删除已存在的镜像: $IMAGE_NAME"
    docker rmi -f $IMAGE_NAME
else
    echo "镜像 $IMAGE_NAME 不存在"
fi
pwd
# 构建新的 Docker 镜像
echo "构建新的 Docker 镜像: $IMAGE_NAME"
docker build -t $IMAGE_NAME .

# 创建并运行新的 Docker 容器
echo "创建并运行容器: $CONTAINER_NAME"
docker run -d --name $CONTAINER_NAME --restart=on-failure:5 -p 3000:80 $IMAGE_NAME
echo "构建成功，删除文件"
rm -rf ./dist
rm ./dist.zip


~~~

## 前端-前台Dockerfile和后台一致
shell
~~~
#!/bin/bash
# 将dockerfile复制过来
cd /var/jenkins_home/workspace/BlogWeb-test/
cp /var/jenkins_home/workspace/Dotnet/front_Dockerfile ./Dockerfile
cp /var/jenkins_home/workspace/Dotnet/nginx.conf ./nginx.conf
cp /var/jenkins_home/workspace/Dotnet/front_web/dist.zip ./dist.zip
unzip -d ./dist dist.zip
# 指定镜像名和容器名
IMAGE_NAME="myblog_web_img"
CONTAINER_NAME="myblog_web"

# 检查并删除已存在的容器
if [ "$(docker ps -aq -f name=$CONTAINER_NAME)" ]; then
    echo "删除已存在的容器: $CONTAINER_NAME"
    docker rm -f $CONTAINER_NAME
else
    echo "容器 $CONTAINER_NAME 不存在"
fi

# 检查并删除已存在的镜像
if [ "$(docker images -q $IMAGE_NAME)" ]; then
    echo "删除已存在的镜像: $IMAGE_NAME"
    docker rmi -f $IMAGE_NAME
else
    echo "镜像 $IMAGE_NAME 不存在"
fi
pwd
# 构建新的 Docker 镜像
echo "构建新的 Docker 镜像: $IMAGE_NAME"
docker build -t $IMAGE_NAME .

# 创建并运行新的 Docker 容器
echo "创建并运行容器: $CONTAINER_NAME"
docker run -d --name $CONTAINER_NAME --restart=on-failure:5 -p 3001:80 $IMAGE_NAME
~~~

