---
title: C# Reamrk特性
date: 2020-04-03 11:11:11
categories: DotNET #分类
tags: ['技术'] #标签
comment: false
---

## Remark特性的应用场景
<!-- more -->
### 场景一：用户状态的枚举值，定义的是英文的字段，需要输出中文含义。枚举定义如下：
````csharp
public enum UserState
{
    /// <summary>
    /// 正常
    /// </summary>
    Normal = 0,

    /// <summary>
    /// 冻结
    /// </summary>
    Frozen = 1,

    /// <summary>
    /// 删除
    /// </summary>
    Deleted = 2
}
````

###  普通做法：根据枚举值进行判断，然后输出中文含义：
```csharp
UserState userState = UserState.Normal;
switch (userState)
{
    case UserState.Deleted:
    Console.WriteLine("删除");
    break;

    case UserState.Normal:
    Console.WriteLine("正常");
    break;

    case UserState.Frozen:
    Console.WriteLine("冻结");
    break;
}
```
###  这种写法违反开不原则，不利于以后的扩展，下面使用特性实现

### 先定义Remark特性：
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace TianciAttribute
{
    /// <summary>
    /// 特性
    /// </summary>
    public class RemarkAttribute: Attribute
    {
        private string _Remark = null;
        /// <summary>
        /// 有参构造函数
        /// </summary>
        /// <param name="remark"></param>
        public RemarkAttribute(string remark)
        {
            this._Remark = remark;
        }

        public string GetRemark()
        {
            return _Remark;
        }
    }
}
```
###  UserState枚举修改如下：
```csharp
public enum UserState
{
    /// <summary>
    /// 正常
    /// </summary>
    [Remark("正常")]
    Normal = 0,

    /// <summary>
    /// 冻结
    /// </summary>
    [Remark("冻结")]
    Frozen = 1,

    /// <summary>
    /// 删除
    /// </summary>
    [Remark("删除")]
    Deleted = 2
}
```

### 写一个扩展方法，对Enum类型进行扩展：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;

namespace TianciAttribute
{
    public static class RemarkExtension
    {
        /// <summary>
        /// Enum的扩展方法，静态类，静态方法，第一个参数前面添加this
        /// </summary>
        /// <param name="value"></param>
        /// <returns></returns>
        public static string GetRemark(this Enum value)
        {
            //获取类型
            Type type = value.GetType();

            //获取字段
            FieldInfo fieId = type.GetField(value.ToString());

            //判断字段上面是否定义了ReamrkAttribute特性
            if (fieId.IsDefined(typeof(RemarkAttribute)))
            {
                //创建实例
                RemarkAttribute attribute = (RemarkAttribute)fieId.GetCustomAttribute(typeof(RemarkAttribute));

                return attribute.GetRemark();
            }
            else
            {
                return value.ToString();
            }
        }
    }
}

```

### 最后Main()方法里面调用
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace TianciAttribute
{
    class Program
    {
        static void Main(string[] args)
        {
            UserState userState = UserState.Normal;
            Console.WriteLine(userState.GetRemark());
            Console.ReadKey();
        }
    }
}
```

### 输出结果
![输出结果](Normal.png)