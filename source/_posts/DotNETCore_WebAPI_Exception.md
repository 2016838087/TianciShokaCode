---
title: .NET Core WebAPI全局异常处理
date: 2020-07-03 16:00:00
categories: DotNET
tags: ['技术'] 
comment: false
---
## 今天记录一下DotNET Core WebAPI的全局异常处理
<!-- more -->
### 上代码，先创建一个类，命名就叫ExceptionFilter继承于ExceptionFilterAttribute

```csharp
public class ExceptionFilter:ExceptionFilterAttribute
{
    public override void OnException(ExceptionContext context)
    {
        if (!context.ExceptionHandled)
        {
            context.Result = new JsonResult(new
            {
                Code = "500",
                Res = new
                {
                    Data = context.Exception.Message,
                    Msg = "接口发生错误"
                }
            });
            context.ExceptionHandled = true;
        }
    }
}
```
### 然后在Startup类下面的ConfigureServices方法下面全局注册一下
```csharp
// 此方法由运行时调用，使用此方法将服务添加到容器
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    //全局配置Json序列化大小写处理
    services.AddMvc().AddJsonOptions(options =>
    {
        options.JsonSerializerOptions.PropertyNamingPolicy = null;
        options.JsonSerializerOptions.DictionaryKeyPolicy = null;
    });

    //全局注册异常类
    services.AddMvc(options =>
    {
        options.Filters.Add<ExceptionFilter>();
    });

    //解决中文被编码
    services.AddControllersWithViews().AddJsonOptions(options =>
    {
        options.JsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.All);
    });
}
```

### 为了防止返回的Json大小写不匹配，我还加了Json大小写处理，确定Json输出和后台定义的格式以及大小写一致，和返回的中文乱码情况
### 在接口报错的时候会返回后台固定的Code，用来判断接口状态
![异常分析](error.png)