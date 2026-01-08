---
title: "c#基础知识+业务场景分享"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "c#基础知识+业务场景分享"
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

## 一 、基础知识
1. 值类型 vs 引用类型
追问：int、struct和class在内存分配上的区别？什么是装箱拆箱？如何避免装箱带来的性能问题？

2. 异常处理机制
追问：catch (Exception ex)和finally的执行顺序？using语句在底层如何保证资源释放？

3. 多线程与异步
追问：async/await和Task.Run()的本质区别是什么？lock关键字底层如何实现？什么是死锁场景？
字符串与性能

4. 问题：string 和 StringBuilder 的区别？在循环中拼接字符串时为什么优先用 StringBuilder？
追问：ReadOnlySpan<char> 和 string 在内存分配上的区别？

5. 集合与泛型
问题：List<T> 和 Array 的适用场景？Dictionary<TKey, TValue> 的哈希冲突如何解决？
追问：IEnumerable 和 IQueryable 的区别？LINQ 的延迟执行是什么？

6. 委托与事件
问题：如何定义一个事件？Action、Func 和 Predicate 的区别？
追问：事件为什么通常用 EventHandler 模式？如何避免事件订阅导致的内存泄漏？

7. GC 与内存管理
问题：什么是 Generation 0/1/2 内存回收？GC.Collect() 可以随意调用吗？
追问：IDisposable 接口的作用？如何实现一个资源释放安全的类？

8. 接口与抽象类
问题：什么情况下用接口而不是抽象类？接口的显式实现（Explicit Interface）有什么用途？

## 二、开发场景实战
1. 数据库性能问题
场景：EF Core查询一个包含10万条记录的Users表时速度缓慢，如何优化？
追问：什么是N+1查询问题？如何通过Include()和延迟加载优化？

2. 依赖注入设计
场景：在ASP.NET Core中，为什么要避免在构造函数中直接new对象？如何通过IServiceCollection注册生命周期（Scoped vs Singleton）？

3. 并发冲突处理
场景：两个线程同时修改同一个文件导致数据覆盖，如何用Mutex或Semaphore解决？乐观锁在数据库中的实现方式是什么

4. 场景 1：Web API 开发
问题：设计一个用户注册接口时，如何保证密码安全性？如何防止接口被恶意频繁调用？
追问：密码存储是否应该加密？使用哪种哈希算法（如BCrypt）？
如何用 Redis + 滑动窗口限制同一IP的请求频率？

5. 场景 2：数据库设计
问题：订单表（Order）和订单详情表（OrderItem）如何设计关联？如何用 EF Core 实现级联删除？
追问：
如果订单表有百万级数据，分页查询 Skip(100000).Take(20) 为什么慢？如何优化？
分库分表时如何保证订单ID全局唯一？
6. 场景 3：缓存与一致性
问题：商品详情页需要高并发读取，如何用缓存（如Redis）提升性能？如何解决缓存穿透和雪崩问题？
追问：
缓存更新策略（Cache-Aside、Write-Through、Write-Behind）的优缺点？
如何用 RedLock 实现分布式锁？
8. 场景 4：身份认证与授权
问题：在 ASP.NET Core 中如何实现 JWT 认证？如何控制不同角色（Role）对 API 的访问权限？
追问：
[Authorize(Policy = "AdminOnly")] 中的 Policy 如何自定义？
如何实现 OAuth 2.0 的授权码模式？
9. 场景 5：分布式系统问题
问题：支付服务调用第三方接口超时，如何设计重试机制？如何保证接口的幂等性（Idempotency）？
追问：什么是幂等键（Idempotency Key）？如何用数据库唯一索引实现？
如何用 Polly 实现熔断（Circuit Breaker）策略？

## 三、调试与架构思维
1. 内存泄漏排查
场景：程序运行一段时间后内存飙升，如何用dotMemory或Windbg分析未释放的EventHandler引用？

2. 设计模式应用
场景：如何用观察者模式实现一个日志系统？工厂方法模式在DI框架中的作用是什么？

## 四、开放性问题
1. 技术决策
问题：在旧项目中遇到大量static类和方法，你会如何推动重构？如何评估重构风险？

2. 学习与成长
问题：过去一年你遇到的最高技术挑战是什么？未来半年计划深入哪个技术方向（如微服务、性能优化等）？

## 五、陷阱问题（考察细节）
1. 隐式类型转换
问题：var list = new ArrayList(); list.Add(1); list.Add("2"); 这段代码是否安全？为什么？
LINQ 的延迟执行
问题：以下代码会抛出异常吗？为什么？
```csharp
var numbers = new List<int> { 1, 2, 3 };
var query = numbers.Where(x => x > 0);
numbers.Add(4);
Console.WriteLine(query.Count()); // 输出是3还是4？
```
## 评价标准
基础扎实性：能否清晰解释IL、CLR等底层概念？
代码质量：变量命名、异常处理边界是否严谨？
实战经验：是否在回答中关联真实项目（如“我曾在XX项目中用Redis解决缓存穿透”）？
逻辑表达：能否用“问题-方案-结果”结构化描述技术决策？
