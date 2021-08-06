---
title: C#扩展方法
date: 2020-02-10 22:00:00
categories: DotNET #分类
tags: ['技术'] #标签
comment: false
---

## C#扩展方法
<!-- more -->
## 定义

### 1. 声明扩展方法的类必须为static类；
### 2. 扩展方法本身也必须声明为static；
### 3. 扩展方法必须包含关键字this作为第一个参数类型，并在后面跟着它所扩展的类型的名称。

## 开始操作
### 先创建一个静态类叫StringExtension
### 然后简单写一个静态方法，我这里写的是布尔，用来判断入参是否等于1
### 控制器导入扩展方法所在的类的命名空间，限制扩展方法的使用
````csharp
public static class StringExtension
{
    /// <summary>
    /// Remark：判断输入的是不是1
    /// </summary>
    /// <param name="input"></param>
    /// <returns></returns>
    public static bool IsOne(this int input)
    {
        if (input == 1)
        {
            return true;
        }
        return false;
    }
}
````
<!-- ![扩展方法](Extension.png "扩展方法") -->

### 接口调用方法，传参为1，返回true

![调用接口](code.png "调用接口")

## 总结
### - 扩展方法必须定义在静态类中，扩展方法本身也是静态方法，扩展方法也可以重载。
### - 如果扩展方法和对应的类位于不同的命名空间，使用时应引入扩展方法所在静态类的命名空间。
### - 当类本身的方法与扩展方法重名时，类本身的方法被优先调用。（建议通过命名空间的方式来限制扩展方法的使用）
### - 扩展方法不要过多使用。尤其是系统定义的类，不要随意增加扩展方法。