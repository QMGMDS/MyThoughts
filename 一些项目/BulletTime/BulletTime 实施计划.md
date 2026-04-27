# BulletTime 实施计划

复刻 SuperHot 核心玩法：子弹时间 + 胜利回放。

---

## 技术选型

| 决策点 | 方案 |
|---|---|
| 胜利回放 | 位置记录：每帧记录关键物体 Transform，回放时直接设置位置 |
| 敌人 AI | 基础复杂度：远程敌人会瞄准射击，近战敌人会靠近攻击 |
| 玩家 HP | 一枪死：被任何攻击命中即死亡（与原版一致） |

---

## 已完成功能 ✅

| 系统 | 文件 | 备注 |
|---|---|---|
| 子弹时间核心 | `Time/TimeMgr.cs`, `Time/TimeControlled.cs`, `Time/TimeInput.cs` | WASD 控制 0.1x ↔ 1x |
| 玩家控制器 | `Base/PlayerInputController.cs` | 移动、视角、拾取(E)、丢弃(Q)、攻击(左键) |
| 武器系统 | `Base/Weapon/BaseWeapon.cs`, `Pistol.cs`, `Bullet.cs`, `WeaponData.cs` | 抽象基类 + 手枪 + 弹药 |
| 对象池 | `Tool/PoolDataMgr.cs` | 统一管理子弹/特效创建与回收 |
| 资源加载 | `Tool/ResLoadAsync.cs` | Resources.LoadAsync 回调封装 |
| 准星 UI | `Base/MouseTipController.cs` | 根据射线命中切换光标 |
| 特效回收 | `Base/AutoRecycle.cs` | 2 秒自动归池 |
| 单例框架 | `Tool/AutoMonoSingleton.cs` | 自动创建 DontDestroyOnLoad |
| 拾取接口 | `Interface/IPickUped.cs` | 可拾取物品契约 |

---

## 待完成功能 ❌

### Phase 1 — 基础设施修复

- [x] **P1-1** 修复编辑器 Bug — 移除 `PlayerInputController.cs:1` 的 `using UnityEditor.Experimental.GraphView`
  - 文件：`Scripts/Base/PlayerInputController.cs`（修改）
- [x] **P1-2** 创建 GameMgr 状态管理器
  - 状态枚举：`Playing / Win / Lose`
  - 单例，自动创建，`DontDestroyOnLoad`
  - 提供 `Win()` / `Lose()` 入口方法，锁定/解锁玩家输入
  - 文件：`Scripts/Time/GameMgr.cs`（新建）
- [x] **P1-3** 注册场景到 Build Settings
  - `MainMenu`（index 0）、`Level_01`（index 1）添加到 `EditorBuildSettings`

### Phase 2 — 敌人与伤害系统

- [ ] **P2-1** 给 `BaseEnemy` 添加伤害与死亡逻辑
  - 新增 `_hp` 字段（默认 1）
  - 新增 `TakeDamage(float damage)` → 扣血 → 血量为 0 调用 `Death()`
  - `Death()` 播放溅血特效 → 禁用对象
  - 文件：`Scripts/Base/Enemy/BaseEnemy.cs`（修改）
- [ ] **P2-2** `Bullet` 命中敌人时调用 `TakeDamage`
  - `OnTriggerEnter` 中已有敌人生成溅血逻辑，需补充 `TakeDamage()` 调用
  - 文件：`Scripts/Base/Weapon/Bullet.cs`（修改）
- [ ] **P2-3** 玩家添加一枪死机制
  - `PlayerInputController` 新增 `_playerHp`（默认 1）
  - 新增 `TakeDamage()` → 扣血 → 0 时调用 `GameMgr.Instance.Lose()`
  - 新增 `Death()` → 玩家死亡表现（先简单禁用输入+日志）
  - 文件：`Scripts/Base/PlayerInputController.cs`（修改）
