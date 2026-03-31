
***PlayerPrefs*** 是 Unity 引擎中用于 **本地存储简单数据** 的类，适用于保存玩家偏好设置或游戏进度等小量数据。


# 主要特点

1. 键值对存储 
    类似字典，通过`键（string）`存取`值`（支持 int、float、string 三种类型）。

2. 存储位置
	- Windows：注册表

3. 持久化保存
    数据保存在本地，应用关闭后不会丢失。


# 基本用法

```c#
// 保存数据至内存缓冲区中，并没有立即写入磁盘
// 此时数据只在内存中
PlayerPrefs.SetInt("Score", 100);
PlayerPrefs.SetFloat("Volume", 0.8f);
PlayerPrefs.SetString("PlayerName", "小明");

// 保存到磁盘（建议在关键节点调用）
PlayerPrefs.Save();

// 读取数据（如果键不存在，返回默认值）
int score = PlayerPrefs.GetInt("Score", 0);
float volume = PlayerPrefs.GetFloat("Volume", 1.0f);
string name = PlayerPrefs.GetString("PlayerName", "默认名");

// 删除数据
PlayerPrefs.DeleteKey("Score");  // 删除指定键
PlayerPrefs.DeleteAll();         // 删除所有数据

// 检查键是否存在
bool hasKey = PlayerPrefs.HasKey("Score");
```


# 注意事项

1. 不适合大量数据
    存储大量数据会导致性能下降，推荐用文件或数据库（如 SQLite）。

2. 安全性低
    数据明文存储，容易被修改，重要数据需加密或使用服务器存储。

3. 平台差异
    不同平台的存储路径和限制可能不同（如 WebGL 依赖浏览器缓存）。

4. 类型限制
    仅支持三种基础类型，复杂数据需序列化（如转成 JSON 字符串存储）。











