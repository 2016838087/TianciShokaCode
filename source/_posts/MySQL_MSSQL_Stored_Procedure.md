---
title: MySQL和SQL Server存储过程
date: 2020-05-11 17:40:00
categories: SQL
tags: ['技术']
comment: false
---
# 记录一下数据库存储过程
<!-- more -->
### MySQL创建带条件查询的存储过程
```bash
DELIMITER $$

CREATE
    PROCEDURE `数据库名`.`存储过程名`(IN 自定义参数 VARCHAR(200))
    BEGIN
	SELECT * FROM 表名 WHERE 字段 = 自定义参数;
    END$$

DELIMITER ;
```
### 调用并传参
```bash
CALL 存储过程名('参数值')
```
### 删除存储过程
```bash
DROP PROCEDURE 存储过程名
```
### SQL Server创建带条件查询的存储过程
```bash
create proc 存储过程名
@自定义参数 varchar(200)
as 
select * from 表名 where 字段=@自定义参数
go 
```
### 调用并传参
```bash
EXEC 存储过程名 '参数值'
```
### 删除存储过程
```bash
drop proc 存储过程名
```