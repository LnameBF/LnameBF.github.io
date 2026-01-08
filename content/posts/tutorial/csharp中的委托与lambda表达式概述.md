---
title: "CSharp中的委托与lambda表达式概述"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "CSharp中的委托与lambda表达式概述"
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

## 委托和lambda

委托定义了一种**引用类型**，表示对具有特定参数列表和返回类型的方法的引用。 其参数列表和返回类型匹配的方法（静态或实例）分配给该类型的变量，然后（使用适当参数）直接调用该方法，或将其作为参数本身传递给另一方法再进行调用。 以下示例演示了委托的用法。

~~~c#
using System;
using System.Linq;

public class Program
{
    public delegate string Reverse(string s);

    static string ReverseString(string s)
    {
        return new string(s.Reverse().ToArray());
    }

    static void Main(string[] args)
    {
        Reverse rev = ReverseString;

        Console.WriteLine(rev("a string"));
    }
}
~~~

- `public delegate string Reverse(string s);` 行创建某种方法的委托类型，即接受字符串参数并返回字符串参数的方法。
- `static string ReverseString(string s)` 方法与定义的委托类型具有完全相同的参数和返回类型，用于实现委托。
- `Reverse rev = ReverseString;` 行显示可将方法分配给相应委托类型的变量。
- `Console.WriteLine(rev("a string"));` 行演示如何使用委托类型的变量来调用委托。

为简化开发过程，.NET 包含一组委托类型，程序员可重用这些类型而无需创建新类型。 这些类型是 `Func<>`、`Action<>` 和 `Predicate<>`，可以使用它们而无需定义新的委托类型。 这三种类型之间有一些区别，与它们的预期使用方式有关：

- `Action<>` 用于需要使用委托参数执行操作的情况。 它所封装的方法**不返回值**。（类似与java中的消费者函数）
- `Func<>` 通常用于现有转换的情况，也就是说需要将委托参数转换为其他结果时。 投影是一个很好的示例。 它所封装的方法**返回指定值**。  （类似与java中的function）
- `Predicate<>` 用于需要确定参数是否满足委托条件的情况。 它也可以编写为 `Func<T, bool>`，这意味着方法返回布尔值。（类似于断定型接口）

~~~c#
 [Test]
    public void TMain2()
    {
        List<int> dataList = new List<int>();
        for (int i = 0; i < 101; i++)
        {
            dataList.Add(i);
        }
        // 使用匿名委托筛选出3的倍数
        List<int> ints = dataList.FindAll(delegate(int no)
        {
            return no % 3 == 0;
        });
        foreach (int item in ints)
        {
            Console.WriteLine(item);
        }

        Console.WriteLine("使用lambda");
        // 使用lambda
        List<int> all = dataList.FindAll(x=> x%3 ==0);
        foreach (int item in all)
        {
            Console.WriteLine(item);
        }
    }
~~~

如你所见，该委托的正文只是一组表达式，其他所有委托也是如此。 但它并非单独定义，而是在调用 [List.FindAll](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.list-1.findall) 方法时临时引入。

但是，即使使用此方法，仍有许多可以丢弃的代码。 此时就需要使用 *lambda 表达式*。 lambda 表达式（或简称“lambda”）在 C# 3.0 中作为语言集成查询 (LINQ) 的核心构建基块被引入。 这种表达式**只是使用委托的更方便**的语法。 它们将声明参数列表和方法正文，但在分配到委托之前没有自己的正式标识。 与委托不同，可将其作为事件注册的右侧内容或在各种 LINQ 子句和方法中直接分配。

由于 lambda 表达式只是指定委托的另一种方式，因此应可重新编写上述示例，令其使用 lambda 表达式而不是匿名委托。
