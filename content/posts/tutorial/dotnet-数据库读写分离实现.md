---
title: "dotnet 数据库读写分离实现"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "dotnet 数据库读写分离实现"
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

# .NET中实现读写分离

## 什么是读写分离

读写分离是一种数据库架构设计模式，将数据库读操作和写操作分离到不同的数据库实例上执行。主数据库（Master）处理写操作和一些重要的读操作，而从数据库（Slave/Replica）处理大部分读操作。这种模式可以提高系统的并发处理能力，优化数据库负载。

## 读写分离的优势

- **提高系统性能**：分担数据库负载，提高并发处理能力
- **提高系统可用性**：主库故障时，从库可以继续提供读服务
- **便于数据备份**：可以在从库进行备份，不影响主库性能
- **扩展性强**：可以根据读请求的增长方便地添加更多从库

## 在.NET中实现读写分离的方法

### 方法一：使用ADO.NET手动实现

```csharp
public class DatabaseManager
{
    private readonly string _masterConnectionString;
    private readonly string _slaveConnectionString;

    public DatabaseManager(string masterConnectionString, string slaveConnectionString)
    {
        _masterConnectionString = masterConnectionString;
        _slaveConnectionString = slaveConnectionString;
    }

    public SqlConnection GetWriteConnection()
    {
        var connection = new SqlConnection(_masterConnectionString);
        connection.Open();
        return connection;
    }
    public SqlConnection GetReadConnection()
    {
        var connection = new SqlConnection(_slaveConnectionString);
        connection.Open();
        return connection;
    }
    
    public async Task<T> ExecuteWriteOperationAsync<T>(Func<SqlConnection, Task<T>> operation)
    {
        using (var connection = GetWriteConnection())
        {
            return await operation(connection);
        }
    }
    
    public async Task<T> ExecuteReadOperationAsync<T>(Func<SqlConnection, Task<T>> operation)
    {
        using (var connection = GetReadConnection())
        {
            return await operation(connection);
        }
    }
}
```

### 方法二：使用Entity Framework Core实现

#### 1. 创建多个DbContext

```csharp
public class ReadDbContext : AppDbContext
{
    public ReadDbContext(DbContextOptions<ReadDbContext> options) : base(options)
    {
    }
}

public class WriteDbContext : AppDbContext
{
    public WriteDbContext(DbContextOptions<WriteDbContext> options) : base(options)
    {
    }
}

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions options) : base(options)
    {
    }
    
    public DbSet<User> Users { get; set; }
    // 其他实体...
}
```

#### 2. 在Startup.cs注册服务

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // 注册写库上下文
    services.AddDbContext<WriteDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MasterConnection")));
    
    // 注册读库上下文
    services.AddDbContext<ReadDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("SlaveConnection")));
        
    // ...其他服务注册
}
```

#### 3. 创建仓储模式实现读写分离

```csharp
public interface IUserRepository
{
    Task<User> GetByIdAsync(int id);
    Task<IEnumerable<User>> GetAllAsync();
    Task AddAsync(User user);
    Task UpdateAsync(User user);
    Task DeleteAsync(int id);
}

public class UserRepository : IUserRepository
{
    private readonly ReadDbContext _readContext;
    private readonly WriteDbContext _writeContext;

    public UserRepository(ReadDbContext readContext, WriteDbContext writeContext)
    {
        _readContext = readContext;
        _writeContext = writeContext;
    }

    public async Task<User> GetByIdAsync(int id)
    {
        return await _readContext.Users.FindAsync(id);
    }

    public async Task<IEnumerable<User>> GetAllAsync()
    {
        return await _readContext.Users.ToListAsync();
    }

    public async Task AddAsync(User user)
    {
        await _writeContext.Users.AddAsync(user);
        await _writeContext.SaveChangesAsync();
    }

    public async Task UpdateAsync(User user)
    {
        _writeContext.Users.Update(user);
        await _writeContext.SaveChangesAsync();
    }

