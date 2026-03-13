## 3. 同事使用示例（极简 + 灵活）

### 3.1 基础注册（保持原有极简调用）

```c#
// 7:30 100%触发起床事件（默认启用、默认100%概率）
TimeEventManager.RegisterEvent(7, 30, "morning_rise", ECharacterName.Self);

// 12:00 80%概率触发午餐事件（自定义概率）
TimeEventManager.RegisterEvent(12, 0, "noon_lunch", ECharacterName.Self, true, 0.8f);

// 20:00 50%概率触发放松事件（先禁用，后续逻辑开启）
TimeEventManager.RegisterEvent(20, 0, "night_relax", ECharacterName.LinXiao, false, 0.5f);
```

### 3.2 游戏逻辑控制（启用 / 禁用）

```c#
// 解锁晚间事件后，启用该事件
TimeEventManager.SetEventEnabled("night_relax", true);

// 剧情分支：临时禁用午餐事件
TimeEventManager.SetEventEnabled("noon_lunch", false);
```

### 3.3 取消事件（精准控制）

```c#
// 取消所有ID为"morning_rise"的事件
TimeEventManager.CancelEventById("morning_rise");

// 取消12:00这个时间点的所有事件
TimeEventManager.CancelEventsAtTime(12, 0);

// 场景切换时清空所有事件
TimeEventManager.ClearAllEvents();
```

## 4. 核心特性总结（极简 + 满足所有需求）

|       特性       |                          实现方式                          |      |                                             |
| :--------------: | :--------------------------------------------------------: | :--: | :-----------------------------------------: |
| 按【时：分】触发 |               核心判断逻辑 `currH > evt.hour               |      | (currH == evt.hour && currM >= evt.minute)` |
|    是否可触发    |      结构体 `isEnabled` + `SetEventEnabled` 动态修改       |      |                                             |
|     触发概率     |   `triggerProb`（0~1）+ `Random.value <= 概率` 随机判断    |      |                                             |
|     取消事件     | `CancelEventById`（按 ID）+ `CancelEventsAtTime`（按时分） |      |                                             |
|   清空所有事件   |               `ClearAllEvents` 直接清空列表                |      |                                             |
|      零臃肿      |   静态类 + 单 List 遍历 + 无多余继承 / 单例 + 仅必要字段   |      |                                             |
|   兼容原有逻辑   |          仅需 TimeManager 加 1 行调用，无其他修改          |      |                                             |





# 游戏时间事件管理器（TimeEventManager）使用说明

## 一、工具简介

`TimeEventManager` 是一套极简的游戏时间事件触发工具，核心功能是**按游戏时间的 “时、分” 精准触发指定事件**，同时支持：

- 控制事件是否可触发（适配游戏剧情 / 解锁逻辑）；
- 自定义事件触发概率（固定时间但随机触发）；
- 精准取消 / 清空事件（适配场景切换、剧情分支）；
- 完全兼容项目原有 `TimeManager`（游戏时间缩放 / 暂停）和 `EventSystem`（事件触发逻辑）。

## 二、快速上手（3 步搞定）

### 步骤 1：确认依赖（已由开发组配置完成）

无需你手动操作，开发组已在 `TimeManager` 中添加了核心调用代码，工具会自动随游戏时间更新。

### 步骤 2：注册事件（核心操作）

在你的业务脚本（如 `GameEventController`）中，调用注册方法即可绑定 “指定时间触发的事件”：

```c#
// 示例：7:30 100%触发起床事件
TimeEventManager.RegisterEvent(7, 30, "morning_rise", ECharacterName.Self);
```

### 步骤 3：按需控制事件（可选）

根据游戏逻辑动态启用 / 禁用、取消事件：

```c#
// 解锁剧情后启用晚间事件
TimeEventManager.SetEventEnabled("night_relax", true);

// 剧情分支取消午餐事件
TimeEventManager.CancelEventById("noon_lunch");
```

## 三、核心 API 详解

所有 API 均为静态方法，无需实例化，直接通过 `TimeEventManager.XXX` 调用。

### 1. 注册事件（最常用）

```c#
/// <summary>
/// 注册按“时、分”触发的事件
/// </summary>
/// <param name="hour">触发小时（0-23）</param>
/// <param name="minute">触发分钟（0-59）</param>
/// <param name="eventId">事件ID（需与Resources/Events下的JSON配置一致）</param>
/// <param name="target">事件目标角色（ECharacterName枚举）</param>
/// <param name="isEnabled">是否默认启用（默认true，可后续修改）</param>
/// <param name="triggerProbability">触发概率（0-1，默认1=100%）</param>
TimeEventManager.RegisterEvent(int hour, int minute, string eventId, ECharacterName target, bool isEnabled = true, float triggerProbability = 1f);
```

