# 规范

VERSION: 2025-12-15-2207

## 文件夹规范

推荐的文件夹结构

```
Assets/
├── _Project/                           # 项目主目录
│   ├── Prefabs/                        # 存放所有 NetworkIdentity 的物体
│   │   ├── Characters/                 # 角色预制体
│   │   ├── Environment/                # Tilemap、灯光、出口
│   │   └── Interactables/              # 炸弹、矿石、能量块
│   │
│   ├── Scripts/                        # 脚本
│   │   ├── Core/                       # 全局基础设施层
│   │   │   ├── Patterns/               # 单例、对象池、事件中心
│   │   │   ├── Time/                   # Timer、Cooldown、TickSystem
│   │   │   ├── Constants/              # StringConstants、TagConstants
│   │   │   ├── Helpers/                # MathHelper、RandomHelper
│   │   │   └── Extensions/             # 扩展方法
│   │   │
│   │   ├── Systems/                    # 系统（跨场景、跨模块）
│   │   │   ├── Networking/             # Mirror 网络消息、NetworkManager 扩展
│   │   │   ├── Input/                  # 输入系统（Input System 封装）
│   │   │   ├── Camera/                 # Cinemachine 控制
│   │   │   ├── UIFramework/            # UI 管理、窗口系统
│   │   │   ├── Save/                   # 存档系统
│   │   │   └── Audio/                  # 声音管理器
│   │   │
│   │   ├── Gameplay/                   # 具体游戏玩法逻辑
│   │   │   ├── Player/                 # 玩家移动、护盾、挖掘逻辑
│   │   │   ├── Enemy/                  # 敌人
│   │   │   ├── Combat/                 # 战斗系统、伤害计算
│   │   │   ├── Items/                  # 道具、拾取
│   │   │   └── Environment/            # 机关、触发器、交互物体
│   │   │
│   │   ├── UI/                         # UI 业务逻辑
│   │   │   ├── Screens/                # 主菜单、暂停界面、设置界面
│   │   │   └── Widgets/                # 血条、提示框、小组件
│   │   │
│   │   ├── Data/                       # 数据结构、ScriptableObject
│   │   │   ├── Configs/                # 配置表
│   │   │   └── Runtime/                # 运行时数据
│   │   │
│   │   └── Editor/                     # 自定义 Inspector、编辑器工具
│   │
│   ├── Scenes/                         # 场景文件
│   │   ├── Main/                       # 主场景（正式流程）
│   │   └── _Levels/                    # 关卡测试场景
│   │
│   ├── Art/                            # 美术资源
│   │   ├── Sprites/                    # 2D 贴图
│   │   └── UI/                         # UI 图标、界面素材
│   │
│   └── Settings/                       # Unity 配置
│
├── _Sandbox/               			# 【关键】个人开发沙盒
│   ├── YourName/            			# 你的测试区（随便折腾，不提交到主分支）
│   └── PartnerName/        			# 他的测试区
│
└── Plugins/                			# 存放 Mirror 等第三方插件

```

## 命名规范

遵循 微软C# 代码规范。



## **Namespace 规范**

### **1. 命名空间 = 项目名 + 结构**

```
PrototypeGod.Core.Patterns.Pooling
PrototypeGod.Core.Timer
PrototypeGod.Core.Constants
```

### **2. 命名空间使用 PascalCase**

### **3. 不使用缩写、不使用下划线、不使用中文**


### 命名空间

顶级命名是 `Serraria`

命名空间结构应与 Scripts/ 下的文件夹层级保持一致。

## Issue 规范

使用以下模板创建 issue.

```md
## 内容

建立基本的玩家预制体和控制器脚本。

_暂不考虑多人联机部分_

## TODO

- [ ] Player 视觉资源导入，并且和控制器脚本联动，能够自动 flip。
- [ ] 创建基本的玩家控制器，可以进行基本的左右移动和跳跃。

## 备注

Hello world
```

> [!TIP]
> 如果 Issue 要完成的事情太多，请考虑创建 SubIssue.

Issue 尽量遵循单一职责原则，如果需要多职责，请考虑在大 Issue 下创建 SubIssue.

## Commit 规范

commit message 遵守 https://www.itcan.cn/2025/03/27/git-commit-rule/

commit 保持功能单一和细粒度。提交消息结构

一个标准的 Git 提交消息由以下部分组成：

- **类型（type）**：表示提交的类别
- **范围（scope，可选）**：指定更改影响的模块或范围，用括号包裹
- **主题（subject）**：简短描述更改内容，通常以动词开头，50–72 字符以内
- **正文（body，可选）**：详细说明更改的原因和细节，每行不超过 72 字符
- **脚注（footer，可选）**：关联 issue 或其他元信息，例如 issue ID

## 格式模板

```
<类型>(<范围>): <主题>
<空行>
<正文>
<空行>
<脚注>
```

## 常见类型

| 类型           | 含义                 |
| ------------ | ------------------ |
| **feat**     | 新功能或特性             |
| **fix**      | 修复 bug             |
| **docs**     | 修改文档               |
| **style**    | 调整代码格式（不影响逻辑）      |
| **refactor** | 代码重构（不新增功能或修复 bug） |
| **test**     | 添加或修改测试            |
| **chore**    | 杂项（依赖更新、配置调整等）     |
| **perf**     | 性能优化               |
| **ci**       | 持续集成相关更改           |
| **revert**   | 回退之前的提交            |



## Project 规范

### Develop

**Status 对应的含义：**
- 无: 暂时不做这个功能，但以后可能会开始做.
- TODO: 可以做，但是还没开始 / 还没分配任务.
- In Progress: 正在做.
- Done: 做完了.

**Asignees 分配原则：**
- 一个 Issue 尽量保持少 Asignees。
- In Progress 的工作尽量不要改变 Asignees.
