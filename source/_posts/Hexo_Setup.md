---
title: Hexo安装与部署
date: 2020-01-12 12:00:00
categories: Hexo #分类
tags: ['技术']
comment: false
---

## 打开Git Bash，输入以下代码安装hexo
<!-- more -->
```bash
$ npm install -g hexo
```
### 安装完成在任意盘创建文件夹，例如D:\hexo
### 再右击打开Git Bash，输入
```bash
$ cd D:\hexo
$ hexo init
```
### hexo会自动下载一些文件到这个目录
### 然后继续输入
```bash
$ npm install hexo-deployer-git
```
![文件目录](file.png)

### 这个是因为等下提交代码需要此插件
### 现在生成网站并预览
```bash
$ hexo g
$ hexo s
```

### 进入浏览器输入[localhost:4000](localhost:4000 "localhost:4000")访问你的网站吧，Ctrl+C停止预览
### 在网上下一个主题然后放进hexo文件夹下面的theme文件夹里面
### 修改hexo文件夹中_config.yml文件里面的theme属性
### 将原来的landscape改为你所下载主题的文件夹名，打开Git Bash重新生成并预览
```bash
$ hexo g
$ hexo s
```
### 你会发现页面已经适应了你的主题
### 之后打开hexo目录下的_config.yml最后一段
![_config.yml](git.png)
### 绑定你的GitHub项目地址
```bash
$ hexo clean
$ hexo g
$ hexo d
```
### 以上代码分别是清除public文件夹，重新生成，和提交代码到GitHub
### 然后上GitHub你会发现存储库里面的文件都是public文件夹下面的
### 每次提交代码都只会提交此文件夹下的代码
### 本期hexo安装与部署就到这里，下期见