## Random.insideUnitSphere
```C#
// 远程兵种敌人的脚本
public class EnemyRanged : MonoBehaviour
{
	// 玩家 Transform
	[SerializeField] private Transform _player;
	// 敌人射击 Transform
	[SerializeField] private Transform _shootPos;
	// 射击随机偏移 用于模拟敌人AI射击准度
	[SerializeField] private float _aimOffsetRange = 0.3f;
	
	// 射击方向的偏移计算 得到方向单位向量
	Vector3 shootDir = (_player.position + Random.insideUnitSphere * _aimOffsetRange - _shootPos.position).normalized;
}
```

`Random.insideUnitSphere` ：
```C#
Vector3 Random.insideUnitSphere { get; }
Returns a random point inside or on a sphere with radius 1.0 (Read Only).
```
在半径为 1 的三维球体中生成一个随机点位坐标
例如：
（0，0.2，0.4）
（1，0.3，0.5）
再通过向量的加法实现射击位置的偏移

