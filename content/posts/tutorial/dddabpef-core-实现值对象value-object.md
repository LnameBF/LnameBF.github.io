---
title: "DDD/ABP/EF Core 实现值对象Value Object"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "DDD/ABP/EF Core 实现值对象Value Object"
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

有一说一DDD真tM抽象:看一遍真是看不懂。

### 简单理解值对象和实体的区别：

1. 实体具有**唯一标识符**，就是主键

2. 值对象**无法单独存在**，一定是依附于一个实体

举个栗子, 地址的三个属性必须同时存在，缺少一个地址属性就没意义了。

```cs
class girlfriend
{
    string height;
    string weight;
    string location;
}

class location
{
    string country;
    string city;
    string street;    
}
```

**那么深入一些：值对象存在的意义 是 把领域知识 放到类的定义中。**

### 实现方式有2种

> <br />
>
> **1. "从属实体类型（owned entities）"：使用Fluent API中的OwnsOne等方法来配置。**
>
> **2、在EF Core中，实体的属性可以定义为枚举类型，枚举类型的属性在数据库中默认是以整数类型来保存的。对于直接操作数据库的人员来讲，0、1、2这样的值没有"CNY"（人民币）、"USD"（美元）、"NZD"（新西兰元）等这样的字符串类型值可读性更强。****EF Core中可以在Fluent API中用HasConversion\<string\>()把枚举类型的值配置成保存为字符串****。（看图二）**

#### 方式2

#### ![](https://i-blog.csdnimg.cn/blog_migrate/d270352cc48d500ff71359883ca125df.png)

![](https://i-blog.csdnimg.cn/blog_migrate/cf0fa7cc46b0ed719acf9cc536deb47b.png)

建议：值对象 定义为 record类型
> ​class ​是主要常用类型，属于引用类型，可以继承，可以多态，还可以定义抽象类。我们用的最多的。在进行两个类对象==操作时，默认比较的是对象的引用地址是否相等。
>
> <br />
>
> ​struct ​是很早C语言就开始使用的数据结构，一般用于简单的数据，不能继承和多态，属于值类型，比较时，默认是进行类型相同并且里面的字段值相等。可以对每个成员变量进行比特位的控制，用于与C程序库中的数据类型进行互相通信时会使用，比如PInvoke时候就常用。
>
> <br />
>
> **​​record ​​新增的类型，默认==操作是进行类型判断和成员值全部相等，也属于值类型。默认初始化之后，其成员数据是无法修改的。可以使用with快速方便的在复制一个副本时修改其中某些成员的值。但是其实现的复制是潜复制。**   

#### 方式1

栗子：将坐标（经纬度）定义为值对象，设置构造函数，好处是在构造中就可以进行校验

### **这就是领域知识封装到一个类里面 ！因为其他人是不知道经纬度的合法数值的！**

```cs
record Geo{
	public double Longitude { get; init; }
	public double Latitude { get; init; }
	public Geo(double longitude, double latitude)
	{
		if(longitude<-180||longitude>180)
			throw new ArgumentException("longitude invalid");
		if (latitude < -90 || latitude > 90)
			throw new ArgumentException("longitude invalid");
		this.Longitude = longitude;
		this.Latitude = latitude;
	}
```

![](https://i-blog.csdnimg.cn/blog_migrate/030bc27c1c636ef64333dc94faf3dab5.png)

注意，Location是一个从属实体属性 ，所以需要配置

![](https://i-blog.csdnimg.cn/blog_migrate/bbb83c1e13c47d0e4c08f2b7e28bc7a9.png)

看，配置完之后，数据库存储时候会将Location**展开为2个列**，并且附加到shop表内。

![](https://i-blog.csdnimg.cn/blog_migrate/46bd143a060ff8a274dee883b2686a69.png)

再次复习一下为什么两个 构造器 一个private 一个public！![](https://i-blog.csdnimg.cn/blog_migrate/c833871b8e4b86143966a4e27488b04c.png)

<br />


