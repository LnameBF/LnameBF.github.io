---
title: "EfCore实现充血模型"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "EfCore实现充血模型"
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

充血模型的EFCore实现
充血模型的五个需求
1. 属性是只读的或者只能被类内部的代码修改。

~~~c#
 public PhoneNumber PhoneNumber { get; private set;}
 public void ChangePassword(string password)
 {
     if (password.Length <= 3)
     {
         throw new ArgumentOutOfRangeException("密码长度需要大于3");
     }
     this.passwordHash = HashHelper.Hash_2_MD5_16(password, false);

 }
~~~
   实现

     属性是只读的或者是只能被类内部的代码修改。

     实现:把属性的set定义为private或者init,然后通过构造方法为这些属性赋予初始值。

2. 定义有参数的构造方法。
~~~c#

  public Guid Id { get; init; }
  public PhoneNumber PhoneNumber { get; private set; }
 //构造函数，在一开始创建对象时就被初始化
 public User(PhoneNumber phoneNumber)
 {
     this.Id = Guid.NewGuid();
     this.PhoneNumber = phoneNumber;
     this.UserAccessFail = new UserAccessFail(this);
 }
~~~
   实现

    定义有参数的构造方法。
    原理:EF Core中的实体类如果没有无参的构造方法,则有参的构造方法中的参数的名字必须和属性的 
    名字一致。
    实现方式1:无参构造方法定义为private。
    实现方式2:实体类中不定义无参构造方法,只定义有意义的有参构造方法,但是要求构造方法中的参数 
    的名字和属性的名字一致。

3. 有的成员变量没有对应的属性，但是这些成员变量需要映射为数据表中的列，也就是我们需要把私有的成员变量映射到数据表中的列。 

~~~c#
private string? passwordHash;
//这个成员变量不需要为外部得知，但是需要保存到数据库中
实现
~~~
    不属于属性的成员变量映射为数据列。
    实现:
~~~c#
builder.Property("成员变量名”)
~~~
4. 有的属性是只读的，也就是它的值是从数据库中读取出来的，但是我们不能修改属性的值。
~~~c#
private string ID;
//这个属性只能从数据库读出来，不能在数据库以外进行更改。
//内部计算列
~~~
    实现

    从数据列中读取值的只读属性。
    EF Core中提供了“支持字段”(backing field)来支持这种写法:在配置实体类的代码中,使用 
    HasField(“成员变量名”)来配置属性。

5. 有的属性不需要映射到数据列，仅在运行时被使用。

  内部使用的属性，不在数据库中生成对应的列
​  实现
​      有的属性不需要映射到数据列,仅在运行时被使用。
​      实现:使用lgnore()来配置忽略这个属性。

前三种比较常见，后两种并不常见。
