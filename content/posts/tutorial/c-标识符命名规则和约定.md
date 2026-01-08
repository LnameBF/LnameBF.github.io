---
title: "C# 标识符命名规则和约定"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "C# 标识符命名规则和约定"
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

## C# 标识符命名规则和约定

>标识符是分配给类型（类、接口、结构、委托或枚举）、成员、变量或命名空间的名称。

### 命名规则

* 标识符必须以字母或下划线 (`_`) 开头。

注意：可以在标识符上使用 @ 前缀来声明与 C# 关键字匹配的标识符。 @ 不是标识符名称的一部分。 例如，@if 声明名为 if 的标识符。 这些逐字标识符主要用于与使用其他语言声明的标识符的互操作性。

### 命名约定

按照约定，C# 程序对类型名称、命名空间和所有公共成员使用 `PascalCase`。

- 接口名称以大写字母 `I` 开头。

~~~c#
public interface IUserQueue
{

}
~~~

- 属性类型以单词 `Attribute` 结尾。



- 枚举类型对非标记使用单数名词，对标记使用复数名词。



- 标识符不应包含两个连续的下划线 (`_`) 字符。 这些名称保留给编译器生成的标识符。



- 对变量、方法和类使用有意义的描述性名称。
- 清晰胜于简洁。。
- 将 PascalCase 用于类名和方法名称。
- 对方法参数和局部变量使用驼峰式大小写。
- 将 PascalCase 用于常量名，包括字段和局部常量。
- 专用实例字段以下划线 (`_`) 开头，其余文本为驼峰式大小写。
- 静态字段以 `s_` 开头。 此约定不是默认的 Visual Studio 行为，也不是[框架设计准则](https://learn.microsoft.com/zh-cn/dotnet/standard/design-guidelines/names-of-type-members#names-of-fields)的一部分，但[在 editorconfig 中可配置](https://learn.microsoft.com/zh-cn/dotnet/fundamentals/code-analysis/style-rules/naming-rules)。
- 避免在名称中使用缩写或首字母缩略词，但广为人知和广泛接受的缩写除外。
- 使用遵循反向域名表示法的有意义的描述性命名空间。
- 选择表示程序集主要用途的程序集名称。
- 避免使用单字母名称，但简单循环计数器除外。 此外，描述 C# 构造的语法示例通常使用与 [C# 语言规范](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/readme)中使用的约定相匹配的以下单字母名称。 语法示例是规则的例外。
- 将 `S` 用于结构，将 `C` 用于类。
- 将 `M` 用于方法。
- 将 `v` 用于变量，将 `p` 用于参数。
- 将 `r` 用于 `ref` 参数。

### Pascal 大小写

在命名 `class`、`interface`、`struct` 或 `delegate` 类型时，使用 Pascal 大小写

```csharp
public class DataService
{

}
```

```csharp

public record PhysicalAddress(
string Street,
string City,
string StateOrProvince,
string ZipCode);
```

```csharp
public struct ValueCoordinate
{

}
```

```csharp
public delegate void DelegateType(string message);
```

命名 `interface` 时，使用 pascal 大小写并在名称前面加上前缀 `I`。 此前缀可以清楚地向使用者表明这是 `interface`。

```csharp
public interface IWorkerQueue
{

}
```

在命名字段、属性和事件等类型的 `public` 成员时，使用 pascal 大小写。 此外，对所有方法和本地函数使用 pascal 大小写。

```csharp
public class ExampleEvents
{
// A public field, these should be used sparingly
public bool IsValid;

// An init-only property
public IWorkerQueue WorkerQueue { get; init; }

// An event
public event Action EventProcessing;

// Method
public void StartEventProcessing()
{
// Local function
static int CountQueueItems() => WorkerQueue.Count;
// ...
}
}
```

编写位置记录时，对参数使用 pascal 大小写，因为它们是记录的公共属性。

C#

```csharp
public record PhysicalAddress(
string Street,
string City,
string StateOrProvince,
string ZipCode);
```

### 驼峰式大小写

命名 私有或者 internal字段时，使用驼峰命名，并添加_作为前缀，命名局部变量（包括委托类型的实例）时，也使用驼峰式大小写。

~~~c#
public class DataService
{
private IWorkerQueue _workerQueue;
}
~~~

static 字段 使用s _或者 静态线程使用t _

~~~c#
public class DataService
{
private static IWorkerQueue s_workerQueue;

[ThreadStatic]
private static TimeSpan t_timeSpan;
}
~~~

编写方法参数时，请使用驼峰式大小写。

~~~c#
public T SomeMethod(int someNumber, bool isValid)
{

}
~~~

### 类型参数命名指南(泛型)

注意：以上内容来自微软官方文档，只是用来记录，做一个学习笔记

## c#编码约定

### 字符串

- 使用[字符串内插](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/tokens/interpolated)来连接短字符串，如下面的代码所示。

```csharp
string displayName = $"{nameList[n].LastName}, {nameList[n].FirstName}";
```

- 若要在循环中追加字符串，尤其是在使用大量文本时，请使用 [System.Text.StringBuilder](https://learn.microsoft.com/zh-cn/dotnet/api/system.text.stringbuilder) 对象。（why？）

>String 类：
>
>1.不可变性：字符串是不可变的，每次对字符串进行修改（如拼接、替换等）时，都会创建一个新的字符串实例。
>2.内存分配：由于不可变性，每次字符串操作都需要在内存堆中为新字符串分配空间，这会导致频繁的内存分配和垃圾回收。
>3.性能：对于单个或少量的字符串操作，性能影响可能不大。但在大量或频繁的字符串连接操作（尤其是循环中），会产生大量的中间字符串，严重影响性能。
>
>
>
>StringBuilder 类：
>
>1.可变性：StringBuilder 是可变的，可以在原对象上直接修改内容，不会生成新的对象。
>2.内存效率：它预先分配了一定大小的缓冲区，并且可以根据需要动态扩展容量，减少了内存分配次数，从而提高了内存使用效率。
>3.性能：适用于处理多个字符串拼接的情况，尤其是在循环或其他需要多次修改字符串的场景下，其性能远优于 String 类。

```csharp
var phrase = "lalalalalalalalalalalalalalalalalalalalalalalalalalalalalala";
var manyPhrases = new StringBuilder();
for (var i = 0; i < 10000; i++)
{
manyPhrases.Append(phrase);
}
//Console.WriteLine("tra" + manyPhrases);
```
