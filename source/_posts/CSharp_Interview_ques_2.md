---
title: C#理论面试题
date: 2020-03-06 13:14:20
categories: DotNET #分类
tags: ['面试题'] #标签
comment: false
---

## 这次的面试题大多为理论知识
<!-- more -->
## 1. 数组有没有length()方法，String有没有length()方法？

```csharp
string[] sz = { "1", "2", "3" };
Console.WriteLine(sz.Length);
String a = "1";
Console.WriteLine(a.Length);
```

### 很明显这两个都有length()方法

## 2. 谈谈final，finally，finalize的区别
    final ：修饰符（关键字）如果类被声明为final,就不能再派生新的子类也不能作为父类被继承
    finally ：在异常处理时提供finally块来执行操作，不管有没有异常，finally里面的代码始终会被执行
    finalize ：方法名，finalize是在Object类中定义的，所有的类都继承了它

## 3. 如何处理几十万条并发数据
    使用缓存，访问过的数据不需要二次访问数据库
    数据库使用存储过程，尽量分页
    使用多线程分批次处理
    使用WebService

## 4. 堆和栈的区别
    栈：由编译器自动分配、释放，在函数体中定义的变量通常在栈上
    堆：由程序员分配释放，用new、malloc分配内存函数得到的就是在堆上

## 5. 成员变量和成员函数前加static的作用
    它们被称为常成员变量和常成员函数，又称为类成员变量和类成员函数，分别用来反映类的状态，
    比如类成员变量可以用来统计类实例的数量，类成员函数负责这种统计的动作

## 6. C#可否直接对内存进行操作
    C#可以直接对内存进行操作。但是默认情况下，为了保持类型安全，C#不支持指针运算
    但是可以通过使用unsafe关键字，定义可使用指针的不安全代码

## 7. Const和ReadOnly？
    Const关键字用来声明编译时常量
    ReadOnly用来声明运行时常量

## 8. String和StringBuffer的区别和优缺点
    String类表示内容不可改变的字符串
    StringBuffer类表示内容可以被修改的字符串
    StringBuffer的执行速度要大于String

## 9. 产生一个int数组，长度为100，并向其中随即插入1-100，且不能重复
```csharp
List<int> lst = new List<int>();
Random r = new Random();
while (true)
{
    int temp = r.Next(1, 101);
    if (lst.Count == 100)
    {
        break;
    }
    if (!lst.Contains(temp))
    {
        lst.Add(temp);
    }
}
for (int i = 0; i < lst.Count; i++)
{
    Console.WriteLine(lst[i]);
}
Console.ReadKey();
```
## 10. 打印九九乘法表
```csharp
int i, j;
for (i = 1; i <= 9; i++)
{
    for (j = 1; j <= i; j++)
    {
        Console.Write("{0}*{1}={2,2} ", j, i, j * i);
    }
    Console.WriteLine();
}
Console.ReadKey();
```
### 先到这里吧！
![日常自嘲](bug.jpg)