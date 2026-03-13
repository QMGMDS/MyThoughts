# 对话系统架构和工作流程详解

## 系统整体架构

```
┌─────────────────────────────────────────────────────────────┐
│                        Excel文件                             │
│                   (包含故事对话数据)                          │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                     ExcelReader                              │
│              (读取Excel数据转换为ExcelData)                    │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                    DialogSystem                              │
│            (核心对话逻辑、管理对话流程)                         │
│                                                              │
│  ┌─ LoadStory()      加载Excel数据                           │
│  ├─ StartDialog()    开始对话流程                            │
│  ├─ ShowNextDialog() 显示下一条对话                          │
│  └─ OnDialogAdded事件 (新签名: speaker, message, isPlayer)  │
└────────────────────────────┬────────────────────────────────┘
                             │ OnDialogAdded
                             │ (包含说话者信息)
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                   DialogUIIntegrator                         │
│         (自动订阅事件，连接业务逻辑和UI显示)                   │
│                                                              │
│  ┌─ OnDialogAdded() 事件处理器                              │
│  └─ 调用 DialogContainerManager.AddDialog()                 │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                DialogContainerManager                        │
│         (管理气泡的创建、排列、滚动)                            │
│                                                              │
│  ┌─ AddDialog()           创建并显示气泡                      │
│  ├─ ClearAllDialogs()     清空所有气泡                       │
│  └─ GetDialogCount()      获取气泡数量                       │
└────────────────────────────┬────────────────────────────────┘
                             │ Instantiate
                             │ (创建预制体实例)
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                      DialogBubble                            │
│              (单个对话气泡的UI显示)                            │
│                                                              │
│  ┌─────────────────────────────────┐                        │
│  │   SpeakerName (TextMeshPro)     │ ◄── 说话者名称           │
│  ├─────────────────────────────────┤                        │
│  │    Content (TextMeshPro)        │ ◄── 对话内容            │
│  └─────────────────────────────────┘                        │
│                                                              │
│  ┌─ SetDialogContent() 设置内容                             │
│  └─ AdjustBubbleStyle() 调整样式                            │
└─────────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│              ScrollView (自动滚动和显示)                       │
│                                                              │
│  ┌────────────────────────────────┐                        │
│  │  Content (VerticalLayoutGroup)  │                       │
│  │                                │                        │
│  │  ┌─ DialogBubble 1 ──────┐    │                        │
│  │  │ Speaker: NPC1         │    │                        │
│  │  │ Content: 你好啊        │    │                        │
│  │  └──────────────────────┘    │                        │
│  │                                │                        │
│  │  ┌─ DialogBubble 2 ──────┐    │                        │
│  │  │ Speaker: You          │    │                        │
│  │  │ Content: 你好         │    │                        │
│  │  └──────────────────────┘    │                        │
│  │                                │                        │
│  │  ┌─ DialogBubble 3 ──────┐    │                        │
│  │  │ Speaker: NPC1         │    │ ◄── 自动滚动到这里        │
│  │  │ Content: 见面很高兴    │    │                        │
│  │  └──────────────────────┘    │                        │
│  └────────────────────────────────┘                        │
└─────────────────────────────────────────────────────────────┘
```

---

## 详细的对话流程

### 1. 初始化阶段

```
User Code
    │
    ├─ dialogSystem.Initialize()
    │     │
    │     └─► 初始化对话队列和状态
    │
    ├─ dialogSystem.LoadStory("story_name")
    │     │
    │     ├─► ExcelReader.ReadExcel(filePath)
    │     │     │
    │     │     └─► 返回List<ExcelData>
    │     │
    │     └─► 将数据放入对话队列
    │
    └─ dialogSystem.StartDialog()
          │
          └─► 调用 ShowNextDialog()
                  │
                  └─► 触发对话流程
```

### 2. 对话显示阶段

```
ShowNextDialog()
    │
    ├─ 从队列取出下一条对话 (ExcelData)
    │
    ├─ 判断是否为玩家对话
    │     │
    │     ├─ 是 ──► OnPlayerPrompt事件
    │     │          (等待玩家输入)
    │     │
    │     └─ 否 ──► 继续下一步
    │
    └─ 触发 OnDialogAdded事件
           │
           ├─ 参数1: speaker (说话者名称)
           ├─ 参数2: message (对话内容)
           └─ 参数3: isPlayer (是否为玩家)
                  │
                  └─► DialogUIIntegrator 接收
                       │
                       └─► 调用 DialogContainerManager.AddDialog()
                            │
                            ├─ 实例化 DialogBubble 预制体
                            │
                            ├─ 调用 bubble.SetDialogContent()
                            │     │
                            │     ├─ speakerNameText.text = speaker
                            │     ├─ contentText.text = message
                            │     └─ AdjustBubbleStyle(isPlayer)
                            │
                            ├─ 强制重新布局
                            │
                            └─ 滚动到底部
```

