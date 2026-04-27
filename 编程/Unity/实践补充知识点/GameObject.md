# Instantiate()
实例化并设置父对象相关 API
```C#
// 直接指定父对象 实例化后直接成为 parent 的子物体
// 位置和旋转保持和 prefab 里设置的本地坐标一致
// 参数一：实例化目标对象
// 参数二：父对象
public static Object Instantiate(Object original, Transform parent);

// 指定父对象，并控制坐标是世界坐标还是本地坐标
// 参数一：实例化目标对象
// 参数二：父对象
// 参数三：实例化后的坐标情况
//     true：保持实例化后的世界坐标不变，本地坐标会自适应
//     false：保持实例化后的本地坐标不变
public static Object Instantiate(Object original, Transform parent, bool instantiateInWorldSpace);

// 同时指定位置、旋转和父对象
// 注意：这个重载里的 position 和 rotation 是世界坐标
// 参数一：实例化目标对象
// 参数二：实例化后的位置坐标
// 参数三：实例化后的四元数旋转信息
// 参数四：父对象
public static Object Instantiate(Object original, Vector3 position, Quaternion rotation, Transform parent);
```
