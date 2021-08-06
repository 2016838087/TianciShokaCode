---
title: Appveyor持续集成Hexo
date: 2021-08-06 18:00:00
categories: Other
tags: ['技术'] 
comment: false
---
### 学习AppVeyor持续集成Hexo并配置GitHub Pages
<!-- more -->
### 创建项目，在 /projects 页面选择你的博客源码仓库
![创建项目](project.jpg)

### 选择源码仓库，添加环境变量
### 点击项目中 SETTINGS 选项卡，如果项目分支不是默认的，修改 Default branch
### 再点击 Environment 栏目，设置4个环境变量：

| name  |  value |
| ------------ | ------------ |
| STATIC_SITE_REPO | 静态网址文件存放git地址  |
| TARGET_BRANCH |  分支（默认master） |
| GIT_USER_EMAIL|   git账号|
| GIT_USER_NAME |   git名称|

### GitHub 添加Access Token
### 在https://ci.appveyor.com/tools/encrypt页面加密

### 配置CI，项目根目录添加appveyor.yml
```yaml
clone_depth: 5

environment:
    access_token:
        secure: You Access Token
    matrix:
        - nodejs_version: "12" //因为node 14版本生成页面和文件为空，这里改为12版本
install:
    - ps: Install-Product node $env:nodejs_version
    - node --version
    - npm --version
    - npm install
    - npm install hexo-cli -g
    
build_script:
    - hexo generate
    
artifacts:
    - path: public
    
on_success:
    - git config --global credential.helper store
    - ps: Add-Content "$env:USERPROFILE\.git-credentials" "https://$($env:access_token):x-oauth-basic@github.com`n"
    - git config --global user.email "%GIT_USER_EMAIL%"
    - git config --global user.name "%GIT_USER_NAME%"
    - git clone --depth 5 -q --branch=%TARGET_BRANCH% %STATIC_SITE_REPO% %TEMP%\static-site
    - cd %TEMP%\static-site
    - del * /f /q
    - for /d %%p IN (*) do rmdir "%%p" /s /q
    - SETLOCAL EnableDelayedExpansion & robocopy "%APPVEYOR_BUILD_FOLDER%\public" "%TEMP%\static-site" /e & IF !ERRORLEVEL! EQU 1 (exit 0) ELSE (IF !ERRORLEVEL! EQU 3 (exit 0) ELSE (exit 1))
    - git add -A
    - git commit -m "Update Static Site"
    - git push origin %TARGET_BRANCH%
    - appveyor AddMessage "Static Site Updated"

```
### 最后代码提交到Git，AppVeyor会自动接收到更新并build提交到指定Git仓库
### 之后更新博客以及代码只需要push即可