---
title: "CSharp析构元组"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "CSharp析构元组"
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

## 析构元组和其他类型

元组提供一种从方法调用中检索多个值的轻量级方法。 但是，一旦检索到元组，就必须处理它的各个元素。

~~~c#
public class Example
{
    public static void Main()
    {
        var result = QueryCityData("New York City");

        var city = result.Item1;
        var pop = result.Item2;
        var size = result.Item3;

         // Do something with the data.
    }

    private static (string, int, double) QueryCityData(string name)
    {
        if (name == "New York City")
            return (name, 8175133, 468.48);

        return ("", 0, 0);
    }
}
~~~

从对象检索多个字段值和属性值可能同样麻烦：必须按成员逐个将字段值或属性值赋给一个变量。

可从元组中检索多个元素，或者在单个析构操作中从对象检索多个字段值、属性值和计算值。 若要析构元组，请将其元素分配给各个变量。 析构对象时，将选定值分配给各个变量。

### 元组

有三种方法可用于析构元组：

- 可以在括号内显式声明每个字段的类型。 以下示例使用此方法来析构由 `QueryCityData` 方法返回的三元组。

~~~c#
public static void Main()
{
    (string city, int population, double area) = QueryCityData("New York City");

    // Do something with the data.
}
~~~

* 可使用 `var` 关键字，以便 C# 推断每个变量的类型。 将 `var` 关键字放在括号外。 以下示例在析构由 `QueryCityData` 方法返回的三元组时使用类型推理。

~~~c#
public static void Main()
{
    var (city, population, area) = QueryCityData("New York City");

    // Do something with the data.
}
~~~

* 还可在括号内将 `var` 关键字单独与任一或全部变量声明结合使用。

~~~c#
public static void Main()
{
    (string city, var population, var area) = QueryCityData("New York City");

    // Do something with the data.
}
~~~

* 

~~~c#
public static void Main()
{
    string city = "Raleigh";
    int population = 458880;
    double area = 144.8;

    (city, population, area) = QueryCityData("New York City");

    // Do something with the data.
}
~~~

### 使用弃元的元组元素

析构元组时，通常只需要关注某些元素的值。 可以利用 C# 对弃元的支持，弃元是一种仅能写入的变量，且其值将被忽略。 在赋值中，通过下划线字符 (_) 指定弃元。 可弃元任意数量的值，且均由单个弃元 `_` 表示。

~~~c#
using System;

public class ExampleDiscard
{
    public static void Main()
    {
        var (_, _, _, pop1, _, pop2) = QueryCityDataForYears("New York City", 1960, 2010);

        Console.WriteLine($"Population change, 1960 to 2010: {pop2 - pop1:N0}");
    }

    private static (string, double, int, int, int, int) QueryCityDataForYears(string name, int year1, int year2)
    {
        int population1 = 0, population2 = 0;
        double area = 0;

        if (name == "New York City")
        {
            area = 468.48;
            if (year1 == 1960)
            {
                population1 = 7781984;
            }
            if (year2 == 2010)
            {
                population2 = 8175133;
            }
            return (name, area, year1, population1, year2, population2);
        }

        return ("", 0, 0, 0, 0, 0);
    }
}
// The example displays the following output:
//      Population change, 1960 to 2010: 393,149
~~~


