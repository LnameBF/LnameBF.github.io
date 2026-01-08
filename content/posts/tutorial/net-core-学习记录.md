---
title: ".NET Core 学习记录"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: ".NET Core 学习记录"
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

### 依赖注入

![*](https://file.heiok.top/note/202408142231236.png)

* 缓存（客户端缓存和服务器端缓存）

![image-20240814225929798](https://file.heiok.top/note/202408142259968.png)

![image-20240814233757373](https://file.heiok.top/note/202408142337527.png)

> 存储Iqueryable或者 IEnumerable等类型延迟加载的对象到缓存中，可能会出现一些问题

### 分布式缓存

![image-20240815234211742](https://file.heiok.top/note/202408152342134.png)

### 配置文件读取

加载顺序

![image-20240816230811595](https://file.heiok.top/note/202408162308803.png)

### Efcore使用（数据库使用的Mysql）

1. 创建实体类模块

![image-20240817230110287](https://file.heiok.top/note/202408172301382.png)

2. 安装依赖包（Microsoft.EntityFrameworkCore.Relational）

>`Microsoft.EntityFrameworkCore.Relational` 是 Entity Framework Core (EF Core) 的一部分，它提供了一些与关系型数据库（如 SQL Server、PostgreSQL、MySQL 等）交互的基础设施和功能。

3. 创建一个Dbcontext提供给迁移数据库使用（在多项目中）

![](https://file.heiok.top/note/202408172301920.png)

* 目录结构

![image-20240817234853373](https://file.heiok.top/note/202408172348443.png)

~~~c#
public class MyDbcContextDesignFac : IDesignTimeDbContextFactory<MyDbContext>
{
    /// <summary>
    /// 开发环境中，给创建数据库提供一个Dbcontext，帮助执行 init，update等命令
    /// </summary>
    /// <param name="args"></param>
    /// <returns></returns>
    /// <exception cref="NotImplementedException"></exception>
    public MyDbContext CreateDbContext(string[] args)
    {
        DbContextOptionsBuilder<MyDbContext> builder = new DbContextOptionsBuilder<MyDbContext>();
        string connstr =
            "server=119.29.250.96;userid=root;pwd=Root!@qq123;port=3306;database=CSharp;sslmode=none;CharSet=utf8;";
        builder.UseMySql(connstr, ServerVersion.AutoDetect(connstr));
        MyDbContext myDbContext = new MyDbContext(builder.Options);
        return myDbContext;
    }
}
~~~

* 执行更新命令

~~~
add-Migration init
update-database
// 注意，使用这两个命令需要安装Microsoft.EntityFrameworkCore.Tools和 Pomelo.EntityFrameworkCore.MySql
~~~
可以看到生成成功了
![image-20240817235108724](https://file.heiok.top/note/202408172351750.png)

#### 多租户模式（多个DbContext）

## 过滤器

所有的过滤器都可以使用这样的方式注入到容器中

~~~c#
builder.Services.AddControllers(opt =>
{
    // order代表执行顺序，order值越小，则越先执行
    // 使用泛型注入
    // o.Filters.Add<CtmAuthorizationFilterAttribute>(1);
    // o.Filters.Add<CtmResourceFilterAttribute>(2);
    // 使用typeof注入
    // o.Filters.Add(typeof(CtmActionFilterAttribute));
    opt.Filters.Add<GlobalExceptionFilter>();
    opt.Filters.Add<MyActionFilter>();
    opt.Filters.Add<MyActionFilter2>();

});

// 或者这样
// builder.Services.Configure<MvcOptions>(opt =>
// {
//     opt.Filters.Add<GlobalExceptionFilter>();
// });
~~~

#### ExceptionFilter

例子:全局异常处理器

~~~c#
public class GlobalExceptionFilter : IAsyncExceptionFilter
{
    private readonly ILogger<GlobalExceptionFilter> _logger;

    public GlobalExceptionFilter(ILogger<GlobalExceptionFilter> logger)
    {
        _logger = logger;
    }

    public Task OnExceptionAsync(ExceptionContext context)
    {
        //context.Exception代表异常信息对象
        //如果给context.ExceptionHandled赋值为true，则其他ExceptionFilter不会再执行
        //context.Resul的值会被输出给客户端

        var exception = context.Exception;

        // 这里可以记录日志，发送通知等
        // Console.WriteLine($"Exception: {exception.Message}");
        _logger.LogError(context.Exception.Message);
        // 设置响应状态码和消息
        context.Result = new ObjectResult(new
        {
            error = true,
            message = exception.Message
        })
        {
            StatusCode = 500
        };

        // 标记异常已处理（标记已经处理后，后面的异常过滤器就不会对异常进行处理了，也不回执行后面的异常过滤器）
        context.ExceptionHandled = true;
        return Task.CompletedTask;
    }
}
~~~

自定义异常处理中间件

中间件执行顺序

https://www.cnblogs.com/lucky_hu/p/12444832.html

#### ActionFilter

![image-20240819205058351](https://file.heiok.top/note/202408192050445.png)

例，编写一个事物的过滤器

~~~c#
// 也可编写特性，放在需要开启事物的方法下
public class TransactionAttribute :ActionFilterAttribute
{
    public override async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
    {
        using (TransactionScope tx = new TransactionScope(TransactionScopeAsyncFlowOption.Suppress))
        {
            //
            var result = await next();
            // 执行之后进行判断。如果没有出现异常，就提交事物，没有提交事物（执行Complete()），默认不提交，也就无法保存成功
            if (result.Exception == null)
            {
                tx.Complete();
            }
        }
    }
}
~~~

## 中间件