    public async Task DeleteAsync(int id)
    {
        var user = await _writeContext.Users.FindAsync(id);
        if (user != null)
        {
            _writeContext.Users.Remove(user);
            await _writeContext.SaveChangesAsync();
        }
    }
}
```

### 方法三：使用第三方库实现读写分离

#### 使用EntityFramework Plus

```csharp
// 安装包
// Install-Package Z.EntityFramework.Plus.EFCore

// 配置
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<AppDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
    
    // 配置EF+的读写分离
    services.AddEFSecondLevelCache();
    
    services.AddScoped<DbContextReadWriteManager>();
}

// 实现读写管理器
public class DbContextReadWriteManager
{
    private readonly AppDbContext _context;
    private readonly IConfiguration _configuration;

    public DbContextReadWriteManager(AppDbContext context, IConfiguration configuration)
    {
        _context = context;
        _configuration = configuration;
    }

    public IQueryable<T> AsReadOnly<T>() where T : class
    {
        // 使用SecondLevelCache缓存查询结果
        return _context.Set<T>().AsNoTracking().FromCache();
    }

    public DbSet<T> AsWritable<T>() where T : class
    {
        return _context.Set<T>();
    }
}
```

### 方法四：使用.NET 6+的中间件实现读写分离

#### 1. 创建自定义中间件

```csharp
public class ReadWriteSeparationMiddleware
{
    private readonly RequestDelegate _next;
    private readonly IConfiguration _configuration;

    public ReadWriteSeparationMiddleware(RequestDelegate next, IConfiguration configuration)
    {
        _next = next;
        _configuration = configuration;
    }

    public async Task InvokeAsync(HttpContext context, AppDbContext dbContext)
    {
        // 根据HTTP方法判断是读操作还是写操作
        bool isReadOperation = context.Request.Method == "GET";

        if (isReadOperation)
        {
            // 设置读库连接字符串
            dbContext.Database.GetDbConnection().ConnectionString = 
                _configuration.GetConnectionString("SlaveConnection");
        }
        else
        {
            // 设置写库连接字符串
            dbContext.Database.GetDbConnection().ConnectionString = 
                _configuration.GetConnectionString("MasterConnection");
        }

        await _next(context);
    }
}

// 扩展方法
public static class ReadWriteSeparationMiddlewareExtensions
{
    public static IApplicationBuilder UseReadWriteSeparation(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<ReadWriteSeparationMiddleware>();
    }
}
```

#### 2. 在Startup.cs中配置中间件

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    // 其他中间件...
    
    app.UseReadWriteSeparation();
    
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

### 方法五：使用Dapper实现读写分离

```csharp
public class DapperReadWriteRepository
{
    private readonly string _masterConnectionString;
    private readonly string _slaveConnectionString;

    public DapperReadWriteRepository(IConfiguration configuration)
    {
        _masterConnectionString = configuration.GetConnectionString("MasterConnection");
        _slaveConnectionString = configuration.GetConnectionString("SlaveConnection");
    }

    public async Task<IEnumerable<T>> QueryAsync<T>(string sql, object param = null)
    {
        using (var connection = new SqlConnection(_slaveConnectionString))
        {
            await connection.OpenAsync();
            return await connection.QueryAsync<T>(sql, param);
        }
    }

    public async Task<T> QuerySingleAsync<T>(string sql, object param = null)
    {
        using (var connection = new SqlConnection(_slaveConnectionString))
        {
            await connection.OpenAsync();
            return await connection.QuerySingleAsync<T>(sql, param);
        }
    }

    public async Task<int> ExecuteAsync(string sql, object param = null)
    {
        using (var connection = new SqlConnection(_masterConnectionString))
        {
            await connection.OpenAsync();
            return await connection.ExecuteAsync(sql, param);
        }
    }
}
```

### 方法六：使用SqlSugar实现读写分离

SqlSugar是一个轻量级的.NET ORM框架，它内置了对读写分离的支持。

#### 1. 安装SqlSugar包

```bash
Install-Package SqlSugar
```

#### 2. 配置主从数据库

```csharp
public class SqlSugarContext
{
    private static SqlSugarScope _db;

