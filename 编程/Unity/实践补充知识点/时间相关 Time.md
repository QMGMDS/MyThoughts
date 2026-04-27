# 前言

因为想复刻 《SuperHot》 而被时间绕晕写下的
本文末尾的实验能验证时间相关 Time 的各种属性和公式

# 帧间隔时间

一个游戏从运行的那一刻开始，就会进入到一个循环往复的死循环内
游戏运行的一次循环称为一帧，但我们通常所说的游戏帧数并不是指这个死循环的执行次数，而是循环次数与时间的比值，即 **帧/每秒**，也就是一秒内循环的次数

_"我电脑玩原神能跑60帧“_
_指的就是他的电脑在原神这款游戏的死循环中能做到稳定每秒跑60次_

游戏帧数绝大多数情况下都是变动的，主要的原因有两点：

1. 外界的环境的变化会影响电脑的处理速度（比如发热）
2. 游戏每一时刻需要处理的数据量不同（比如玩家跑图和原地发呆）
   这也导致每次循环所花费的时间是不同的

上一帧可能只花费了0.01s，而下一帧却花费了0.02s
每帧花费的时间不同就会导致一个问题——
现在游戏中有一个物体要以 5m/s 的速度进行为期 1s 匀速直线运动
要实现 “匀速” 这个效果，只需要不断设置该物体在每一时刻的具体位置就可以了
比如说：
将 1s 的时间分割 10 等份，即每份代表 0.1s ，根据小学的公式 $s = vt$ 得每次移动的距离为 0.5 米，接下来每隔 0.1s 就设置一次物体位置，让它在原来的路径上继续往前 0.5 米。这样一来，只要我分割的次数足够多，那物体在该路径下的 1s 时间内的移动就足够 “流畅”，而设置物体位置的时机分别是 0.1s、0.2s ......
不难发现，设置位置的时机是有规律的，但是设置位置这个行为在游戏死循环的执行时间是没有规律的，即分割的次数是不固定的
可能在这一秒内循环了 60 次，于是我需要分割 60 次
可能在这一秒内循环了 120 次，于是我需要分割 120 次

我得需要一个能记录当前循环时间的东西，Unity 找到了这个东西，它被称为 **帧间隔时间** ，即上一帧所花费的现实时间

```C#
void EveryFrame() // 这就是游戏死循环，运行一轮算一帧
{
    // 这里指每一帧的开头位置
	// 第一帧执行到这里开始计时
	// 第二帧执行到这里结束计时，记下的时间间隔就是帧间隔时间

	Update(); // 每一帧执行一次
}
```

于是用 位置 = 帧间隔时间 \* 速度
哪怕游戏卡顿了好几秒，由于帧间隔时间的不断累加
物体也不会因为游戏卡顿而走的慢
尽管视觉效果是物体卡了一下，突然跳到目标位置的

# Unity 做了什么？

Unity 将游戏运行的死循环划分成了很多个区域，**Update** 和 **FixedUpdate** 就是其中的两个区域，两者在游戏运行的每一帧都会被执行且一帧只执行一次，但后者是否真正成功执行的条件还需要满足：
**累积的 “现实流逝时间” >= “物理时间步长”**

```C#
void EveryFrame()
{
	Update();

    // 每一帧都会尝试执行 FixedUpdate
    if(累计时间 >= Time.fixedDeltaTime / Time.timeScale)
    {
	    // 只有条件满足时，才会真正执行物理帧更新
        FixedUpdate();
    }
}
```

物理时间步长 = 固定的时间间隔 / 时间缩放Time.timeScale

这个固定的时间间隔就是指 `Edit/Project Settings/Time/Fixed Timestep` 中的数值，也就是 `Time.fixedDeltaTime` ，默认为0.02，但并不是指每隔 0.02s 成功执行一次物理帧更新，物理时间步长由 `Time.fixedDeltaTime` 和 `Time.timeScale` 共同决定

借助本文末尾的实验，重新认识这四个属性

```C#
// 记录现实世界中完成上一帧所花费的时间
Time.unscaledDeltaTime
// 记录现实世界中完成上一次物理帧更新所花费的时间
Time.fixedUnscaledDeltaTime

// 公式处理后的结果：完成上一帧所花费时间
Time.deltaTime
// 物理时间步长公式的重要变量
// 该值就是项目设置中的 Fixed Timestep
// 修改该值即修改项目设置的 Fixed Timestep
Time.fixedDeltaTime
```

# 时间缩放 Time.timeScale

首先需要明确，修改 `Time.timeScale` 只会影响 `Time.deltaTime` 和 物理时间步长

1. 影响 Time.deltaTime 的数值变化，公式如下：
   `Time.deltaTime = Time.unscaledDeltaTime * Time.timeScale`

注意：`Time.deltaTime` 的最大值为 `Edit/Project Settings/Time/Maximum Allowed Timestep` ，该值默认为 0.33

2. 影响物理时间步长，公式如下：
   `物理时间步长 = Time.fixedDeltaTime / Time.timeScale`

注意：物理帧更新时间是由 `Time.fixedDeltaTime / Time.timeScale` 计算出来的结果所决定，与 `Time.fixedUnscaledDeltaTime` 无关
`Time.fixedDeltaTime` 本身不会受 `timeScale` 影响

# 实验

将 Test 脚本挂载在场景中的物体上，运行后暂停
现实世界过个 20 秒再恢复暂停

```C#
using UnityEngine;

public class Test : MonoBehaviour
{
    private void Update()
    {
        if (Time.unscaledDeltaTime > 20)
			print("对对对对对对");
        if (Time.fixedUnscaledDeltaTime > 20)
	        print("对对对");
	    if(Time.deltaTime > 20)
			print("原神牛逼");
		if(Time.fixedDeltaTime > 20)
		    print("鸣朝牛逼");
    }
}
```

可以看到只打印了
![[Pasted image 20260420170856.png]]

验证了：

1. `Time.unscaledDeltaTime` 记录现实世界中完成上一帧所花费的时间
2. `Time.fixedUnscaledDeltaTime` 记录现实世界中完成上一次物理帧更新所花费的时间
3. `Time.deltaTime` 该值存在最大值，不会直接等于公式计算结果
