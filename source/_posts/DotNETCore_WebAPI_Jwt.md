---
title: 学习.NET Core Jwt授权与鉴权
date: 2021-03-03 01:00:00
categories: DotNET
tags: ['技术'] 
comment: false
---
### 大晚上写的博客，内容不是很细致，记录一个简单的过程
<!-- more -->
### 首先安装jwt所需的Nuget包
```csharp
Microsoft.AspNetCore.Authentication.JwtBearer
System.ldentityModel.Tokens.Jwt
```
![Nuget](Jwt_Nuget.png)
### 根据账户生成token的方法
```csharp
/// <summary>
/// 获取token
/// </summary>
/// <param name="username"></param>
/// <returns></returns>
public static string GetToken(string username)
{
    if (!string.IsNullOrEmpty(username))
    {
        var claims = new[]
        {
            new Claim(JwtRegisteredClaimNames.Sub,username),
            new Claim(JwtRegisteredClaimNames.Jti,Guid.NewGuid().ToString())
        };
        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("SDMC-CJAS1-SAD-DFSFA-SADHJVF-VF"));
        var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);
        var token = new JwtSecurityToken
        (
            issuer: "Admin",//签发人
            audience: "Admin",//受众
            claims: claims,
            expires: DateTime.Now.AddMinutes(3),//过期时间
            signingCredentials: creds
        );

        return new JwtSecurityTokenHandler().WriteToken(token);
    }
    else
    {
        return "账号不存在";
    }
}
```
### ConfigureServices添加JWT验证
```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,//是否验证Issuer
            ValidateAudience = true,//是否验证Audience
            ValidateLifetime = true,//是否验证失效时间
            ClockSkew = TimeSpan.FromSeconds(3),
            ValidateIssuerSigningKey = true,//是否验证SecurityKey
            ValidAudience = "Admin",//Audience
            ValidIssuer = "Admin",//Issuer，这两项和前面签发jwt的设置一致
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("SDMC-CJAS1-SAD-DFSFA-SADHJVF-VF"))//拿到SecurityKey
        };
        options.Events = new JwtBearerEvents
        {
            //此处为权限验证失败后触发的事件
            OnChallenge = context =>
            {
                //此处代码为终止.Net Core默认的返回类型和数据结果，这个很重要哦，必须
                context.HandleResponse();
                //自定义自己想要返回的数据结果         
                var payload = JsonConvert.SerializeObject(new { code = 401, res = new { msg = "Token过期，请重新登录!!!" } });
                //自定义返回的数据类型
                context.Response.ContentType = "application/json";
                //自定义返回状态码，默认为401 我这里改成 200
                context.Response.StatusCode = StatusCodes.Status401Unauthorized;
                //输出Json数据结果
                context.Response.WriteAsync(payload);
                return Task.FromResult(0);
            }
        };
    });
```
### Configure添加jwt鉴权
```csharp
//jwt鉴权
app.UseAuthentication();
//使用跨域
app.UseHttpsRedirection().UseCors(builder =>
builder.AllowAnyOrigin().AllowAnyMethod().AllowAnyHeader());
```
### 最后控制器添加[Authorize]用来鉴权
### 如果要使用Swagger进行鉴权，在ConfigureServices里面的services.AddSwaggerGen里面加上下面这段代码即可
```csharp
c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
{
    In = ParameterLocation.Header,
    Type = SecuritySchemeType.ApiKey,
    Description = "直接在下框中输入Bearer {token}（注意两者之间是一个空格）",
    Name = "Authorization",
    BearerFormat = "JWT",
    Scheme = "Bearer"
});
c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
{
    In = ParameterLocation.Header,
    Type = SecuritySchemeType.ApiKey,
    Description = "直接在下框中输入Bearer {token}（注意两者之间是一个空格）",
    Name = "Authorization",
    BearerFormat = "JWT",
    Scheme = "Bearer"
});
c.AddSecurityRequirement(new OpenApiSecurityRequirement
{
    {
        new OpenApiSecurityScheme
        {
            Reference = new OpenApiReference
            {
                Type = ReferenceType.SecurityScheme,
                Id = "Bearer"
            }
        },
        new string[] {}
    }
});
```