- [ ] **P2-4** 创建 `EnemyRanged` 远程敌人
  - 检测玩家位置（`Transform _player` 引用）
  - 每隔 `_shootInterval` 秒向玩家发射子弹（从对象池取）
  - 用 LineRenderer 表现弹道轨迹
  - 发射方向在玩家位置基础上加随机偏移模拟准度
  - 文件：`Scripts/Base/Enemy/EnemyRanged.cs`（新建）
- [ ] **P2-5** 实现 `EnemyMelee` 近战敌人
  - `Update` 中向玩家移动（`DeltaTime` 驱动速度）
  - 进入 `_attackRange` 范围后执行攻击
  - 攻击调用玩家 `PlayerInputController.TakeDamage()`
  - 攻击间隔 `_attackInterval` 防止连续伤害
  - 文件：`Scripts/Base/Enemy/EnemyMelee.cs`（修改）

### Phase 3 — 关卡管理

- [ ] **P3-1** 创建 `EnemySpawner` 敌人管理器
  - 维护场景内所有敌人列表 `List<BaseEnemy>`
  - 追踪存活敌人数量
  - 全部死亡 → 调用 `GameMgr.Instance.Win()`
  - 文件：`Scripts/Base/EnemySpawner.cs`（新建）
- [ ] **P3-2** GameMgr 完善胜利/失败逻辑
  - `Win()`：锁定玩家输入，禁用 TimeInput → 立即启动回放
  - `Lose()`：锁定玩家输入 → 显示失败信息 → 等待重新开始
  - 文件：`Scripts/Time/GameMgr.cs`（修改）

### Phase 4 — 胜利回放

- [ ] **P4-1** 创建 `ReplayRecorder` 回放记录器
  - 每帧记录关键物体的 position + rotation
  - 记录对象：玩家 Camera、所有敌人、所有活跃子弹
  - 通过 `GameMgr.Instance.Win()` 触发停止记录
  - 文件：`Scripts/Time/ReplayRecorder.cs`（新建）
- [ ] **P4-2** 创建 `ReplayPlayer` 回放播放器
  - 以 1x 速度逐帧回放记录的位置/旋转数据
  - 支持自由视角（WASD + 鼠标在回放中移动摄像机）
  - 回放结束后返回主菜单
  - 文件：`Scripts/Time/ReplayPlayer.cs`（新建）
- [ ] **P4-3** 回放 → 主菜单流程
  - 回放结束或按 Esc → `SceneManager.LoadScene("MainMenu")`
  - 文件：`Scripts/Time/ReplayPlayer.cs`

---

## 文件变更汇总

### 新建（5 个）
| 文件 | Phase | 说明 |
|---|---|---|
| `Scripts/Time/GameMgr.cs` | P1-2 | 游戏状态管理器 |
| `Scripts/Base/Enemy/EnemyRanged.cs` | P2-4 | 远程敌人 |
| `Scripts/Base/EnemySpawner.cs` | P3-1 | 敌人管理器 |
| `Scripts/Time/ReplayRecorder.cs` | P4-1 | 回放记录器 |
| `Scripts/Time/ReplayPlayer.cs` | P4-2 | 回放播放器 |

### 修改（6 个）
| 文件 | Phase | 说明 |
|---|---|---|
| `Scripts/Base/PlayerInputController.cs` | P1-1, P2-3 | 移除编辑器引用 + 加 HP |
| `Scripts/Base/Enemy/BaseEnemy.cs` | P2-1 | 伤害/死亡逻辑 |
| `Scripts/Base/Enemy/EnemyMelee.cs` | P2-5 | 近战行为 |
| `Scripts/Base/Weapon/Bullet.cs` | P2-2 | 调用 TakeDamage |
| `ProjectSettings/EditorBuildSettings.asset` | P1-3 | 场景注册 |
| `Scripts/Time/GameMgr.cs` | P3-2 | 胜负状态处理 |
