---
title: MVC连接MySQL教程
date: 2020-04-14 16:16:16
categories: SQL
tags: ['技术']
comment: false
---
## 记录一下MVC+EF连接MySQL数据库需要安装的插件
<!-- more -->
### 准备以下文件
1. [点击下载：mysql-connector-net-6.9.12.msi](mysql-connector-net-6.9.12.msi "下载地址")
2. [点击下载：mysql-for-visualstudio-1.2.8.msi](mysql-for-visualstudio-1.2.8.msi "下载地址")
3. MySQL Data和MySQL Data Entity（NuGet进行安装，这边装的版本都是6.9.12）

### 在model文件夹右击添加新建项，选择ADO.NET实体数据模型
!["选择ADO.NET"](model.png)
### 选择来自数据库的EF设计器
!["选择内容"](from.png)
### 新建连接，更改数据源，选择MySQL Database，连接MySQL服务器
!["连接数据库"](database.png)
### 注意：这里如果下一步时，出现闪退，则要检查MySQL Data和MySQL Data Entity版本。
### 选择所有表
!["选择需要的表"](table.png)
### 然后点击完成即可生成
!["最后查看模型"](modelentity.png)