    public static SqlSugarScope Db
    {
        get
        {
            if (_db == null)
            {
                _db = new SqlSugarScope(new ConnectionConfig()
                {
                    ConnectionString = "Server=master-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
                    DbType = DbType.SqlServer,
                    InitKeyType = InitKeyType.Attribute,
                    IsAutoCloseConnection = true,
                    SlaveConnectionConfigs = new List<SlaveConnectionConfig>()
                    {
                        // 配置从库连接
                        new SlaveConnectionConfig{ 
                            ConnectionString = "Server=slave1-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
                            HitRate = 10 // 权重
                        },
                        new SlaveConnectionConfig{ 
                            ConnectionString = "Server=slave2-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
                            HitRate = 10 // 权重
                        }
                    }
                });
            }
            return _db;
        }
    }
}
```

#### 3. 使用读写分离

```csharp
public class UserRepository
{
    // 读操作自动使用从库
    public async Task<List<User>> GetAllUsersAsync()
    {
        return await SqlSugarContext.Db.Queryable<User>().ToListAsync();
    }

    public async Task<User> GetUserByIdAsync(int id)
    {
        return await SqlSugarContext.Db.Queryable<User>().InSingleAsync(id);
    }

    // 写操作使用主库
    public async Task AddUserAsync(User user)
    {
        await SqlSugarContext.Db.Insertable(user).ExecuteCommandAsync();
    }

    public async Task UpdateUserAsync(User user)
    {
        await SqlSugarContext.Db.Updateable(user).ExecuteCommandAsync();
    }

    public async Task DeleteUserAsync(int id)
    {
        await SqlSugarContext.Db.Deleteable<User>().In(id).ExecuteCommandAsync();
    }
}
```

#### 4. 高级配置：自定义读写逻辑

```csharp
public async Task<User> GetUserWithCustomLogicAsync(int id)
{
    // 强制使用主库进行查询（适用于对一致性要求高的场景）
    var user = await SqlSugarContext.Db.Ado.UseMasterOrElse(()=>
    {
        return SqlSugarContext.Db.Queryable<User>().InSingleAsync(id);
    });
    
    return user;
}

