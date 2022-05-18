---
title: Office365 E5账号遇到的问题
date: 2022-05-18 13:00:00
categories: Other
tags: ['技术'] 
comment: false
---
### 这两天为了使用Office365注册了一个E5开发人员账号
<!-- more -->
### 但最近微软强制使用手机令牌Authenticator登陆的时候进行了绑定
### 每一次登录都得拿出手机确认登录，就很麻烦
### 所以我在Azure Active Directory设置里面关掉了安全默认值，打开网址[Azure Active Directory](https://portal.azure.com/#home "Azure Active Directory")具体操作如下
![管理Azure Active Directory](管理.png)
![属性](属性.png)
![管理安全默认值](管理安全默认值.png)
![启用安全默认值](启用安全默认值.png)
### 并且在 [Office设置页面](https://mysignins.microsoft.com/security-info "Office设置页面") 的安全信息页面把Microsoft Authenticator和电子邮件登陆方法都删除了
![安全信息](安全信息.png)
### 这两步做完就导致E5账号无法登录了，手机令牌没了但还是提示需要手机令牌确认才可以登录
### 借用社区的相同问题作为参考[Microsoft 365 开发人员订阅唯一管理员账户丢失 Authenticator 验证码](https://answers.microsoft.com/zh-hans/msoffice/forum/all/microsoft-365/4c319bcc-d110-4702-a175-d766fc87398b "Microsoft 365 开发人员订阅唯一管理员账户丢失 Authenticator 验证码")和[无法批准Authenticator管理员账户登陆请求](https://answers.microsoft.com/zh-hans/msoffice/forum/all/%E6%97%A0%E6%B3%95%E6%89%B9%E5%87%86authenticator/cff1298f-617b-4d74-b760-f59832b56d05 "无法批准Authenticator管理员账户登陆请求")
### 解决办法只能求助官方了，具体操作步骤如下

### 进入[联系中国支持人员](https://docs.microsoft.com/zh-cn/microsoft-365/admin/support/china-prc?view=o365-worldwide "联系中国支持人员")网址，拨打电话800 988 0365
### 向客服提交相应的问题，问题描述可以参考上方的社区问题，客服会把问题转成工单，然后会有技术支持人员联系，需要电脑远程重现问题所在，当然这一步如果遇到的是与我相同的问题那么是不可能解决的，技术支持会提交到数据安全部门，最后数据安全部门会清除你账号的所有验证方式，再次登录进行绑定就可以开始你的表演了