### 3. 玩家输入阶段

```
OnPlayerPrompt 事件 (包含选项列表)
    │
    └─► ChatInput 处理输入
         │
         ├─ 显示选项按钮 (如果有选项)
         │
         └─ 等待玩家选择或输入
            │
            └─ OnOptionSelected() 或 OnSendClicked()
                 │
                 └─► dialogSystem.AddPlayerMessage(msg)
                      │
                      ├─ 触发 OnDialogAdded(speaker="You", msg, true)
                      │
                      ├─ DialogContainerManager 创建气泡
                      │
                      └─ 调用 ShowNextDialog() 继续
```

### 4. 对话完成阶段

```
队列为空
    │
    └─ EndDialog()
         │
         ├─ 触发 OnDialogCompleted 事件
         │
         └─ 清空对话状态
```

---

## 核心数据结构

### ExcelData 结构
```csharp
public struct ExcelData
{
    public string name;                    // 说话者名称 (如: "NPC", "You", "老李")
    public string dialog;                  // 对话内容 (如: "你好啊！")
    public List<string> options;           // 选项列表 (如: ["选项1", "选项2"])
    public List<string> optionJumpPaths;   // 跳转路径 (如: ["story2", "story3"])
}
```

### Excel 文件格式
```
列数  │ 内容说明  │ 示例值              │ 备注
──────┼──────────┼───────────────────┼──────────────
0     │ Speaker  │ "NPC"             │ 说话者名称
1     │ Content  │ "你好！"           │ 对话内容
2     │ Option 1 │ "是的"             │ 第1个选项（可空）
3     │ Option 2 │ "不是"             │ 第2个选项（可空）
4     │ Jump 1   │ "story_next"      │ 选项1的跳转路径
5     │ Jump 2   │ "story_alt"       │ 选项2的跳转路径
```

---

## 事件和委托

### DialogSystem 的关键事件

#### 1. OnDialogAdded
```csharp
// 定义
public delegate void DialogEvent(string speaker, string message, bool isPlayer);
public event DialogEvent OnDialogAdded;

// 触发时刻
// - NPC对话显示时
// - 玩家消息确认时
// - AddNPCMessage()调用时

// 示例触发
OnDialogAdded?.Invoke("老李", "你好啊！", false);  // NPC
OnDialogAdded?.Invoke("You", "你好", true);         // 玩家

// 订阅示例
dialogSystem.OnDialogAdded += (speaker, msg, isPlayer) => {
    Debug.Log($"{speaker}: {msg}");
};
```

#### 2. OnPlayerPrompt
```csharp
// 定义
public event System.Action<string, List<string>> OnPlayerPrompt;

// 触发时刻
// - 遇到玩家条目（name == "You"）时

// 参数说明
// - 参数1: 待填入的提示文本
// - 参数2: 可选项列表 (可能为null或空)

// 示例
OnPlayerPrompt?.Invoke("请选择你的回答", new List<string> {"选项1", "选项2"});
```

#### 3. OnDialogCompleted
```csharp
// 定义
public event System.Action OnDialogCompleted;

// 触发时刻
// - 对话队列为空，所有对话都显示完成

// 示例
OnDialogCompleted += () => {
    Debug.Log("对话已完成！");
};
```

---

## 关键方法调用流程

### 场景1: 显示简单NPC对话

```
DialogBubble气泡显示流程：

DialogContainerManager.AddDialog("NPC", "你好！", false)
    │
    ├─ var bubble = Instantiate(dialogBubblePrefab, contentParent)
    │     │
    │     └─ 创建新的气泡UI元素
    │
    ├─ bubble.SetDialogContent("NPC", "你好！", false)
    │     │
    │     ├─ speakerNameText.text = "NPC"
    │     ├─ contentText.text = "你好！"
    │     └─ AdjustBubbleStyle(false)  // NPC样式
    │
    ├─ LayoutRebuilder.ForceRebuildLayoutHierarchy(contentParent)
    │     │
    │     └─ 重新计算VerticalLayoutGroup的布局
    │
    ├─ scrollRect.normalizedPosition = new Vector2(0, 0)
    │     │
    │     └─ 滚动到底部
    │
    └─ activeBubbles.Add(bubble)
         │
         └─ 保存气泡引用用于后续清理
```

### 场景2: 显示玩家对话和选项

