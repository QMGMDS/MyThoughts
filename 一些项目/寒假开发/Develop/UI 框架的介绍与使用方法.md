
> [!summary] 概述
>从 **指定目录** 下拿取对应 UI 预制体， 并在 **根节点** 中加载并激活该预制体



# 使用方法

在这里，游戏运行的一开始会显示标题界面 “TitlePanel” 

假设我们现在要为 “开始新游戏” 创建界面切换逻辑，点击后去往下一个 “NewGamePanel” 界面

**配置部分(找对应注释)：**

1. 将两个创建好了命名好了的界面预制体放在如下目录
![[Pasted image 20260201154422.png]]

2. 在 *UIManager* 中配置根目录，已经弄好了这里不要去动，现在所有 UI 界面都放在这里(这一步不用做！！)
![[Pasted image 20260201154552.png]]

3. 在 *UIManager* 脚本的 UIConst 类下配置常量表，“” 中的名字要与预制体名字保持一致
![[Pasted image 20260201155653.png]]

4. 在 *UIManager* 这里配置路径字典目录
![[Pasted image 20260201160044.png]]


**逻辑部分：**

1. 为 “TitlePanel” 界面下对应位置创建一个按钮 Button ，我这里取名为 “Start New” (这个名字要记下来，后面要用)
![[Pasted image 20260201153438.png]]

2. 为 “TitlePanel” 这个物体创建一个控制脚本 *TitlePanel* 并挂载上去，"NewGamePanel"也创建一个控制脚本 *NewGamePanel* 并挂载上去，二者都要继承 *BasePanel* 
![[Pasted image 20260201153112.png]]

3. 脚本中：
```c#
public class TitlePanel : BasePanel // 1.继承BasePanel
{
	// 2.声明按钮为 UIStartNewBtn
    private Transform UIStartNewBtn;

    override protected void Awake()
    {
        base.Awake();
        InitUI();
    }

    private void InitUI()
    {
        InitUIName();
        InitClick();
    }

    private void InitUIName()
    {
	    // 3.初始化(拿到按钮对应组件)
        UIStartNewBtn = transform.Find("Start New"); // Find("Start New") 填写按钮名称
    }

    private void InitClick()
    {
	    // 3.初始化(拿到按钮对应组件)
        UIStartNewBtn.GetComponent<Button>().onClick.AddListener(OnStartNew); // 添加一个 OnStartNew 事件
    }

    private void OnStartNew()
    {
	    print(">>>>> OnStartNew");
        ClosePanel(); // 关闭当前界面
        UIManager.Instance.OpenPanel(UIConst.NewGamePanel); // 进入新游戏界面
    }
}
```