// 使用事务时也会自动切换到主库
public async Task TransferAsync(int fromUserId, int toUserId, decimal amount)
{
    try
    {
        // 开启事务自动使用主库
        SqlSugarContext.Db.Ado.BeginTran();
        
        var fromUser = await SqlSugarContext.Db.Queryable<User>().InSingleAsync(fromUserId);
        var toUser = await SqlSugarContext.Db.Queryable<User>().InSingleAsync(toUserId);
        
        fromUser.Balance -= amount;
        toUser.Balance += amount;
        
        await SqlSugarContext.Db.Updateable(fromUser).ExecuteCommandAsync();
        await SqlSugarContext.Db.Updateable(toUser).ExecuteCommandAsync();
        
        SqlSugarContext.Db.Ado.CommitTran();
    }
    catch (Exception ex)
    {
        SqlSugarContext.Db.Ado.RollbackTran();
        throw;
    }
}
```

#### 5. 配置负载均衡策略

SqlSugar支持两种负载均衡策略：

1. **轮询策略**：按顺序分配查询到从库

```csharp
// 配置轮询策略
new SqlSugarScope(new ConnectionConfig()
{
    // 主库配置...
    SlaveConnectionConfigs = slaveConnections,
    // 默认为轮询策略，可以不设置
    SlaveLoadBalancer = new RoundRobinSqlSugarLoadBalancer() 
});
```

2. **权重策略**：根据HitRate权重分配查询

```csharp
// 配置权重策略，根据SlaveConnectionConfig中的HitRate进行分配
new SqlSugarScope(new ConnectionConfig()
{
    // 主库配置...
    SlaveConnectionConfigs = slaveConnections,
    SlaveLoadBalancer = new WeightSqlSugarLoadBalancer() 
});
```

#### 6. 与依赖注入集成

```csharp
// 在Startup.cs中注册SqlSugar服务
public void ConfigureServices(IServiceCollection services)
{
    // 注册SqlSugar服务
    services.AddSingleton<ISqlSugarClient>(sp =>
    {
        var config = new ConnectionConfig()
        {
            ConnectionString = Configuration.GetConnectionString("MasterConnection"),
            DbType = DbType.SqlServer,
            InitKeyType = InitKeyType.Attribute,
            IsAutoCloseConnection = true,
            SlaveConnectionConfigs = new List<SlaveConnectionConfig>()
            {
                new SlaveConnectionConfig{
                    ConnectionString = Configuration.GetConnectionString("SlaveConnection1"),
                    HitRate = 10
                },
                new SlaveConnectionConfig{
                    ConnectionString = Configuration.GetConnectionString("SlaveConnection2"),
                    HitRate = 10
                }
            }
        };
        
        return new SqlSugarScope(config);
    });
    
    // 注册仓储
    services.AddScoped<IUserRepository, UserRepository>();
    
    // 其他服务注册...
}
```

#### 7. 多租户场景下的读写分离

```csharp
public class MultiTenantDbContext
{
    public static SqlSugarScope GetDb()
    {
        return new SqlSugarScope(new List<ConnectionConfig>()
        {
            // 租户1的配置（包含读写分离）
            new ConnectionConfig()
            {
                ConfigId = "tenant1",
                ConnectionString = "Server=tenant1-master;Database=Tenant1Db;User Id=sa;Password=YourPassword;",
                DbType = DbType.SqlServer,
                IsAutoCloseConnection = true,
                SlaveConnectionConfigs = new List<SlaveConnectionConfig>()
                {
                    new SlaveConnectionConfig{ 
                        ConnectionString = "Server=tenant1-slave;Database=Tenant1Db;User Id=sa;Password=YourPassword;",
                        HitRate = 10
                    }
                }
            },
            // 租户2的配置（包含读写分离）
            new ConnectionConfig()
            {
                ConfigId = "tenant2",
                ConnectionString = "Server=tenant2-master;Database=Tenant2Db;User Id=sa;Password=YourPassword;",
                DbType = DbType.SqlServer,
                IsAutoCloseConnection = true,
                SlaveConnectionConfigs = new List<SlaveConnectionConfig>()
                {
                    new SlaveConnectionConfig{ 
                        ConnectionString = "Server=tenant2-slave;Database=Tenant2Db;User Id=sa;Password=YourPassword;",
                        HitRate = 10
                    }
                }
            }
        });
    }
}

// 使用多租户
public async Task<List<User>> GetTenantUsersAsync(string tenantId)
{
    var db = MultiTenantDbContext.GetDb();
    return await db.GetConnection(tenantId).Queryable<User>().ToListAsync();
}
```

SqlSugar的读写分离实现简单高效，自动根据操作类型（查询/增删改）来选择合适的数据库连接，无需开发人员手动管理连接。它内置的负载均衡策略也可以灵活应对不同规模的系统需求。

## 负载均衡策略

在实现读写分离时，如果有多个从库，可以实现不同的负载均衡策略：

### 1. 轮询策略（Round Robin）

```csharp
public class RoundRobinLoadBalancer
{
    private readonly List<string> _slaveConnectionStrings;
    private int _currentIndex = 0;
    private readonly object _lock = new object();

    public RoundRobinLoadBalancer(IConfiguration configuration)
    {
        _slaveConnectionStrings = configuration.GetSection("SlaveConnections")
            .Get<List<string>>();
    }

