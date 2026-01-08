---
title: "CSharp面向对象的编程"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "CSharp面向对象的编程"
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

## 面向对象的编程（继承（以及封装和多态性）是面向对象的编程的三个主要特征）

### C# 中的类、结构和记录概述

#### 封装

*封装*有时称为面向对象的编程的第一支柱或原则。 类或结构可以指定自己的每个成员对外部代码的可访问性。 可以隐藏不得在类或程序集外部使用的方法和变量，以限制编码错误或恶意攻击发生的可能性。 有关详细信息，请参阅[面向对象的编程](https://learn.microsoft.com/zh-cn/dotnet/csharp/fundamentals/tutorials/oop)教程。

#### 成员

类型的成员包括所有方法、字段、常量、属性和事件。 C# 没有全局变量或方法，这一点其他某些语言不同。 即使是编程的入口点（`Main` 方法），也必须在类或结构中声明（使用[顶级语句](https://learn.microsoft.com/zh-cn/dotnet/csharp/fundamentals/program-structure/top-level-statements)时，隐式声明）。

下面列出了所有可以在类、结构或记录中声明的各种成员。

- 字段
- 常量
- 属性
- 方法
- 构造函数
- 事件
- 终结器
- 索引器
- 运算符
- 嵌套类型

#### 可访问性 [可访问性级别](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/accessibility-levels)

一些方法和属性可供类或结构外部的代码（称为“*客户端代码*”）调用或访问。 另一些方法和属性只能在类或结构本身中使用。 请务必限制代码的可访问性，仅供预期的客户端代码进行访问。 需要使用以下访问修饰符指定类型及其成员对客户端代码的可访问性：

- [public](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/public)

 公共访问是允许的最高访问级别。 对访问公共成员没有限制

- [受保护](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/protected)

只有在通过派生类类型进行访问时，基类的受保护成员在派生类中才是可访问的

- [internal](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/internal)

只有在同一[程序集](https://learn.microsoft.com/zh-cn/dotnet/standard/assembly/)的文件中，才能访问内部类型或成员

- [protected internal](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/protected-internal)
- [private](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/private)
- [专用受保护](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/private-protected)。

可访问性的默认值为 `private`。

#### 扩展方法

>扩展方法使你能够向现有类型“添加”方法，而无需创建新的派生类型、重新编译或以其他方式修改原始类型。 扩展方法是一种静态方法，但可以像扩展类型上的实例方法一样进行调用

扩展方法被定义为静态方法，但它们是通过实例方法语法进行调用的。 它们的第一个参数指定方法操作的类型。 该参数位于 [this](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/this) 修饰符之后。 仅当你使用 `using` 指令将命名空间显式导入到源代码中之后，扩展方法才位于范围中。

下面的示例演示为 [System.String](https://learn.microsoft.com/zh-cn/dotnet/api/system.string) 类定义的一个扩展方法。 它是在非嵌套的、非泛型静态类内部定义的：

~~~c#
namespace ExtensionMethods
{
    public static class MyExtensions
    {
        public static int WordCount(this string str)
        {
            return str.Split(new char[] { ' ', '.', '?' },
                             StringSplitOptions.RemoveEmptyEntries).Length;
        }
    }
}
~~~

### 对象 
创建类型的实例

### 继承

* 唯一性，一个类只能有一个基类
* 继承时可传递的，a继承自b，b继承自c，a将继承c中声明的成员
* 注 ：结构不支持继承，但它们可以实现接口。

### 抽象方法和虚方法

基类将方法声明为 [`virtual`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/virtual) 时，派生类可以使用其自己的实现[`override`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/override)该方法。 如果基类将成员声明为 [`abstract`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/abstract)，则必须在直接继承自该类的任何非抽象类中重写该方法。 如果派生类本身是抽象的，则它会继承抽象成员而不会实现它们。 抽象和虚拟成员是多形性（面向对象的编程的第二个主要特征）的基础。 有关详细信息，请参阅[多态性](https://learn.microsoft.com/zh-cn/dotnet/csharp/fundamentals/object-oriented/polymorphism)。

### 抽象基类

如果要通过使用 [new](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/new-operator) 运算符来防止直接实例化，则可以将类声明为[抽象](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/abstract)。 只有当一个新类派生自该类时，才能使用抽象类。 抽象类可以包含一个或多个本身声明为抽象的方法签名。 这些签名指定参数和返回值，但没有任何实现（方法体）。 抽象类不必包含抽象成员；但是，如果类包含抽象成员，则类本身必须声明为抽象。 本身不抽象的派生类必须为来自抽象基类的任何抽象方法提供实现。

