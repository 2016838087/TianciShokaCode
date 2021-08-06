---
title: Windows服务器搭建私人GitLab
date: 2020-10-11 21:00:00
categories: Other
tags: ['技术'] 
comment: false
---

## 记录一下Windows服务器搭建私人GitLab
<!-- more -->
## Windows服务器搭建GitLab需要安装Java环境

[Java环境下载](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html#license-lightbox "Java环境下载")
[Gitblit-1.9.1.zip](https://github.com/gitblit/gitblit/releases/download/v1.9.1/gitblit-1.9.1.zip "Gitblit-1.9.1.zip")
## 下载JavaJDK并配置环境

### 此电脑->属性->高级系统设置->环境变量->添加

```bash
变量名：JAVA_HOME
变量值：电脑上JDK安装的绝对路径
```

```bash
变量名：CLASSPATH
变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;
```

### 选择path这一列点编辑
![环境变量](path.jpg)

### 然后新增两行
![环境变量](path2.jpg)
```bash
%JAVA_HOME%\bin
%JAVA_HOME%\jre\bin
```

### 打开cmd输入java -version查看版本号，没有错误说明安装成功

### 打开data文件夹编辑找到defaults.properties配置文件

```bash
# 设置版本库的位置

git.repositoriesFolder = 你要的路径

# 设置端口号

server.httpPort = 端口号

# 设置ip地址

server.httpBindInterface = 本机ipv4

server.certificateAlias = localhost
```

### 修改installService.cmd文件
```bash
@REM arch = x86, amd64, or ia32
SET ARCH=amd64
SET CD = C:\WebFile\GitLab --(GitLab解压后的路径)
```

### 然后在命令窗口运行gitlab.cmd（切记cmd窗口不能关闭）

### 最后以管理员账号登陆，就可以自己添加存储库了，然后自行拉取提交推送