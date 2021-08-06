---
title: .NET Core WebAPI使用EFCore连接MySQL
date: 2020-07-09 18:00:00
categories: DotNET
tags: ['技术']
comment: false
---
## 记录一下DotNET Core使用EFCore连接MySQL数据库
<!-- more -->
### 首先从nuget包里面找到MySQL.Data.EntityFrameworkCore进行安装


![EFCore](EFCore.png)

### 因为我的.NET Core是最新版本3.1，所以这个EFCore我安装的也是最新版本8.0.20

### 第一步在配置文件appsettings.json里面添加连接字符串

```csharp
"ConnectionStrings": {
    "MySQLConnection": "server=本地或者线上地址;uid=用户名;pwd=密码;port=端口号;database=需要连接的数据库名称;SslMode=None"
  }
```
### 第二步添加数据库上下文类
```csharp
public class EFCoreDbContext:DbContext
{
    public virtual DbSet<UserInfo> Users { get; set; } //将实体类添加Context中

    public EFCoreDbContext(DbContextOptions<EFCoreDbContext> options) : base(options)
    {

    }
}
```
### 第三步Startup.cs里面的ConfigureServices方法下面读取服务添加到容器
```csharp
services.AddDbContext<EFCoreDbContext>(options =>
options.UseMySQL(Configuration.GetConnectionString("MySQLConnection")));
```
### 最后在需要使用到数据库的控制器内添加构造函数，初始化数据库上下文类
```csharp
/// <summary>
/// 初始化数据库上下文
/// </summary>
private readonly EFCoreDbContext _efCoreDbContext;
/// <summary>
/// 构造及初始化类参数
/// </summary>
public ImageController(EFCoreDbContext coreDbContext)
{
  _efCoreDbContext = coreDbContext;
}
```
### 最后接口通过上下文类取出相应的数据，如果数据不为空则连接成功