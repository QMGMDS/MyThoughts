

> [!summary] 概述
***static ***是一个关键字，用于声明属于类型本身而不是特定实例的成员





**实例：**

静态字段/属性
```c#
public class Counter
{
    private static int _count = 0;  // 所有实例共享
    
    public static int TotalCount 
    { 
        get { return _count; }
    }
    
    public Counter()
    {
        _count++;
    }
}

// 使用
Counter c1 = new Counter();
Counter c2 = new Counter();
Console.WriteLine(Counter.TotalCount);  // 输出 2
```






**参考：**
[5分钟讲透C++的static（5分钟实在讲不完，超时了很抱歉【手动狗头】）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV19D421K7hv/?spm_id_from=333.337.search-card.all.click&vd_source=732c9244333e52705285cb10d980dfc0)


