---
title: Hexo安装与部署
date: 2020-01-12 12:00:00
categories: Hexo #分类
tags: ['技术']
comment: false
---
## 首先安装node.js环境
<!-- more -->
## 打开Git Bash，输入以下代码安装hexo
```bash
$ npm install -g hexo
```
### 安装完成在任意盘创建文件夹，例如D:\hexo
### 再右击打开Git Bash，输入
```bash
$ cd D:\hexo
$ hexo init
```
### hexo会自动生成环境到这个目录
### 然后继续输入
```bash
$ hexo g
```
### 会生成一个public文件夹，这个文件夹就是博客生成的静态文件
### 可直接部署到GitPage或者服务器
```bash
$ hexo s
```

### 进入浏览器输入[localhost:4000](localhost:4000 "localhost:4000")即可访问你的网站，Ctrl+C停止预览
### 在Hexo官网下载一个主题然后放进hexo文件夹下面的theme文件夹里面
### 修改hexo文件夹中根目录_config.yml文件里面的theme属性
### 将原来的theme属性改为你所下载主题的文件夹名，打开Git Bash重新生成并预览
```bash
$ hexo g
$ hexo s
```
### 你会发现页面已经适应了你的主题
### 接下来就开启了你的魔改主题之路