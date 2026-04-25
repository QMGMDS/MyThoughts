# GetComponent 泛型方法能用父类装子类吗？

- 问题描述：
  如果一个物体身上挂载了子类脚本 Son 而没有父类脚本 Father
  通过 GetComponent<>(); 能够获得子类脚本吗？

- 示例代码：
```C#
class Father { }
class Son : Father { }

// Test 脚本挂载在仅有子类脚本的物体身上
class Test : MonoBehaviour
{
	void Start()
	{
		Son s = this.GetComponent<Father>() as Son;
	}
}
```

- 回答：
可以，但是得到的脚本需要 as 成子类才能使用内部的方法

- 记录神秘代码
```C#
private void PickUp()
{
	if (Input.GetKeyDown(KeyCode.E))
	{
		Ray r = Camera.main.ScreenPointToRay(new Vector3(Screen.width / 2, Screen.height / 2, 0));
		RaycastHit hitInfo;
		if (Physics.Raycast(r, out hitInfo, 1000) && hitInfo.collider.CompareTag("Weapon"))
		{
			// 获取目标物体身上的武器组件
			// 基类也可以获得子类
			BaseWeapon targetObj = hitInfo.collider.GetComponent<BaseWeapon>();
			// 判断目标物品是否为可拾取的物品
			if (targetObj is IPickUped)
			{
				// 如果是可拾取的物品就调用接口方法
				(targetObj as IPickUped).PickUped();
			}
		}
	}
}
```