```
玩家选择选项的流程：

1. DialogSystem 遇到玩家条目
   └─ OnPlayerPrompt?.Invoke("你想做什么？", ["选项1", "选项2"])

2. ChatInput 显示选项按钮
   └─ 创建2个Button，每个对应一个选项

3. 玩家点击选项
   └─ OnOptionSelected("选项1")

4. ChatInput 调用
   └─ dialogSystem.AddPlayerMessage("选项1")

5. DialogSystem 处理
   │
   ├─ OnDialogAdded?.Invoke("You", "选项1", true)
   │
   └─ DialogContainerManager 显示气泡
      │
      └─ 显示："You: 选项1"

6. DialogSystem 继续
   └─ ShowNextDialog()
      │
      └─ 显示下一条对话
```

---

## UI层级结构

### 推荐的Canvas结构
```
Canvas
├─ GamePanel
│  └─ PhonePanel
│     └─ DialogScrollView (ScrollRect)
│        ├─ Scrollbar Vertical (ScrollBar)
│        └─ Viewport
│           └─ Content (VerticalLayoutGroup + LayoutElement)
│              ├─ DialogBubble (预制体实例1)
│              │  ├─ SpeakerName (TextMeshPro)
│              │  └─ Content (TextMeshPro)
│              ├─ DialogBubble (预制体实例2)
│              │  ├─ SpeakerName
│              │  └─ Content
│              └─ DialogBubble (预制体实例N)
│                 ├─ SpeakerName
│                 └─ Content
│
└─ ChatInputPanel
   └─ InputField
```

---

## 性能考虑

### 内存管理
```csharp
// DialogContainerManager 自动清理
public void ClearAllDialogs()
{
    foreach (var bubble in activeBubbles)
    {
        Destroy(bubble.gameObject);  // 销毁UI元素
    }
    activeBubbles.Clear();           // 清空列表引用
}

// 在开始新对话前调用
dialogUIIntegrator.ClearDialogs();
dialogSystem.LoadStory("new_story");
dialogSystem.StartDialog();
```

### 优化建议
1. 对于长对话，考虑分页显示（分多个章节）
2. 使用对象池来重用预制体（降低GC压力）
3. 对于大量对话，考虑只保留最近的N条在UI上

---

## 扩展点

### 1. 自定义气泡样式
```csharp
// 修改DialogBubble.AdjustBubbleStyle()
private void AdjustBubbleStyle(bool isPlayer)
{
    // 改变颜色
    GetComponent<Image>().color = isPlayer ? Color.blue : Color.green;
    
    // 改变位置
    var rt = GetComponent<RectTransform>();
    rt.anchoredPosition = isPlayer ? new Vector2(100, 0) : new Vector2(-100, 0);
    
    // 添加动画
    StartCoroutine(ShowAnimation());
}
```

### 2. 自定义对话效果
```csharp
// 在DialogBubble中添加打字机效果
IEnumerator TypeContent(string content)
{
    contentText.text = "";
    foreach (char c in content)
    {
        contentText.text += c;
        yield return new WaitForSeconds(0.05f);
    }
}
```

### 3. 自定义布局
```csharp
// 修改DialogContainerManager的布局逻辑
public void AddDialog(string speaker, string content, bool isPlayer)
{
    // ... 实例化代码 ...
    
    // 自定义布局（网格、流式等）
    LayoutElement le = bubble.AddComponent<LayoutElement>();
    le.preferredWidth = isPlayer ? 300 : 200;
}
```

---

## 调试技巧

### 1. 追踪事件触发
```csharp
// 在DialogSystem中添加调试代码
OnDialogAdded?.Invoke(speaker, message, isPlayer);
Debug.Log($"[Dialog] {speaker}: {message} (isPlayer={isPlayer})");
```

### 2. 检查容器状态
```csharp
// 在DialogContainerManager中
public void AddDialog(string speaker, string content, bool isPlayer)
{
    Debug.Log($"Adding bubble #{activeBubbles.Count + 1}: {speaker}");
    // ... 其他代码 ...
    Debug.Log($"Total bubbles: {activeBubbles.Count}");
}
```

### 3. 监视滚动位置
```csharp
// 在DialogUIIntegrator中
private void OnDialogAdded(string speaker, string message, bool isPlayer)
{
    Debug.Log($"ScrollPosition before: {scrollRect.normalizedPosition.y}");
    // ... 显示气泡代码 ...
    Debug.Log($"ScrollPosition after: {scrollRect.normalizedPosition.y}");
}
```

---

祝你使用愉快！如有疑问，请参考相关文档。
