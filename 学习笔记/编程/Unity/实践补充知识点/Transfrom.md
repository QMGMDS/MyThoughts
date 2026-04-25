# 解除父子关系
```C#
// 解除当前物体的父子关系
transform.parent = null;

// 使用 API 解除父子关系
transform.SetParent(null);

// 让当前物体的所有直接子物体都失去父级
transform.DetachChildren();
```