    public string GetConnection()
    {
        lock (_lock)
        {
            if (_currentIndex >= _slaveConnectionStrings.Count)
            {
                _currentIndex = 0;
            }
            
            return _slaveConnectionStrings[_currentIndex++];
        }
    }
}
```

### 2. 随机策略（Random）

```csharp
public class RandomLoadBalancer
{
    private readonly List<string> _slaveConnectionStrings;
    private readonly Random _random = new Random();

    public RandomLoadBalancer(IConfiguration configuration)
    {
        _slaveConnectionStrings = configuration.GetSection("SlaveConnections")
            .Get<List<string>>();
    }

    public string GetConnection()
    {
        int index = _random.Next(0, _slaveConnectionStrings.Count);
        return _slaveConnectionStrings[index];
    }
}
```

### 3. 权重策略（Weighted）

```csharp
public class WeightedLoadBalancer
{
    private readonly List<SlaveDatabase> _slaveDatabases;
    private readonly Random _random = new Random();

    public WeightedLoadBalancer(IConfiguration configuration)
    {
        _slaveDatabases = configuration.GetSection("SlaveDatabases")
            .Get<List<SlaveDatabase>>();
    }

    public string GetConnection()
    {
        int totalWeight = _slaveDatabases.Sum(db => db.Weight);
        int randomValue = _random.Next(1, totalWeight + 1);
        
        int weightSum = 0;
        foreach (var database in _slaveDatabases)
        {
            weightSum += database.Weight;
            if (randomValue <= weightSum)
            {
                return database.ConnectionString;
            }
        }
        
        // 默认返回第一个
        return _slaveDatabases.First().ConnectionString;
    }
}

public class SlaveDatabase
{
    public string ConnectionString { get; set; }
    public int Weight { get; set; }
}
```

## 数据一致性处理

读写分离面临的主要挑战是数据一致性。主库和从库之间的数据同步存在延迟，可能导致从库读取到旧数据。

### 处理数据一致性的策略：

1. **延迟容忍**：对于可以容忍数据延迟的业务，直接从从库读取
2. **强一致性要求**：对于要求强一致性的业务，从主库读取
3. **会话一致性**：在同一会话内，写操作后的读操作都路由到主库

```csharp
public class ConsistencyAwareRepository
{
    private readonly ReadDbContext _readContext;
    private readonly WriteDbContext _writeContext;
    private readonly IHttpContextAccessor _httpContextAccessor;

    public ConsistencyAwareRepository(
        ReadDbContext readContext, 
        WriteDbContext writeContext,
        IHttpContextAccessor httpContextAccessor)
    {
        _readContext = readContext;
        _writeContext = writeContext;
        _httpContextAccessor = httpContextAccessor;
    }

    public async Task<User> GetByIdAsync(int id)
    {
        // 检查会话状态，判断是否需要从主库读取
        if (HasRecentWriteOperation())
        {
            return await _writeContext.Users.FindAsync(id);
        }
        
        return await _readContext.Users.FindAsync(id);
    }

    private bool HasRecentWriteOperation()
    {
        // 检查会话中是否有标记表明最近有写操作
        return _httpContextAccessor.HttpContext.Session.GetString("HasRecentWrite") == "true";
    }

