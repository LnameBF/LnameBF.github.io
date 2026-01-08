---
title: "Csharp中的类型系统"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Csharp中的类型系统"
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

## 类型系统

C# 是一种强类型语言。 每个变量和常量都有一个类型，每个求值的表达式也是如此。 每个方法声明都为每个输入参数和返回值指定名称、类型和种类（值、引用或输出）

### 主要类别

值类型和引用类型是 C# 类型的两个主要类别。 值类型的变量包含类型的实例。 它不同于引用类型的变量，后者包含对类型实例的引用。

#### 1.值类型

所谓值类型就是包含一个实际数据的量。当定义一个值类型的变量是，C#会根据他所声明的类型，以堆栈方式分配一块大小相适应的存储区域给这个变量，随后对这个变量的读写操作就直接在这块内存区域进行。

C#中的值类型包括：简单类型、枚举类型和结构类型。废话不多说，一张表胜过千言万语。

![image-20240721001817459](https://file.heiok.top/note/202407210018513.png)

#### 2.引用类型

一个引用类型的变量不存储它们所代表的实际数据，而是存储实际数据的引用。引用类型包括类、接口、数组、object、string ，委托。直接上图。

注：object是C#中所有类型的根类，string类型是一个从object类直接继承的密封类型。

![file_1721492311442_435](https://file.heiok.top/note/202407210018844.png)

## 描述

### 类

#### 创建对象

虽然它们有时可以互换使用，但类和对象是不同的概念。 类定义对象类型，但不是对象本身。 对象是基于类的具体实体，有时称为类的实例。

可通过使用 `new` 关键字，后跟类的名称来创建对象，如下所示：

```csharp
Customer object1 = new Customer();
```

创建类的实例后，会将一个该对象的引用传递回程序员。 在上一示例中，`object1` 是对基于 `Customer` 的对象的引用。 该引用指向新对象，但不包含对象数据本身。 事实上，可以创建对象引用，而完全无需创建对象本身

#### 构造函数和初始化

 创建类型的实例时，需要确保其字段和属性已初始化为有用的值。 可通过多种方式初始化值：

- 接受默认值
- 字段初始化表达式
- 构造函数参数
- 对象初始值设定项

#### 类继承

类完全支持继承，这是面向对象的编程的基本特点。 创建类时，可以从其他任何未定义为 [`sealed`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/sealed) （`sealed` 修饰符可阻止其他类继承自该类）的类继承。 其他类可以从你的类继承并替代类虚拟方法。 此外，你可以实现一个或多个接口。

~~~c#
public class Manager : Employee
{
// Employee fields, properties, methods and events are inherited
// New Manager fields, properties, methods and events go here...
}
~~~

类声明包括基类时，它会继承基类除构造函数外的所有成员

C# 中的类只能直接从基类继承。 但是，因为基类本身可能继承自其他类，因此类可能间接继承多个基类。 此外，类可以支持实现一个或多个接口。 有关详细信息，请参阅[接口](https://learn.microsoft.com/zh-cn/dotnet/csharp/fundamentals/types/interfaces)。

类可以声明为 [`abstract`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/abstract)。 抽象类包含抽象方法，抽象方法包含签名定义但不包含实现。 抽象类不能实例化。 只能通过可实现抽象方法的派生类来使用该类。 与此相反，[密封](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/sealed)类不允许其他类继承。 有关详细信息，请参阅[抽象类、密封类和类成员](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members)。

### 记录

### 接口

### 泛型类和方法