**参数说明**：

- `eventId`：必须与 `Resources/Events` 目录下 JSON 文件中的事件 ID 一致（如 `morning_rise`、`noon_lunch`）；
- `triggerProbability`：范围 0~1（0=0% 触发，0.8=80% 触发，1=100% 触发），超出范围会自动修正为 1；
- `isEnabled`：默认 true（事件到时间会判断触发），设为 false 则暂时不触发（可后续启用）。

### 2. 动态修改事件启用状态

```c#
/// <summary>
/// 修改指定ID事件的启用状态（适配剧情解锁/禁用）
/// </summary>
/// <param name="eventId">事件ID</param>
/// <param name="isEnabled">是否启用</param>
TimeEventManager.SetEventEnabled(string eventId, bool isEnabled);
```

### 3. 取消事件（精准控制）

#### （1）按事件 ID 取消（推荐）

```c#
/// <summary>
/// 取消所有指定ID的事件（无论绑定在哪个时间点）
/// </summary>
TimeEventManager.CancelEventById(string eventId);
```

#### （2）按时、分取消

```c#
/// <summary>
/// 取消指定“时、分”的所有事件
/// </summary>
TimeEventManager.CancelEventsAtTime(int hour, int minute);
```

### 4. 清空所有事件

```c#
/// <summary>
/// 清空所有未触发的时间事件（场景切换/游戏重启时调用）
/// </summary>
TimeEventManager.ClearAllEvents();
```

## 四、常见使用场景示例

### 场景 1：基础注册（100% 触发事件）

```c#
// 7:30 100%触发起床事件（默认启用、默认100%概率）
TimeEventManager.RegisterEvent(7, 30, "morning_rise", ECharacterName.Self);

// 12:00 100%触发午餐事件
TimeEventManager.RegisterEvent(12, 0, "noon_lunch", ECharacterName.Self);
```

### 场景 2：带概率的事件注册

```c#
// 20:00 50%概率触发放松事件（随机触发）
TimeEventManager.RegisterEvent(20, 0, "night_relax", ECharacterName.LinXiao, true, 0.5f);

// 18:00 80%概率触发晚餐事件
TimeEventManager.RegisterEvent(18, 0, "evening_dinner", ECharacterName.Self, true, 0.8f);
```

### 场景 3：先禁用、后续解锁的事件

```c#
// 初始注册时禁用晚间事件（未解锁剧情）
TimeEventManager.RegisterEvent(20, 0, "night_relax", ECharacterName.LinXiao, false, 0.5f);

// 解锁剧情后，启用该事件（到20:00会判断概率触发）
public void UnlockEveningEvent()
{
    TimeEventManager.SetEventEnabled("night_relax", true);
}
```

### 场景 4：剧情分支取消事件

```c#
// 剧情选择“加班”，取消18:00的晚餐事件
public void ChooseWorkOvertime()
{
    TimeEventManager.CancelEventById("evening_dinner");
}

// 剧情选择“跳过午休”，取消12:00的所有事件
public void SkipLunch()
{
    TimeEventManager.CancelEventsAtTime(12, 0);
}
```

### 场景 5：场景切换清空事件

```c#
// 场景切换时，清空所有未触发的时间事件（避免跨场景触发）
private void OnSceneChange()
{
    TimeEventManager.ClearAllEvents();
}
```

## 五、调试与日志说明

工具会在控制台输出清晰的日志，帮助你排查问题：

|                     日志类型                      |         说明          |
| :-------------------------------------------: | :-----------------: |
|     ✅ 触发时间事件：[7:30] morning_rise（概率：100%）     |       事件触发成功        |
| ❌ 时间事件未触发：[20:00] night_relax（概率不足：60% > 50%） |       概率不足未触发       |
|        已取消 1 个 ID 为 [noon_lunch] 的时间事件        |       事件取消成功        |
|                 事件 ID 为空，注册失败                 | 注册时 eventId 为空，检查参数 |
|            触发概率需在 0~1 之间，已自动修正为 1             |   概率参数超出范围，工具自动修正   |
|                                               |                     |

## 六、注意事项

1. `eventId` 必须与 `Resources/Events` 目录下 JSON 文件中的事件 ID 完全一致（大小写敏感）；
2. 事件触发后会自动从列表中移除（仅触发 1 次），如需重复触发需重新注册；
3. 概率判断基于 Unity 的 `Random.value`（0~1），每次到时间仅判断 1 次；
4. 工具完全受 `TimeManager` 控制：游戏时间暂停时，事件不会触发；时间缩放时，触发节奏随缩放比例变化。