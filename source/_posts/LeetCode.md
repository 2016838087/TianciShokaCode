---
title: LeetCode部分算法题（持续更新）
date: 2022-05-18 13:35:00
categories: Other
tags: ['技术'] 
comment: false
sticky: true #置顶文章
---
### 刷过LeetCode才发现自己的基础是这么的烂
<!-- more -->
### 两数之和
```csharp
/// <summary>
/// 两数之和
/// </summary>
/// <param name="nums"></param>
/// <param name="target"></param>
/// <returns></returns>
public static int[] TwoSum(int[] nums, int target)
{
    for (int i = 0; i < nums.Length; i++)
    {
        for (int j = 0; j < nums.Length; j++)
        {
            if (i != j)
            {
                if (nums[i] + nums[j] == target)
                {
                    return new int[] { i, j };
                }
            }
        }
    }
    return null;
}
```
### 两数相加（抄来的并且已经提交）
```csharp
public class ListNode
{
    public int val;
    public ListNode next;
    public ListNode(int val = 0, ListNode next = null)
    {
        this.val = val;
        this.next = next;
    }
}

/// <summary>
/// 两数相加（抄来的并且已经提交）
/// </summary>
/// <param name="l1"></param>
/// <param name="l2"></param>
/// <returns></returns>
public static ListNode AddTwoNumbers(ListNode l1, ListNode l2)
{
    ListNode dummyHead = new ListNode(-1);
    ListNode pre = dummyHead;
    int t = 0;
    while (l1 != null || l2 != null || t != 0)
    {
        if (l1 != null)
        {
            t += l1.val;
            l1 = l1.next;
        }
        if (l2 != null)
        {
            t += l2.val;
            l2 = l2.next;
        }
        pre.next = new ListNode(t % 10);
        pre = pre.next;
        t /= 10;
    }

    return dummyHead.next;
}
```
### 回文数
```csharp
/// <summary>
/// 回文数
/// </summary>
/// <param name="x"></param>
/// <returns></returns>
public static bool IsPalindrome(int x)
{
    if (x < 0)
    {
        return false;
    }
    string num = x.ToString();
    List<string> list = new List<string>();
    for (int i = 0; i < num.Length; i++)
    {
        list.Add(num[i].ToString());
    }
    var arr = list.ToArray();
    Array.Reverse(arr);
    string num2 = string.Empty;
    for (int i = 0; i < arr.Length; i++)
    {
        num2 += arr[i].ToString();
    }
    return x == Convert.ToInt32(num2);
    //别人的方法
    //if (x < 0)
    //    return false;
    //int rem = 0, y = 0;
    //int quo = x;
    //while (quo != 0)
    //{
    //    rem = quo % 10;
    //    y = y * 10 + rem;
    //    quo = quo / 10;
    //}
    //return y == x;
}
```
### 有效的括号
```csharp
/// <summary>
/// 有效的括号
/// </summary>
/// <param name="s"></param>
/// <returns></returns>
public static bool IsValid(string s)
{
    while (s.Contains("()") || s.Contains("{}") || s.Contains("[]"))
    {
        s = s.Replace("()", "").Replace("{}", "").Replace("[]", "");
    }

    return s.Length == 0;
}
```
### 各位相加
```csharp
/// <summary>
/// 各位相加
/// </summary>
/// <param name="num"></param>
/// <returns></returns>
public static int AddDigits(int num)
{
    string number = num.ToString();
    int x = 0;
    while (Convert.ToInt32(number) >= 10)
    {
       for (int i = 0; i < number.Length; i++)
       {
           x += Convert.ToInt32(number[i].ToString());
       }
       if (x >= 10)
       {
           number = x.ToString();
           x = 0;
           continue;
       }
       return x;
    }
    return num;
    //别人的方法（属实是佩服这种思路和逻辑）
    //return (num - 1) % 9 + 1;
}
```
### 寻找两个正序数组的中位数
```csharp
/// <summary>
/// 寻找两个正序数组的中位数
/// </summary>
/// <param name="nums1"></param>
/// <param name="nums2"></param>
/// <returns></returns>
public static double FindMedianSortedArrays(int[] nums1, int[] nums2)
{
    List<int> list = new List<int>();
    list.AddRange(nums1);
    list.AddRange(nums2);
    list.Sort();
    int count = 0;
    if (list.Count % 2 == 0)
    {
        count = list.Count / 2;
        double num = list[count - 1] + list[count];
        return Convert.ToDouble(num / 2);
    }
    list.Add(list[list.Count - 1]);
    list.Sort();
    count = list.Count / 2;
    return list[count - 1];
}
```
### 快乐数
```csharp
/// <summary>
/// 快乐数
/// </summary>
/// <param name="n"></param>
/// <returns></returns>
public static bool IsHappy(int n)
{
    if (n == 1)
    {
        return true;
    }
    List<int> list = new List<int>();//存一个数
    while (n > 1)
    {
        int num2 = 0;
        string num = n.ToString();
        for (int i = 0; i < num.Length; i++)
        {
            num2 += Convert.ToInt32(num[i].ToString()) * Convert.ToInt32(num[i].ToString());
        }
        list.Add(num2);
        if (list.Where(s => s.Equals(num2)).ToList().Count > 1)
        {
            //如果多次循环则返回false
            return false;
        }
        if (num2 == 1)
        {
            return true;
        }
        n = num2;
    }
    return false;
}
```
### 加一
```csharp
/// <summary>
/// 加一
/// </summary>
/// <param name="digits"></param>
/// <returns></returns>
public static int[] PlusOne(int[] digits)
{
    List<int> list = new List<int>();
    list.AddRange(digits);
    for (int i = digits.Length - 1; i >= 0; i--)
    {
        if (digits[i] != 9)
        {
            //第一次循环最后一位数不为9直接+1返回结果
            digits[i]++;
            return digits;
        }
        //否则当前下标数进位满10为0
        digits[i] = 0;
    }
    //循环走完说明全是9，长度+1
    int[] arr = new int[digits.Length + 1];
    arr[0] = 1;
    return arr;
}
```
### 移动零
```csharp
/// <summary>
/// 移动零
/// </summary>
/// <param name="nums"></param>
public static void MoveZeroes(int[] nums)
{
    int length = nums.Length;
    int i, j = 0;
    for (i = 0; i < length; i++)
    {
        if (nums[i] != 0)
        {
            nums[j] = nums[i];
            j++;
        }
    }
    while (j < length)
    {
        nums[j++] = 0;
    }
}
```
### 将找到的值乘以2
```csharp
/// <summary>
/// 将找到的值乘以2
/// </summary>
/// <param name="nums"></param>
/// <param name="original"></param>
/// <returns></returns>
public static int FindFinalValue(int[] nums, int original)
{
    for (int i = 0; i < nums.Length; i++)
    {
        if (nums[i] == original)
        {
            return FindFinalValue(nums, original *= 2);
        }
    }
    return original;
}
```
### 排列硬币
```csharp
/// <summary>
/// 排列硬币
/// </summary>
/// <param name="n"></param>
/// <returns></returns>
public static int ArrangeCoins(int n)
{
    if (n == 1)
    {
        return 1;
    }
    int count = 0;
    int m = n;
    for (int i = 1; i < m; i++)
    {
        if (n - i >= 0)
        {
            n -= i;
            count++;
            continue;
        }
        return count;
    }
    return count;
}
```
### 两整数相加（这题有点侮辱智商）
```csharp
/// <summary>
/// 两整数相加
/// </summary>
/// <param name="num1"></param>
/// <param name="num2"></param>
/// <returns></returns>
public static int Sum(int num1, int num2)
{
    return num1 + num2;
}
```
### 只出现一次的数字（抄来的未提交）
```csharp
/// <summary>
/// 只出现一次的数字（抄来的未提交）
/// </summary>
/// <param name="nums"></param>
/// <returns></returns>
public static int SingleNumber(int[] nums)
{
   return nums.Aggregate((a, b) => a ^ b);
}
```
### 只出现一次的数字（暴力写法照样能写出来）
```csharp
/// <summary>
/// 只出现一次的数字
/// </summary>
/// <param name="nums"></param>
/// <returns></returns>
public static int SingleNumber(int[] nums)
{
    List<int> list = new List<int>();
    list.AddRange(nums);
    for (int i = 0; i < list.Count; i++)
    {
        if (list.Where(s => s == list[i]).ToList().Count == 1)
        {
            return list[i];
        }
    }
    return 0;
}
```
###  2的幂
```csharp
/// <summary>
///  2的幂
/// </summary>
/// <param name="n"></param>
/// <returns></returns>
public static bool IsPowerOfTwo(int n)
{
    if (n == 1)
    {
        return true;
    }
    if (n % 2 != 0 || n <= 0)
    {
        return false;
    }
    return IsPowerOfTwo(n / 2);
}
```
###  3的幂
```csharp
/// <summary>
///  3的幂
/// </summary>
/// <param name="n"></param>
/// <returns></returns>
public static bool IsPowerOfThree(int n)
{
    if (n == 1)
    {
        return true;
    }
    if (n % 3 != 0 || n <= 0)
    {
        return false;
    }
    return IsPowerOfThree(n / 3);
}
```
###  4的幂
```csharp
/// <summary>
///  4的幂
/// </summary>
/// <param name="n"></param>
/// <returns></returns>
public static bool IsPowerOfFour(int n)
{
    if (n == 1)
    {
        return true;
    }
    if (n % 4 != 0 || n < 0)
    {
        return false;
    }
    return IsPowerOfFour(n / 4);
}
```
### 第三大的数
```csharp
/// <summary>
/// 第三大的数
/// </summary>
/// <param name="n"></param>
/// <returns></returns>
public static int ThirdMax(int[] nums)
{
    Array.Sort(nums);
    Array.Reverse(nums);
    nums = nums.Distinct().ToArray();
    if (nums.Length >= 3)
    {
        return nums[2];
    }
    return nums[0];
}
```
### 斐波那契数
```csharp
/// <summary>
/// 斐波那契数
/// </summary>
/// <param name="n"></param>
/// <returns></returns>
public static int Fib(int n)
{
    if(n == 0)
    {
        return 0;
    }
    if (n == 1)
    {
        return 1;
    }
    return Fib(n - 1) + Fib(n - 2);
}
```
### 三个数的最大乘积
```csharp
/// <summary>
/// 三个数的最大乘积
/// </summary>
/// <param name="nums"></param>
/// <returns></returns>
public static int MaximumProduct(int[] nums)
{
    Array.Sort(nums);
    return nums[0] * nums[1] * nums[nums.Length - 1] > nums[nums.Length - 1] * nums[nums.Length - 2] * nums[nums.Length - 3] ? nums[0] * nums[1] * nums[nums.Length - 1] : nums[nums.Length - 1] * nums[nums.Length - 2] * nums[nums.Length - 3];
}
```
###  X的平方根（emmm没想通直接用Math了）
```csharp
/// <summary>
///  X的平方根
/// </summary>
/// <param name="x"></param>
/// <returns></returns>
public static int MySqrt(int x)
{
    return (int)Math.Sqrt(x);
}
```
### 二分查找（用了最简单的方法但也通过了，但是要求是二分查找所以重写了）
```csharp
/// <summary>
/// 二分查找
/// </summary>
/// <param name="nums"></param>
/// <param name="target"></param>
/// <returns></returns>
public static int Search(int[] nums, int target)
{
    //Array.Sort(nums);
    //for (int i = 0; i < nums.Length; i++)
    //{
    //    if (nums[i] == target)
    //    {
    //        return i;
    //    }
    //}
    //return -1;
    //二分查找
    Array.Sort(nums);
    int start = 0;
    int end = nums.Length - 1;
    while (start < end)
    {
        int mid = (start + end) / 2;
        if (nums[mid] == target)
        {
            return mid;
        }
        if (nums[mid] > target)
        {
            end = mid - 1;
        }
        if (nums[mid] < target)
        {
            start = mid + 1;
        }
    }
    return -1;
}
```
### 整数反转（自己写的不符合要求）
```csharp
/// <summary>
/// 整数反转
/// </summary>
/// <param name="x"></param>
/// <returns></returns>
public static int Reverse(int x)
{
    string num = string.Empty;
    if (x < 0)
    {
        num = (x * -1).ToString();//变正数
    }
    else
    {
        num = (x * -1).ToString();
    }
    string nums = string.Empty;
    for (int i = num.Length-1; i >= 0; i--)
    {
        //最后一位是0的话就跳出
        if (num[num.Length - 1] == 0)
        {
            continue;
        }
        nums += num[i].ToString();
    }
    if (x < 0)
    {
        return Convert.ToInt32(nums) * -1;//还原符号
    }
    return Convert.ToInt32(nums);
}
```
### 整数反转（借鉴了别人Java的解题思路）
```csharp
/// <summary>
/// 整数反转
/// </summary>
/// <param name="x"></param>
/// <returns></returns>
public static int Reverse(int x)
{
    long n = 0;
    while (x != 0)
    {
        n = n * 10 + x % 10;
        x = x / 10;
    }
    return (int)n == n ? (int)n : 0;
}
```
### 检查两个字符串数组是否相等
```csharp
/// <summary>
/// 检查两个字符串数组是否相等
/// </summary>
/// <param name="word1"></param>
/// <param name="word2"></param>
/// <returns></returns>
public static bool ArrayStringsAreEqual(string[] word1, string[] word2)
{
    string txt1 = string.Empty;
    string txt2 = string.Empty;
    for (int i = 0; i < word1.Length; i++)
    {
       txt1 += word1[i].ToString();
    }
    for (int i = 0; i < word2.Length; i++)
    {
       txt2 += word2[i].ToString();
    }
    return txt1 == txt2;
    //别人的一句代码解决（性能好过一丢丢）
    //return string.Join("", word1).Equals(string.Join("", word2));
}
```
### 合并两个有序数组（性能杠杠滴就是写完就看不懂了）
```csharp
/// <summary>
/// 合并两个有序数组
/// </summary>
/// <param name="nums1"></param>
/// <param name="m"></param>
/// <param name="nums2"></param>
/// <param name="n"></param>
public static void Merge(int[] nums1, int m, int[] nums2, int n)
{
    if (m == 0)
    {
        nums1 = new int[n];
        for (int i = 0; i < nums2.Length; i++)
        {
            if (i == n)
            {
                break;
            }
            if (nums2[i] != 0)
            {
                nums1[i] = nums2[i];
            }
        }
    }
    if (n == 0)
    {
        int[] num3 = nums1;
        for (int i = 0; i < num3.Length; i++)
        {
            if (i == m)
            {
                break;
            }
            if (num3[i] != 0)
            {
                nums1[i] = num3[i];
            }
        }
    }
    else
    {
        Array.Sort(nums1);
        Array.Sort(nums2);
        Array.Reverse(nums2);
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < nums1.Length; j++)
            {
                if (nums1[j] == 0)
                {
                    nums1[j] = nums2[i];
                    break;
                }
            }
        }
        Array.Sort(nums1);
    }
}
```
### 有序数组中出现次数超过25%的元素
```csharp
/// <summary>
/// 有序数组中出现次数超过25%的元素
/// </summary>
/// <param name="arr"></param>
/// <returns></returns>
public static int FindSpecialInteger(int[] arr)
{
    int count = arr.Length / 4;
    List<int>list=arr.ToList();
    for (int i = 0; i < list.Count; i++)
    {
        if (list.Where(s => s == list[i]).ToList().Count > count)
        {
            return list[i];
        }
    }
    return 0;
}
```
### 字符串压缩
```csharp
/// <summary>
/// 字符串压缩
/// </summary>
/// <param name="S"></param>
/// <returns></returns>
public static string CompressString(string S)
{
    S += "?";
    string key = string.Empty;
    string text = string.Empty;
    int count = 0;
    for (int i = 0; i < S.Length; i++)
    {
        if (i == 0)
        {
            key = S[i].ToString();
        }
        if (key == S[i].ToString())
        {
            count++;
        }
        else
        {
            text += S[i - 1].ToString() + count.ToString();
            key = S[i].ToString();
            count = 1;
        }
    }
    return text.Length <= S.Length - 1 ? text: S.Replace("?", "") ;
}
```
###  将字符串拆分为若干长度为 k 的组（头发掉光写法）
```csharp
/// <summary>
///  将字符串拆分为若干长度为 k 的组（头发掉光写法）
/// </summary>
/// <param name="s"></param>
/// <param name="k"></param>
/// <param name="fill"></param>
/// <returns></returns>
public static string[] DivideString(string s, int k, char fill)
{
    int mold = s.Length % k;//余数
    int remainder = (s.Length - mold) / k;//获取需要几个item
    int count = 0;//item数量

    if (remainder > 0)
    {
        count += remainder;
    }
    if (mold > 0)
    {
        count++;
    }
    if (count > 0)
    {
        List<string> list = new List<string>();
        int sum = 0;
        for (int i = 0; i < count; i++)
        {
            string text = string.Empty;
            int kcount = 0;//获取数量
            for (int j = 0; j < s.Length; j++)
            {
                if (i == count - 1 && mold > 0)
                {
                    text += s[sum].ToString();
                    kcount++;
                    //最后一次遍历
                    if (sum == s.Length - 1)
                    {
                        int x = k - kcount;
                        for (int w = 0; w < x; w++)
                        {
                            text += fill.ToString();
                        }
                        list.Add(text);
                        return list.ToArray();
                    }
                    sum++;
                    continue;
                }
                text += s[sum].ToString();
                kcount++;
                sum++;
                if (kcount == k)
                {
                    list.Add(text);
                    break;
                }
            }
        }
        return list.ToArray();
    }
    return null;    
}
```
###  将字符串拆分为若干长度为 k 的组（先补齐后分割）
```csharp
/// <summary>
///  将字符串拆分为若干长度为 k 的组（先补齐后分割）
/// </summary>
/// <param name="s"></param>
/// <param name="k"></param>
/// <param name="fill"></param>
/// <returns></returns>
public static string[] DivideString(string s, int k, char fill)
{
    //不如原来的性能好
    int mold = s.Length % k;//余数
    int remainder = (s.Length - mold) / k;//获取需要几个item
    int count = 0;//item数量

    if (remainder > 0)
    {
       count += remainder;
    }
    if (mold > 0)
    {
       count++;
    }
    if (count > 0)
    {
       int kcount = count * k - s.Length;//补齐数
       for (int i = 0; i < kcount; i++)
       {
           s += fill.ToString();
       }
       List<string> list = new List<string>();
       int sum = 0;
       for (int i = 0; i < count; i++)
       {
           string text = string.Empty;
           kcount = 0;//获取数量
           for (int j = 0; j < s.Length; j++)
           {                        
               text += s[sum].ToString();
               kcount++;
               sum++;
               if (kcount == k)
               {
                   list.Add(text);
                   break;
               }
           }
       }
       return list.ToArray();
    }
    return null;
}
```