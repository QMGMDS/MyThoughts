# BulletTime Folder Structure

## 1. 目录总览

```plaintext
Assets/
├─ Art/               # 所有美术资源
├─ docs/              # 项目文档（本规范所在目录）
├─ Resources/         # 运行时动态加载资源
├─ Scenes/            # 场景文件
├─ Scripts/           # 所有C#脚本（按模块分层）
│  ├─ Base/           # 基础功能实现
│  ├─ Interface/      # 接口定义
│  ├─ Time/           # 子弹时间核心逻辑
│  ├─ Tool/           # 通用工具类
│  └─ 类图/           # 项目类图与架构文档
└─ Packages/          # Unity包管理器依赖（外部）
```

## 2. 各目录详细说明

### 2.1 `Assets/Art/` - 美术资源目录

存放所有与游戏内容相关的美术素材，按用途严格分类：
```plaintext
Art/
├─ Character/       # 角色相关素材
│  └─ Player/       # 玩家角色模型、动画、材质
├─ Weapon/          # 武器相关素材（手枪、拳头、子弹等）
├─ Special_Effects/ # 特效素材（枪口火焰、子弹尾迹、击中特效）
├─ Cursor/          # 十字准星UI素材
└─ Test/            # 测试用临时素材
```

- 约束：
	- 所有美术资源按模块分类存放，禁止直接丢在 `Art` 根目录。
	- 测试素材必须放入 `Test/` 目录，发布前统一删除。
	- 武器、特效素材需与 `Scripts/Base/Weapon/` 脚本一一对应命名（如 `Pistol.prefab` 对应 `Pistol.cs` ）。

### 2.2 `Assets/Scripts/` - 脚本目录（核心）

按模块 + 功能分层，与项目类图严格对应，保证 AI 快速定位逻辑：
```plaintext
Scripts/
├─ Base/              # 基础游戏功能实现
│  ├─ Player/         # 玩家控制逻辑
│  │  └─ PlayerInputController.cs  # 玩家输入、移动、攻击控制
│  ├─ Weapon/         # 武器系统
│  │  ├─ BaseWeapon.cs              # 武器基类
│  │  ├─ Pistol.cs                  # 手枪类
│  │  ├─ Fist.cs                    # 拳头类
│  │  ├─ Bullet.cs                  # 子弹逻辑
│  │  └─ WeaponData.cs              # 武器数据配置
│  └─ Enemy/          # 敌人AI系统
│     ├─ BaseEnemy.cs               # 敌人基类
│     ├─ EnemyMelee.cs              # 近战敌人
│     └─ EnemyRanged.cs             # 远程敌人
├─ Interface/         # 接口定义
│  └─ IPickUped.cs    # 可拾取物品接口（武器、道具实现）
├─ Time/              # 子弹时间核心逻辑
│  ├─ TimeMgr.cs      # 全局时间管理器
│  ├─ TimeControlled.cs # 受时间缩放控制的基类
│  └─ TimeInput.cs    # 子弹时间输入控制
├─ Tool/              # 通用工具类（跨模块复用）
│  ├─ AutoMonoSingleton.cs # 自动单例基类
│  ├─ PoolDataMgr.cs  # 对象池管理器（子弹、特效复用）
│  └─ ResLoadAsync.cs # 异步资源加载工具
└─ 类图/              # 项目架构与类图文档
```

- 约束：
	- 脚本与类图一一对应，未经允许禁止新增未在类图中定义的脚本。
	- `Base/` 下的脚本为业务逻辑核心，所有玩家、敌人、武器逻辑均继承自对应基类。
	- `Time/` 目录为项目核心机制，所有受时间缩放影响的物体必须继承 `TimeControlled.cs` 。
	- `Tool/` 下的工具类为通用功能，禁止写入业务逻辑，仅提供静态方法或单例管理。

### 2.3 其他关键目录

 `Assets/Scenes/` - 场景文件
- 存放游戏所有场景（主菜单、关卡、测试场景），按阶段命名：
	- `MainMenu.unity`：主菜单场景
	- `Level_01.unity`：第一关游戏场景
	- `Test_Time.unity`：子弹时间测试场景
- 约束：场景文件与 `Build Settings` 中的场景列表一一对应。

`Assets/Resources/` - 动态加载资源
- 存放需通过动态加载的资源（如预制体、资源预设）。
- 约束：禁止将大型资源放入此目录，仅存放运行时必须加载的配置与小型预设。

`Assets/docs/` - 项目文档
- 存放所有项目规范、设计文档（含本文件），后续新增模块需补充对应文档（如`module-time.md`、`module-weapon.md`）。

## 3. 目录扩展规则

1. 新增模块：如需新增功能模块（如存档系统），需在Scripts/下新增对应目录，并更新类图与文档，新建 `.md` 文件进行说明。
2. 命名规范：所有目录、文件命名遵循项目统一规范，详见 `naming-conventions.md` 这篇文档
3. 废弃目录：废弃资源 / 脚本需放入 `Assets/_Deprecated/` 目录，禁止直接删除（方便回溯）。

## 4. AI 协作关键提示

1. 核心机制逻辑优先查看 `Scripts/Time/` 目录，时间缩放控制均基于此模块。
2. 玩家与敌人的交互逻辑均基于 `Base/` 下的基类扩展，修改前需先阅读基类定义。
3. 可拾取物品必须实现 `Interface/IPickUped.cs` 接口，武器系统依赖此接口。

