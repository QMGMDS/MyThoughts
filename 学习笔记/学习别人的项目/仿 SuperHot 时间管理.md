# 资料来源

仓库连接：
[zong4/GameFragments: Some interesting game fragments!](https://github.com/zong4/GameFragments)

# 概述

因为想要复刻 SuperHot 这个游戏而找到的别人的源代码
该部分的代码实现了关键的：
1. 子弹时间效果
2. 游戏胜利时的重播放

# 子弹时间实现

所有人物的移动都含有 `Time.deltaTime`
根据玩家当前的状态去实时修改 `Time.timeScale`
玩家禁止状态时设置为 0.1f
玩家移动状态时设置为 1f
有且仅在玩家状态改变时改变该值，调用封装好的 `TimeScaleManager` 的设置时间方法

# 重播放实现

创建人物信息结构体
包含人物的 ID 、位置、方位、当前状态等信息
游戏开始时就每帧记录这些信息存储在 List 列表当中
当场景中的敌人全部死亡时，再通过协程赋值回溯这个过程
