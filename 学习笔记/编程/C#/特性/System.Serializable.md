
主要作用：允许对象被序列化

- 允许将对象的状态（数据）转换为可存储或传输的格式（如 JSON、XML、二进制）
- 是 Unity 中 Inspector 面板显示自定义类的必要条件



序列化使得该对象允许被转换为可存储或传输的格式
```c#
// 保存游戏数据
[System.Serializable]
public class SaveData
{
    public int level;
    public float playTime;
    public List<string> inventory;
}

// 保存到文件
// 序列化使得类 SaveData 允许被JSON化
string json = JsonUtility.ToJson(saveData);
```


序列化使得在 Inspector 窗口中可编辑
```c#
[System.Serializable]
public class PlayerStats
{
    public string name;
    public int health;
    public float speed;
}

// 在 MonoBehaviour 中，这个类会显示在 Inspector 面板
public class Player : MonoBehaviour
{
	// 可以在 Inspector 中编辑该类的成员
    public PlayerStats stats;
}
```