    public async Task AddAsync(User user)
    {
        await _writeContext.Users.AddAsync(user);
        await _writeContext.SaveChangesAsync();
        
        // 标记当前会话有写操作
        _httpContextAccessor.HttpContext.Session.SetString("HasRecentWrite", "true");
    }
}
```

## 读写分离配置示例（appsettings.json）

```json
{
  "ConnectionStrings": {
    "MasterConnection": "Server=master-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
    "SlaveConnection": "Server=slave-server;Database=MyDatabase;User Id=sa;Password=YourPassword;"
  },
  "SlaveConnections": [
    "Server=slave1-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
    "Server=slave2-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
    "Server=slave3-server;Database=MyDatabase;User Id=sa;Password=YourPassword;"
  ],
  "SlaveDatabases": [
    {
      "ConnectionString": "Server=slave1-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
      "Weight": 5
    },
    {
      "ConnectionString": "Server=slave2-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
      "Weight": 3
    },
    {
      "ConnectionString": "Server=slave3-server;Database=MyDatabase;User Id=sa;Password=YourPassword;",
      "Weight": 2
    }
  ]
}
```

## 读写分离最佳实践

1. **合理划分读写操作**：明确区分哪些操作是读操作，哪些是写操作
2. **处理数据一致性问题**：根据业务需求选择合适的数据一致性策略
3. **监控主从延迟**：定期检查主从同步状态，避免数据延迟过大
4. **合理设置连接池**：根据读写比例配置主从数据库的连接池大小
5. **异常处理机制**：当从库不可用时，应有降级策略（如切换到主库）
6. **分布式事务处理**：在涉及多个数据源的事务中，确保事务的一致性

## 总结

.NET中实现读写分离有多种方式，可以根据项目需求和复杂度选择合适的实现方法。无论采用哪种方式，都需要注意数据一致性、异常处理和负载均衡等问题。读写分离是提高系统性能和可用性的重要手段，但也增加了系统的复杂度，需要权衡利弊后再决定是否采用。 

## sqlsugar 具体实现
~~~c#
using Myblog.Entity.Enties;
using Myblog.Entity.Enties.Interface;
using NLog;
using SqlSugar;

namespace Myblog.WebApi.Extension;

/// <summary>
/// sqlsugar配置
/// </summary>
public static class SqlsugarExtension
{
    public static void AddSqlsugar(this WebApplicationBuilder builder)
    {
        // 注册SqlSugar
        builder.Services.AddSingleton<ISqlSugarClient>(s =>
        {
            Console.WriteLine("初始化数据库配置");
            
            // 配置主从库连接
            List<SlaveConnectionConfig> slaveConnectionConfigs = new List<SlaveConnectionConfig>();
            // 添加从库配置
            string? slaveConnection = builder.Configuration.GetSection("ConnectionStrings:MySqlSlave").Value;
            if (!string.IsNullOrEmpty(slaveConnection))
            {
                slaveConnectionConfigs.Add(new SlaveConnectionConfig
                {
                    ConnectionString = slaveConnection,
                    HitRate = 10 // 访问权重
                });
            }
            

            
            SqlSugarScope sqlSugar = new SqlSugarScope(new ConnectionConfig()
                {
                    DbType = SqlSugar.DbType.MySql,
                    InitKeyType = InitKeyType.Attribute,
                    ConnectionString = builder.Configuration.GetSection("ConnectionStrings:MySql").Value, // 主库连接字符串
                    IsAutoCloseConnection = true,
                    SlaveConnectionConfigs = slaveConnectionConfigs, // 从库配置
                },
                db =>
                {
                    //单例参数配置，所有上下文生效
                    db.Aop.OnLogExecuting = (sql, pars) =>
                    {
                        // 获取完整的 SQL 语句（替换参数）
                        var fullSql = sql;
                        foreach (var parameter in pars)
                        {
                            // 根据参数类型处理替换
                            var value = parameter.Value?.ToString();
                            if (parameter.DbType == System.Data.DbType.String
                                || parameter.DbType == System.Data.DbType.DateTime
                                || parameter.DbType == System.Data.DbType.Date)
                            {
                                value = $"'{value}'";
                            }

                            fullSql = fullSql.Replace(parameter.ParameterName, value ?? "NULL");
                        }

                        // 获取当前执行的数据库类型（主库/从库）
                        string dbType = "Master";
                        if (sql.Contains("/*Slave*/"))
                        {
                            dbType = "Slave";
                        }

                        var logger = LogManager.GetCurrentClassLogger();
                        DateTime date = DateTime.Now.Date.ToLocalTime();
                        Console.WriteLine($"===================SQL开始:{date}====================");
                        Console.WriteLine($"当前使用数据库: {dbType}");
                        logger.Info($"OP:SQL:[{dbType}]\r\n{fullSql}");
                        Console.WriteLine("===================SQL结束====================");
                    };
                });

            // 初始化数据库
            string? initDatabasevalue = builder.Configuration.GetSection("ConnectionStrings:InitDatabase").Value;
            bool initDatabase;
            bool.TryParse(initDatabasevalue, out initDatabase);
            if (initDatabase)
            {
                sqlSugar.DbMaintenance.CreateDatabase();
                // 初始化表
                Type[] types = typeof(Albums).Assembly.GetTypes()
                    .Where(it => it.IsClass && !it.IsAbstract && typeof(IEntity).IsAssignableFrom(it))
                    .ToArray();
                foreach (var type in types)
                {
                    Console.WriteLine("初始化" + type.Name);
                }
                sqlSugar.CodeFirst.InitTables(types);
            }
            return sqlSugar;
        });
    }
}
~~~
##  编写拓展方法实现具体的读写操作使用库
~~~c#
using SqlSugar;

namespace Myblog.Repository
{
    /// <summary>
    /// 数据库读写分离扩展方法
    /// </summary>
    public static class DatabaseExtensions
    {
        /// <summary>
        /// 获取主库查询对象（用于写操作）
        /// </summary>
        public static ISugarQueryable<T> AsMaster<T>(this ISugarQueryable<T> queryable) where T : class, new()
        {
            return queryable.AS("Master");
        }

        /// <summary>
        /// 获取从库查询对象（用于读操作）
        /// </summary>
        public static ISugarQueryable<T> AsSlave<T>(this ISugarQueryable<T> queryable) where T : class, new()
        {
            return queryable.AS("Slave");
        }

        /// <summary>
        /// 使用从库执行查询（带缓存）
        /// </summary>
        public static ISugarQueryable<T> AsSlaveWithCache<T>(this ISugarQueryable<T> queryable, int cacheDurationInSeconds = 60) where T : class, new()
        {
            return queryable.WithCache(cacheDurationInSeconds).AS("Slave");
        }
    }
} 
~~~


## 在基础仓储层配置读写操作
~~~
  #region 写操作 - 使用主库

        public async Task<bool> AddAsync(T entity)
        {
            return await base.InsertAsync(entity);
        }

        public async Task<bool> DeleteAsync(long id)
        {
            return await base.DeleteByIdAsync(id);
        }

        public async Task<bool> UpdateAsync(T entity)
        {
            return await base.UpdateAsync(entity);
        }

        #endregion
~~~



~~~c#
   #region 读操作 - 使用从库

        public async Task<T> FindAsync(long id)
        {
            // 使用从库查询
            return await Context.Queryable<T>().AsSlaveWithCache().InSingleAsync(id);
        }

        public async Task<List<T>> QueryAsync()
        {
            // 使用从库查询
            return await Context.Queryable<T>().AsSlaveWithCache().ToListAsync();
        }

        public async Task<List<T>> FindPageListAsync(Expression<Func<T, bool>>? func)
        {
            // 使用从库查询
            if (func == null)
            {
                return await Context.Queryable<T>().AsSlaveWithCache().ToListAsync();
            }
            return await Context.Queryable<T>().AsSlaveWithCache().Where(func).ToListAsync();
        }

        public async Task<List<T>> PageListAsync(int page, int size, RefAsync<int> total)
        {
            // 使用从库查询
            return await Context.Queryable<T>().AsSlave().ToPageListAsync(page, size, total);
        }

        public async Task<List<T>> PageListAsync(Expression<Func<T, bool>> func, int page, int size,
            RefAsync<int> total)
        {
            // 使用从库查询
            return await Context.Queryable<T>()
                .AsSlave()
                .Where(func)
                .ToPageListAsync(page, size, total);
        }

        #endregion
~~~


