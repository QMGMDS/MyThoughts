# BulletTime Naming Conventions

本规范用于统一分支、类、函数、字段、命名空间等命名风格，降低沟通和维护成本。

## 1. 分支命名

格式：`<type>/<scope-kebab-case>`

允许的 `type`：
- `feat/`：新功能
- `fix/`：缺陷修复
- `refactor/`：重构（不改外部行为）

示例：
- `feat/sp-ground-element-restraint`
- `fix/pool-null-check`
- `refactor/core-object-pool`

规则：
- `scope` 使用小写 + kebab-case。
- 名称要表达意图，不使用 `test`、`temp`、`update2` 这类模糊词。

## 2. C# 命名总原则

- 语义清晰，见名知意。
- 使用英文命名。
- 不使用拼音缩写和无意义缩写。
- 尽量与 Unity 和 .NET 生态常见术语保持一致。

官方参考：
- C# 标识符命名规则（Microsoft）：<https://learn.microsoft.com/dotnet/csharp/fundamentals/coding-style/identifier-names>

## 3. 类名（Class）

规则：
- 使用 PascalCase。
- 使用名词或名词短语。
- 行为类可使用明确后缀（如 `Controller`、`Service`、`System`、`Manager`），但不要滥用 `Manager`。

示例：
- `ObjectPool`
- `ProjectileSpawner`
- `GameStateController`

## 4. 函数名（Method）

规则：
- 使用 PascalCase。
- 优先使用动词或动宾结构，表达明确动作。
- 布尔返回方法建议用 `Is` / `Has` / `Can` 前缀。
- 异步方法必须以 `Async` 结尾。

示例：
- `GetFromPool()`
- `RecyclePool()`
- `IsGrounded()`
- `LoadSceneAsync()`

## 5. 字段名（Field）

规则：
- 私有字段：`_camelCase`。
- `readonly` 字段仍遵循私有字段规则。
- 公有字段不推荐直接暴露；优先用属性（Property）。
- 常量（`const`）使用 PascalCase。

示例：
- `private readonly Queue<T> _pool;`
- `private Transform _parent;`
- `public const int MaxRetryCount = 3;`

## 6. 属性名（Property）

规则：
- 使用 PascalCase。
- 名称应为名词或形容词短语。

示例：
- `public int Count { get; }`
- `public bool IsActive { get; set; }`

## 7. 命名空间（Namespace）

规则：
- 使用 PascalCase 分段，按模块层级组织。
- 与目录结构保持一致。
- 参考形式：`namespace Core.Pool`

建议结构：
- `Core.Pool`
- `Core.Gameplay`
- `Core.UI`
- `Game.Battle`

## 8. 接口、泛型、事件补充

接口：
- 以 `I` 开头，使用 PascalCase。
- 示例：`IPoolable`、`IDamageable`

泛型：
- 使用 `T` 前缀。
- 示例：`T`、`TItem`、`TKey`、`TValue`

事件：
- 事件名使用 PascalCase，表示已发生事实。
- 回调触发方法可用 `OnXxx`。
- 示例：`OnSpawned`、`OnRecycled`

## 9. 文件名与目录名

规则：
- 一个主要类型对应一个文件，文件名与类型名一致。
- 脚本文件使用 PascalCase，例如 `ObjectPool.cs`。
- 目录名按模块语义组织，避免 `Misc`、`Temp`。

## 10. Unity 项目补充建议

- `MonoBehaviour` 组件名应体现职责，避免 `PlayerScript` 这类泛化命名。
- 对可序列化字段使用 `[SerializeField] private` + 清晰字段名。
- 保持 Inspector 显示名可读，必要时用 `[Header]`、`[Tooltip]` 辅助。

## 11. 常见反例

- `DoIt()`、`Handle()`、`DataManager2`、`TmpObj`
- `public int hp;`（缩写不清晰）
- `namespace core.pool`（命名空间大小写不规范）
