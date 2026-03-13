# 任务系统（简易版）使用说明

适配版本：字典ID管理 + 无放弃功能 + 暂停续跑（保留已耗时）

核心逻辑：动态新增任务 → 接取 → 开始/暂停（可续跑） → 完成，同一时间仅1个活跃任务

# 一、核心特性（必看）

- 字典管理：以「任务ID」为唯一Key，自动避免ID重复，查找/操作效率高

- 暂停续跑：暂停任务后保留已耗时，再次开始从暂停点继续，**无放弃任务功能**

- 时间统一：基于项目 `GameTime`（小时/分钟），适配 `TimeManager` 时间缩放

- 动态创建：无需Inspector配置，全部通过代码动态新增任务

# 二、前提准备

1. 场景中创建空物体，命名为 `TaskSystem`

2. 给该物体挂载 `TaskManager` 脚本（单例自动初始化，无需额外设置）

# 三、核心操作步骤（重中之重）

## 步骤1：动态新增任务（对话/剧情触发前调用）

两种创建方式，推荐方式1（简洁），ID必须唯一（建议格式：task_xxx）

```csharp
// 方式1：通过 小时+分钟 快速创建（最常用）
TaskData task = new TaskData(
    taskId: "task_cook_lunch",    // 唯一ID（不可重复）
    taskName: "烹饪午餐",          // UI显示名称
    taskDesc: "制作午餐，需30分钟",// UI显示描述
    totalHours: 0,                // 总耗时-小时
    totalMinutes: 30              // 总耗时-分钟
);
// 新增到任务管理器（重复ID返回false）
TaskManager.Instance.AddNewTask(task);

// 方式2：直接传入GameTime创建（适配已有GameTime变量）
GameTime taskTime = new GameTime(TimeConverter.HoursMinutesToSeconds(1, 15));// 1小时15分
TaskData task2 = new TaskData("task_clean", "打扫房间", "打扫1小时15分", taskTime);
TaskManager.Instance.AddNewTask(task2);
```

## 步骤2：接取任务（对话完成后调用）

传入任务ID即可，前提是该ID已通过 `AddNewTask` 新增

```csharp
// 对话结束回调示例
public void OnDialogueEnd(string taskId)
{
    // 接取任务（已接取/完成/ID不存在会返回false）
    bool success = TaskManager.Instance.AcceptTask(taskId);
    if (success)
    {
        Debug.Log($"接取任务成功：{taskId}");
    }
}
```

## 步骤3：开始/暂停任务（UI按钮绑定）

仅两个核心操作，暂停后保留已耗时，再次开始从暂停点续跑

```csharp
// 1. 开始/继续任务（适配“未开始”和“已暂停”状态）
TaskManager.Instance.StartTask("task_cook_lunch");

// 2. 暂停任务（仅当前活跃任务可暂停）
TaskManager.Instance.PauseTask("task_cook_lunch");
```

## 步骤4：任务完成（自动/手动）

- 自动完成：任务计时结束后，自动标记为 `Completed`，触发 `OnTaskCompleted` 事件

- 手动完成（调试/剧情强制）：

```csharp
// 手动完成指定任务
TaskManager.Instance.CompleteTaskManually("task_cook_lunch");
```

# 四、常用API（简易版）

|API名称|用途|关键说明|
|---|---|---|
|AddNewTask(TaskData task)|新增任务|ID唯一，重复返回false|
|AcceptTask(string taskId)|接取任务|仅未接取状态可接取|
|StartTask(string taskId)|开始/继续任务|暂停后调用可续跑|
|PauseTask(string taskId)|暂停任务|保留已耗时，仅活跃任务可暂停|
|GetTask(string taskId)|获取任务数据|返回TaskData，不存在返回null|
|ClearAllTasks()|清空所有任务|场景切换时必调用|
# 五、UI对接简易示例

绑定核心按钮和显示组件，监听关键事件即可

```csharp
using UnityEngine;
using UnityEngine.UI;

public class SimpleTaskUI : MonoBehaviour
{
    [SerializeField] private Slider _progressBar;
    [SerializeField] private Text _taskName;
    [SerializeField] private Text _remainingTime;
    [SerializeField] private Button _startBtn;
    [SerializeField] private Button _pauseBtn;

    private void Start()
    {
        // 绑定按钮
        _startBtn.onClick.AddListener(OnStart);
        _pauseBtn.onClick.AddListener(OnPause);

        // 监听进度和状态更新
        TaskManager.Instance.OnCurrentTaskProgressUpdated += (p) => _progressBar.value = p;
        TaskManager.Instance.OnTaskStateChanged += UpdateUI;
    }

    // 更新UI显示
    private void UpdateUI(TaskData task)
    {
        _taskName.text = task.TaskName;
        _remainingTime.text = $"剩余：{task.RemainingTimeString}";
        // 切换按钮显示
        _startBtn.SetActive(task.State is TaskState.Accepted or TaskState.Paused);
        _pauseBtn.SetActive(task.State == TaskState.InProgress);
    }

    private void OnStart()
    {
        if (TaskManager.Instance.CurrentActiveTask != null)
            TaskManager.Instance.StartTask(TaskManager.Instance.CurrentActiveTask.TaskId);
    }

    private void OnPause()
    {
        if (TaskManager.Instance.CurrentActiveTask != null)
            TaskManager.Instance.PauseTask(TaskManager.Instance.CurrentActiveTask.TaskId);
    }
}
```

# 六、关键注意事项

- 任务ID必须唯一，建议统一命名规范（task_功能名称）

- 同一时间仅1个活跃任务，需完成当前任务才能开始新任务

- 场景切换时，必须调用 `ClearAllTasks()`，避免计时器残留

- 暂停任务后，已耗时会保留，再次开始从暂停点继续，无放弃功能

- 新增任务必须在「接取任务」之前，否则接取会失败
> （注：文档部分内容可能由 AI 生成）