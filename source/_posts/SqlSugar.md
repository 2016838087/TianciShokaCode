---
title: SqlSugar的使用日常
date: 2022-02-22 22:22:22
categories: DotNET
tags: ['技术'] 
comment: false
---
### 这两天从FreeSql转SqlSugar，记录一些日常
<!-- more -->
### 首先本地装了一个8.0.28最新版本的MySQL
### 然后项目中集成MySQL和SqlSugar
![项目结构](TianciAdminCode.png "项目结构")
### 项目分层我是在放实体的DataModel层添加引用
```csharp
<PackageReference Include="MySql.Data" Version="8.0.28" />
<PackageReference Include="SqlSugarCore" Version="5.0.5.4" />
```
### 然后处理代码逻辑是在DataService层，也就是接口实现，所以在这层写上下文类
```csharp
using SqlSugar;

namespace DataService
{
    /// <summary>
    /// 数据库上下文类
    /// </summary>
    public class DbContext
    {
        public static string ConnectionString { get; set; }

        public static SqlSugarClient GetInstance()
        {
            var db = new SqlSugarClient(new ConnectionConfig
            {
                ConnectionString = ConnectionString,
                DbType = DbType.MySql,
                IsAutoCloseConnection = true,//自动释放数据务，如果存在事务，在事务结束后释放
                InitKeyType = InitKeyType.Attribute//从实体特性中读取主键自增列信息
            });
            return db;
        }
    }
}
```
### 在Startup中ConfigureServices里注入
```csharp
//连接MySQL数据库，添加数据库上下文
DataService.DbContext.ConnectionString = Configuration.GetConnectionString("MySQLConnection");
```
### appsettings.json添加本地数据库连接字符串
```json
"ConnectionStrings": {
    "MySQLConnection": "server=127.0.0.1;uid=root;pwd=123456;port=3306;database=world;SslMode=None"
  }
```
### 今天本打算写一个树形结构的菜单层级处理，看到SqlSugar官方文档有自带的方法ToTree
### 简单使用一下，以下是数据库结构
![表结构](Table.png "表结构")
### 实体类数据代码
```csharp
using System.Collections.Generic;
using SqlSugar;

namespace DataModel.Table
{
    /// <summary>
    /// 菜单表
    /// </summary>
    [SugarTable("menuinfo")]
    public partial class MenuInfo
    {
        /// <summary>
        /// 主键
        /// </summary>
        [SugarColumn(IsPrimaryKey = true, IsIdentity = true)]
        public int Id { get; set; }

        /// <summary>
        /// 菜单名称
        /// </summary>           
        public string MenuName { get; set; }

        /// <summary>
        /// 父级Id
        /// </summary>           
        public int? ParentId { get; set; }

        /// <summary>
        /// 不验证数据库，做树形结构使用
        /// </summary>
        [SqlSugar.SugarColumn(IsIgnore = true)]
        public List<MenuInfo> Child { get; set; }
    }
}
```
### 示例代码
```csharp
using (var db = DbContext.GetInstance())
{
    //首次连接数据库获取数据不计算在耗时之内
    db.Queryable<MenuInfo>().ToJson();
    //SqlSugar自带树形结构
    Stopwatch sw = new Stopwatch();
    sw.Start();
    var tree = db.Queryable<MenuInfo>().ToTree(s => s.Child, s => s.ParentId, 0);
    string json = JsonConvert.SerializeObject(tree);
    sw.Stop();
    //Linq自带Foreach实现递归遍历树形结构
    Stopwatch sw2 = new Stopwatch();
    sw2.Start();
    var list = db.Queryable<MenuInfo>().ToList();
    list.ForEach(s => s.Child = list.Where(x => x.ParentId == s.Id).ToList());
    var tree2 = list.Count > 0 ? list.Where(s => s.ParentId == list.OrderBy(s => s.ParentId).ToList().FirstOrDefault().ParentId).ToList() : null;
    string json2 = JsonConvert.SerializeObject(tree2);
    sw2.Stop();
    _logger.LogInformation("SqlSugar耗时：{0}ms，数据：{1}", sw.ElapsedTicks / (decimal)Stopwatch.Frequency * 1000, json);//SqlSugar
    _logger.LogInformation("Linq Foreach耗时：{0}ms，数据：{1}", sw2.ElapsedTicks / (decimal)Stopwatch.Frequency * 1000, json2);//Linq Foreach
    //SqlSugar的性能没有Linq Foreach高
}
```
### 运行五次并打印日志
```log
SqlSugar耗时：14.0168000ms
Linq Foreach耗时：7.8033000ms
SqlSugar耗时：1.6285000ms
Linq Foreach耗时：1.0115000ms
SqlSugar耗时：0.8813000ms
Linq Foreach耗时：0.7391000ms
SqlSugar耗时：4.4206000ms
Linq Foreach耗时：4.1635000ms
SqlSugar耗时：1.015000ms
Linq Foreach耗时：0.8329000ms
```
### 结论：SqlSugar这个ToTree方法处理树形结构很方便，但是实际性能Foreach要好一丢丢