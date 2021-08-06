---
title: .NET Core 部署IIS
date: 2020-06-10 15:30:00
categories: DotNET
tags: ['技术'] 
comment: false
---
## 记录DotNET Core部署IIS遇到的坑
<!-- more -->
### VS2019发布项目到文件夹，然后拷贝到服务器，这些正常流程结束后访问api，出现以下情况

### 第一种500错误
![500错误](500.jpg)

### 第二种502错误
![502错误](502.jpg)

### 百度了很多得到以下解决方案

[点击下载：dotnet-hosting-3.1.5-win.exe](https://download.visualstudio.microsoft.com/download/pr/7c30d3a1-f519-4167-b850-b9c49bf2aa0e/dbfa957a76a41a1e1795f59d400d4ccd/dotnet-hosting-3.1.5-win.exe "下载地址")

### 下载并且安装DotNET Core托管捆绑包安装程序

### 重新启动IIS或者重新添加网站，发布启动即可访问成功