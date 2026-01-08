---
title: "c#高级之反射常见操作"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "c#高级之反射常见操作"
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

~~~c#
public class RefTest1
{
    // 反射获取所有属性  公共的字段
    [Test]
    public void Test1()
    {
        var propertyInfos = typeof(StudentInfo).GetProperties();
        foreach (var propertyInfo in propertyInfos)
        {
            Console.WriteLine($"属性名：{propertyInfo.Name}, 属性类型：{propertyInfo.PropertyType}");
        }
    }

    // 利用反射获取所有字段
    // 字段其实就是封装了属性
    [Test]
    public void Test2()
    {
        // 获取类型
        var type = typeof(StudentInfo);
        var fieldInfos = type.GetFields(BindingFlags.NonPublic | BindingFlags.Instance);
        foreach (var fieldInfo in fieldInfos)
        {
            Console.WriteLine($"字段名：{fieldInfo.Name} 字段类型：{fieldInfo.FieldType}");
        }

        // 获取单个字段
        var field = type.GetField("Address", BindingFlags.Instance | BindingFlags.NonPublic);
        Console.WriteLine($"字段名：{field.Name} 字段类型：{field.FieldType}");
    }

    // 利用反射获取全类名
    [Test]
    public void FullName()
    {
        var type = typeof(StudentInfo);
        Console.WriteLine($"命名空间：{type.Namespace}");
        var typeFullName = type.FullName;
        Console.WriteLine($"全类名：{typeFullName}");
        Console.WriteLine($"基类名：{type.BaseType}");
    }


    // 利用反射创建对象
    [Test]
    public void CreateInstance()
    {
        var type = typeof(StudentInfo);
        // 创建对象底层实际就是调用无参构造参数
        var studentInfo = Activator.CreateInstance(type) as StudentInfo;
        // 调用属性
        // studentInfo.Name = "张三";
        // Console.WriteLine($"{studentInfo.Name}");
    }

    // 操作属性
    [Test]
    public void Test3()
    {
        //1. 获取类型
        var type = typeof(StudentInfo);
        //2. 获取一个属性
        var fieldInfo = type.GetProperty("Name");
        //3. 通过属性创建对象
        var obj = Activator.CreateInstance(type);
        fieldInfo.SetValue(obj, "ljw"); // 属性赋值
        var value = fieldInfo.GetValue(obj); // 获取属性值
        Console.WriteLine(value);
    }

    // 操作字段
    [Test]
    public void Test4()
    {
        // 1. 获取类型
        var type = typeof(StudentInfo);
        // 获取字段
        var address = type.GetField("Address", BindingFlags.Instance | BindingFlags.NonPublic);
        // 创建对象
        var obj = Activator.CreateInstance(type);
        address.SetValue(obj, "北京");
        Console.WriteLine($"字段的值： {address.GetValue(obj)}");
    }


    // 利用反射加载程序集，创建对象等
    [Test]
    public void LoadingAnAssembly()
    {
        // 当当前程序集引用其他程序集时，可以通过程序集名称来加载程序集，并且创建对象
        // var assembly = Assembly.Load("Step.Unit2");
        // var instance = assembly.CreateInstance("Step.Unit2.MyTest",false);
        // var myTest = instance as MyTest;
        // myTest.run();

        // 在当前程序集没有引用其他程序集的情况下，可以使用程序集路径创建对象
        var unit2 = Assembly.LoadFile(@"E:\c#\high-Csharp\Step.Unit2\bin\Debug\net6.0\Step.Unit2.dll");
        var instance = unit2.CreateInstance("Step.Unit2.MyTest", false);
        var type = instance.GetType();
        var methodInfos = type.GetMethods();
        foreach (var methodInfo in methodInfos)
        {
            Console.WriteLine(methodInfo.Name);
        }


        //获取程序集完整路径
        string location = Assembly.GetExecutingAssembly().Location;

        //获取当前程序集名称
        string file = Assembly.GetExecutingAssembly().GetName().Name;

        //获取程序集版本号
        string version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
    }
~~~
