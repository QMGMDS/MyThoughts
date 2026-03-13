# 创建属性



==属性的定义：==

```c#
public class Player
{
	public int Experience //定义一个int类型的属性，名为Experience
    {
        get
        {
            return experience;
        }
        set
        {
            experience = value;
        }
    }
    
	//这是一个自动实现的属性的示例，通过prop+TAB可以自动插入以下代码段
    public int Health{ get; set;}
}
```



==属性的使用：==

```c#
void Start () 
    {
        Player myPlayer = new Player();

        //属性可以像变量一样使用
        myPlayer.Experience = 5; //利用set进行赋值
        int x = myPlayer.Experience; //利用get进行访问
    }
```



==属性的优点：==

- 通过删除 `get` 或者 `set` 将字段设置为只读或只写

- 能在访问器内部（即 `get` 和 `set` 中）运行其他代码和调用其他函数







# 静态



==静态成员的定义：==

```c#
public static int enemyCount = 0;

public static int Add(int num1, int num2)

通过添加关键词static实现静态特性
```



==静态的优点：==

- 静态让该变量（或者方法）成为跨类的所有实例共享的成员
- 静态成员可以直接通过类访问，无需先对类的对象进行实例化







# 方法重载



==方法重载的定义：==

```c#
using UnityEngine;
using System.Collections;

public class SomeClass
{
    //第一个 Add 方法的签名为
    //“Add(int, int)”。该签名必须具有唯一性。
    public int Add(int num1, int num2)
    {
        return num1 + num2;
    }

    //第二个 Add 方法的签名为
    //“Add(string, string)”。同样，该签名必须具有唯一性。
    public string Add(string str1, string str2)
    {
        return str1 + str2;
    }
}
```

这样就创建了具有相同名称的不同方法



==方法重载的使用：==

```c#
using UnityEngine;
using System.Collections;

public class SomeOtherClass : MonoBehaviour 
{
    void Start () 
    {
        SomeClass myClass = new SomeClass();

        //具体调用的 Add 方法将取决于传入的参数。
        myClass.Add (1, 2);
        myClass.Add ("Hello ", "World");
    }
}
```



==方法重载的优点：==

- 可以使用同一个方法名称去执行两项不同的操作

- 提高方法调用的效率







# 泛型



==泛型的定义与使用：==

- 泛型方法

```c#
using UnityEngine;
using System.Collections;

public class SomeClass 
{
    //这是一个泛型方法。注意通用类型“T”。该“T”将在运行时替换为实际类型。
    //<T>表示泛型方法，T表示返回值是个泛型变量，T param表示形参是泛型变量
    public T GenericMethod<T>(T param)
    {
        return param;
    }
}
```



```c#
using UnityEngine;
using System.Collections;

public class SomeOtherClass : MonoBehaviour 
{
    void Start () 
    {
        SomeClass myClass = new SomeClass();

        //为了使用此方法，必须告诉此方法用什么类型替换“T”。
        myClass.GenericMethod<int>(5);
    }
}
```





- 泛型类

```c#
using UnityEngine;
using System.Collections;

//这是一个泛型类。注意泛型标识“T”。
//“T”将被替换为实际类型，同样，
//该类中使用的“T”类型实例也将被替换。
public class GenericClass <T>
{
    T item;

    public void UpdateItem(T newItem)
    {
        item = newItem;
    }
}
```



```c#
using UnityEngine;
using System.Collections;

public class GenericClassExample : MonoBehaviour 
{
    void Start () 
    {        
        //为了创建通用类的对象，必须
        //指定希望该类具有的类型。
        GenericClass<int> myClass = new GenericClass<int>();

        myClass.UpdateItem(5);
    }
}

```



==总结：==

T只是一个使用惯例，实际上你可以用其他字母代替，甚至对一个方法有多个泛型的变量

**T就相当于一个万能占位符**，在泛型方法或者泛型类的使用要预先告诉：我要传入的是什么类型的变量



==泛型的优点：==

- 实现代码复用，让一套逻辑处理多种数据类型







# 继承



- 父类（基类）

```c#
using UnityEngine;
using System.Collections;

//这是基类，也称为父类。
public class Fruit 
{
    public string color; //继承了Fruit的子类

    //这是 Fruit 类的第一个构造函数，不会被任何派生类继承。
    //构造函数特点：没有返回值，用于对象创建时的初始化，使用 new 关键字进行调用
    public Fruit()
    {
        color = "orange";
        Debug.Log("1st Fruit Constructor Called");
    }

    //这是 Fruit 类的第二个构造函数，不会被任何派生类继承。
    public Fruit(string newColor)
    {
        color = newColor;
        Debug.Log("2nd Fruit Constructor Called");
    }

    public void Chop()
    {
        Debug.Log("The " + color + " fruit has been chopped.");        
    }

    public void SayHello()
    {
        Debug.Log("Hello, I am a fruit.");
    }
}
```

**注意：**Fruit中的两个构造函数相当于用**方法重载**让 new Fruit 时有两种不同的方法



- 子类（派生类）

```c#
using UnityEngine;
using System.Collections;

//这是派生类，也称为子类。
public class Apple : Fruit 
{
    //这是 Apple 类的第一个构造函数。
    //它立即调用父构造函数，甚至在它运行之前调用。
    public Apple()
    {
        //注意 Apple 如何访问公共变量 color，该变量是父 Fruit 类的一部分。
        //继承了Fruit的子类初始时有着color = "orange"
        color = "red";
        Debug.Log("1st Apple Constructor Called");
    }

    //这是 Apple 类的第二个构造函数。
    //它使用“base”关键字指定要调用哪个父构造函数。
    public Apple(string newColor) : base(newColor)
    {
        //请注意，该构造函数不会设置 color，
        //因为基构造函数会设置作为参数传递的 color。
        Debug.Log("2nd Apple Constructor Called");
    }
}
```





```c#
public class FruitSalad : MonoBehaviour 
{
    void Start () 
    {
        //让我们用默认构造函数来说明继承。
        Debug.Log("Creating the fruit");
        Fruit myFruit = new Fruit();
        Debug.Log("Creating the apple");
        Apple myApple = new Apple(); //创建的 Apple 同样有 Color 且初始为 red

        //调用 Fruit 类的方法。
        myFruit.SayHello();
        myFruit.Chop();

        //调用 Apple 类的方法。
        //注意 Apple 类如何访问 Fruit 类的所有公共方法。
        myApple.SayHello();
        myApple.Chop();

        //现在，让我们用读取字符串的构造函数来说明继承。
        Debug.Log("Creating the fruit");
        myFruit = new Fruit("yellow"); //使用 Fruit 中第二种方法的构造函数
        Debug.Log("Creating the apple");
        myApple = new Apple("green");

        //调用 Fruit 类的方法。
        myFruit.SayHello();
        myFruit.Chop();

        //调用 Apple 类的方法。
        //注意 Apple 类如何访问 Fruit 类的所有公共方法。
        myApple.SayHello();
        myApple.Chop();
    }
}
```





# 多态



==简单理解为：多态是一种子类被允许的性质==



- 父类

```c#
using UnityEngine;
using System.Collections;

public class Fruit 
{
    public Fruit()
    {
        Debug.Log("1st Fruit Constructor Called");
    }

    public void Chop()
    {
        Debug.Log("The fruit has been chopped.");        
    }

    public void SayHello()
    {
        Debug.Log("Hello, I am a fruit.");
    }
}
```



- 子类

```c#
using UnityEngine;
using System.Collections;

public class Apple : Fruit 
{
    public Apple()
    {
        Debug.Log("1st Apple Constructor Called");
    }

    //Apple 有自己的 Chop() 和 SayHello() 版本。
    //运行脚本时，请注意何时调用 Fruit 版本的这些方法以及何时调用 Apple 版本的这些方法。
    //此示例使用“new”关键字禁止来自 Unity 的警告，同时不覆盖 Apple 类中的方法。
    public new void Chop()
    {
        Debug.Log("The apple has been chopped.");
    }

    public new void SayHello()
    {
        Debug.Log("Hello, I am an apple.");
    }
}
```

==子类中允许自己重新定义源于父类中的方法==

==即：Apple 有自己的 Chop() 和 SayHello() 版本而不覆盖原本 Furit 中的方法==



- 父类的向下转换

```c#
using UnityEngine;
using System.Collections;

public class FruitSalad : MonoBehaviour
{
    void Start () 
    {
        //请注意，这里的变量“myFruit”的类型是
        //Fruit，但是被分配了对 Apple 的引用。这是
        //由于多态而起作用的。由于 Apple 是 Fruit，
        //因此这样是可行的。虽然 Apple 引用存储
        //在 Fruit 变量中，但只能像 Fruit 一样使用
        Fruit myFruit = new Apple();

        myFruit.SayHello();
        myFruit.Chop();

        //这称为向下转换。Fruit 类型的变量“myFruit”
        //实际上包含对 Apple 的引用。因此，
        //可以安全地将它转换回 Apple 变量。这使得
        //它可以像 Apple 一样使用，而在以前只能像 Fruit 一样使用。
        Apple myApple = (Apple)myFruit;

        myApple.SayHello();
        myApple.Chop();    
    }
}
```







# 成员隐藏



==简单理解为：成员隐藏是一种现象==



在这里定义了三个类，它们的关系是 Orc : Enemy : Humanoid

在 Orc 与 Enemy 中对 Yell 方法进行了版本更改

```c#
using UnityEngine;
using System.Collections;

public class Humanoid
{
    //Yell 方法的基版本
    public void Yell()
    {
        Debug.Log ("Humanoid version of the Yell() method");
    }
}
```

```c#
using UnityEngine;
using System.Collections;

public class Enemy : Humanoid
{
    //这会隐藏 Humanoid 版本。
    new public void Yell()
    {
        Debug.Log ("Enemy version of the Yell() method");
    }
}
```

```c#
using UnityEngine;
using System.Collections;

public class Orc : Enemy
{
    //这会隐藏 Enemy 版本。
    new public void Yell()
    {
        Debug.Log ("Orc version of the Yell() method");
    }
}
```



**最后： Orc 与 Enemy 中的 Yell 方法被隐藏了**

```c#
using UnityEngine;
using System.Collections;

public class WarBand : MonoBehaviour 
{
    void Start () 
    {
        Humanoid human = new Humanoid();
        Humanoid enemy = new Enemy();
        Humanoid orc = new Orc();

        //注意每个 Humanoid 变量如何包含对继承层级视图中不同类的引用，
        //但每个变量都调用 Humanoid Yell() 方法。
        human.Yell();
        enemy.Yell();
        orc.Yell();
    }
}
```







# 覆盖



==覆盖是指更改子类中的父类方法==



- 父类

```c#
using UnityEngine;
using System.Collections;

public class Fruit
{
    public Fruit ()
    {
        Debug.Log("1st Fruit Constructor Called");
    }

    //这些方法是虚方法，因此可以在子类中将它们覆盖
    //添加了关键词 virtual 的方法称之为虚方法
    public virtual void Chop ()
    {
        Debug.Log("The fruit has been chopped.");
    }

    public virtual void SayHello ()
    {
        Debug.Log("Hello, I am a fruit.");
    }
}
```



- 子类

```c#
using UnityEngine;
using System.Collections;

public class Apple : Fruit
{
    public Apple ()
    {
        Debug.Log("1st Apple Constructor Called");
    }

    //这些方法是覆盖方法，因此可以覆盖父类中的任何虚方法。
    //添加了关键词 override 的方法称之为覆盖方法
    public override void Chop ()
    {
        base.Chop(); // base.Chop() 表示调用了父类中的 Chop 方法
        Debug.Log("The apple has been chopped.");
    }

    public override void SayHello ()
    {
        base.SayHello();
        Debug.Log("Hello, I am an apple.");
    }
}
```



- 执行

```c#
using UnityEngine;
using System.Collections;

public class FruitSalad : MonoBehaviour 
{    
    void Start () 
    {
        Apple myApple = new Apple();

        //请注意，Apple 版本的方法将覆盖 Fruit 版本。
        //另外请注意，由于 Apple 版本使用“base”关键字来调用 Fruit 版本，因此两者都被调用。
        //即调用了父类 Fruit 的方法后，再执行子类 Apple 覆盖方法版本中 base. 之下的新的方法
        myApple.SayHello();
        myApple.Chop();

        //“覆盖”在多态情况下也很有用。
        //由于 Fruit 类的方法是“虚”的，而 Apple 类的方法是“覆盖”的，
        //因此当我们将 Apple 向上转换为 Fruit 时，将使用 Apple 版本的方法。
        Fruit myFruit = new Apple();
        myFruit.SayHello();
        myFruit.Chop();
    }
}
```







# 接口



==注意：接口不是类，不能有自己的实例==



==接口的定义：==

```c#
using UnityEngine;
using System.Collections;

//这是只有一个必需方法的基本接口。
public interface IKillable
{
    void Kill();
}

//这是一个泛型接口，其中 T 是将由实现类提供的数据类型的占位符。
public interface IDamageable<T>
{
    void Damage(T damageTaken);
}
```



 ==接口的使用：==

```c#
using UnityEngine;
using System.Collections;

public class Avatar : MonoBehaviour, IKillable, IDamageable<float> //这里接上接口
{
    //IKillable 接口的必需方法
    public void Kill()
    {
        //执行一些有趣操作
    }

    //IDamageable 接口的必需方法
    public void Damage(float damageTaken)
    {
        //执行一些有趣操作
    }
}
```



==接口的优点：==

- 接口用于跨多个互不相干的类定义通用功能







# 扩展方法



==向类型添加功能，而不必更改原始类型==

==如名字一样：扩展==

==适用于：需要向类添加功能而不能编辑类时==



==扩展方法的定义：==

```c#
using UnityEngine;
using System.Collections;

//创建一个包含所有扩展方法的类是很常见的做法。此类必须是静态类。
public static class ExtensionMethods
{
    //扩展方法即使像普通方法一样使用，也必须声明为静态。
    //请注意，第一个参数具有“this”关键字，后跟一个 Transform变量。
    //此变量表示扩展方法会成为哪个类的一部分。
    public static void ResetTransformation(this Transform trans) //重置Transform
    {
        trans.position = Vector3.zero;
        trans.localRotation = Quaternion.identity;
        trans.localScale = new Vector3(1, 1, 1);
    }
}
```



==扩展方法的使用：==

```c#
using UnityEngine;
using System.Collections;

public class SomeClass : MonoBehaviour 
{
    void Start () {
        //请注意，即使方法声明中有一个参数，也不会将任何参数传递给此扩展方法。
        //调用此方法的 Transform 对象会自动作为第一个参数传入。
        transform.ResetTransformation();
    }
}
```







# 命名空间



==类的容器==



==示例：==

```c#
using UnityEngine;
using System.Collections;

namespace SampleNamespace
{
    public class SomeClass : MonoBehaviour 
    {
        void Start () 
        {

        }
    }
}
```







# 列表和字典



==列表和字典是两个泛型集合==



==列表==

```c#
using UnityEngine;
using System.Collections;
using System; //这允许 IComparable 接口

//这是您将存储在不同集合中的类。
//为了使用集合的 Sort() 方法，此类需要实现 IComparable 接口。
public class BadGuy : IComparable<BadGuy>
{
    public string name;
    public int power;

    public BadGuy(string newName, int newPower) //这里是列表中单个列表数据的定义
    {
        name = newName;
        power = newPower;
    }

    //IComparable 接口需要此方法。
    public int CompareTo(BadGuy other)
    {
        if(other == null)
        {
            return 1;
        }

        //返回力量差异。
        return power - other.power;
    }
}
```



```c#
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class SomeClass : MonoBehaviour
{
    void Start () 
    {
        //这是创建列表的方式。注意如何在尖括号 (< >) 中指定类型。
        //即该列表是一个什么的列表，类似于一个结构体数组
        //但列表的创建不需要提前知道列表的长度
        List<BadGuy> badguys = new List<BadGuy>();

        //这里将 3 个 BadGuy 添加到列表
        badguys.Add( new BadGuy("Harvey", 50));
        badguys.Add( new BadGuy("Magneto", 100));
        badguys.Add( new BadGuy("Pip", 5));

        badguys.Sort(); //列表元素排序

        foreach(BadGuy guy in badguys)
        {
            print (guy.name + " " + guy.power);
        }

        //这会清除列表，使其为空。
        badguys.Clear();
    }
}
```



==字典==

```c#
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class SomeOtherClass : MonoBehaviour 
{
    void Start ()
    {
        //这是创建字典的方式。注意这是如何采用两个通用术语的。
        //在此情况中，您将使用字符串和 BadGuy 作为两个值。
        Dictionary<string, BadGuy> badguys = new Dictionary<string, BadGuy>();

        BadGuy bg1 = new BadGuy("Harvey", 50);
        BadGuy bg2 = new BadGuy("Magneto", 100);

        //可以使用 Add() 方法将变量放入字典中。
        badguys.Add("gangster", bg1);
        badguys.Add("mutant", bg2);

        BadGuy magneto = badguys["mutant"]; //拿出字典中 BadGuy 类型数据

        BadGuy temp = null;

        //这是一种访问字典中值的更安全但缓慢的方法。
        if(badguys.TryGetValue("birds", out temp))
        {
            //成功！
        }
        else
        {
            //失败！
        }
    }
}
```





# HashSet哈希集合



==HashSet是一种**无序的、不包含重复元素**的集合，基于哈希表实现==



==HashSet的优点：==

- 查找、插入、删除操作的时间复杂度接近O(1)，适用于需要重复快速的查找



==HashSet的缺点：==

- 集合中的元素无序，不允许有重复元素



# 协程



==协程是允许赋予给函数的一种特性，即协程函数==

==协程函数是按时间间隔执行的函数==



==协程函数的定义与使用：==



```c#
	//需要 IEnumerator 关键字
	//函数内要有 yield return 
	//函数在调用时使用 StartCoroutine(MyCoroutine())
	IEnumerator MyCoroutine ()
    {
        yield return new WaitForSeconds(3f);
    }
```







# 四元数



==Unity中存储旋转信息的数据称之为四元数==







# 委托



==简单理解成：函数的容器==



==示例：==

```c#
using UnityEngine;
using System.Collections;


public class DelegateScript : MonoBehaviour 
{
    //定义一个名为MyDelegate的委托
    //关键字 delegate 表示我要定义一个委托
    delegate void MyDelegate(int num);
    MyDelegate myDelegate;


    void Start () 
    {
        myDelegate = PrintNum; //将函数当成变量赋值给委托
        myDelegate(50); //调用委托相当于调用函数

        myDelegate = DoubleNum;
        myDelegate(50);
    }

    void PrintNum(int num)
    {
        print ("Print Num: " + num);
    }

    void DoubleNum(int num)
    {
        print ("Double Num: " + num * 2);
    }
}
```



==委托不仅仅只能容纳一个函数==

```c#
using UnityEngine;
using System.Collections;

public class MulticastScript : MonoBehaviour 
{
    delegate void MultiDelegate();
    MultiDelegate myMultiDelegate;


    void Start () 
    {
        //通过 += 运算符将两个函数方法分配给同一个委托变量
        myMultiDelegate += PowerUp;
        myMultiDelegate += TurnRed;

        //调用委托前判断委托是否有函数方法
        //如果没有函数方法则委托的调用会报空
        if(myMultiDelegate != null)
        {
            myMultiDelegate(); //调用委托，相当于执行了 PowerUp() 和 TurnRed() 两个函数
        }
    }

    void PowerUp()
    {
        print ("Orb is powering up!");
    }

    void TurnRed()
    {
        renderer.material.color = Color.red;
    }
}
```







# 特性



==在声明变量、方法、类时添加特性可以方便的为其附加信息==



==Range特性==

```c#
using UnityEngine;
using System.Collections;

public class SpinScript : MonoBehaviour 
{
    // Range 特性划定了speed的取值范围为[-100，100]
    [Range(-100, 100)] public int speed = 0;

    void Update () 
    {
        transform.Rotate(new Vector3(0, speed * Time.deltaTime, 0));
    }
}
```







# 事件



==事件是一种特殊类型的委托==

==适用于：提醒其他类发生了某个事件==



==事件的定义和调用==

```c#
using UnityEngine;
using System.Collections;

public class EventManager : MonoBehaviour 
{
    public delegate void ClickAction();
    public static event ClickAction OnClicked; //定义事件


    void OnGUI()
    {
        if(GUI.Button(new Rect(Screen.width / 2 - 50, 5, 100, 30), "Click"))
        {
            if(OnClicked != null)
                OnClicked(); //在这里，事件被调用了，说明事件订阅的函数方法也要一起被调用
        }
    }
}
```



==事件订阅的函数方法==

```c#
using UnityEngine;
using System.Collections;

public class TeleportScript : MonoBehaviour 
{
    void OnEnable()
    {
        EventManager.OnClicked += Teleport;
    }


    void OnDisable()
    {
        //当挂载TeleportScript脚本物体被销毁时退订方法Teleport，防止内存泄漏和游戏出错
        EventManager.OnClicked -= Teleport;
    }


    void Teleport()
    {
        Vector3 pos = transform.position;
        pos.y = Random.Range(1.0f, 3.0f);
        transform.position = pos;
    }
}
```



==总结：事件可被视为广播系统，对事件感兴趣的任何类，都可以将方法订阅到事件中==







# Out输出参数



==总结：==

`out` 参数让你能够从一个方法返回多个值，非常适合需要返回多个相关数据但又不想创建专门的数据结构的情况



==例如：==

````c#
public bool GetGridDimensions(string sceneName, out int width, out int height, out Vector2Int gridOrigin)
{
    // 假设根据场景名获取网格尺寸的逻辑
    if (sceneName == "Level1")
    {
        width = 10;
        height = 20;
        gridOrigin = new Vector2Int(0, 0);
        return true;
    }
    else
    {
        // 即使没有找到，也必须为 out 参数赋值
        width = 0;
        height = 0;
        gridOrigin = Vector2Int.zero;
        return false;
    }
}
````

````c#
int gridWidth, gridHeight;
Vector2Int origin;
bool success = GetGridDimensions("Level1", out gridWidth, out gridHeight, out origin);

if (success)
{
    // 使用 gridWidth, gridHeight, origin
    Debug.Log($"Grid Dimensions: {gridWidth}x{gridHeight}, Origin: {origin}");
}
````



==注意：==

- 在方法内部，必须对每个 `out` 参数进行赋值
- 在调用该方法时，也需要使用 `out` 关键字来传递参数，明确表示这些参数将被方法修改





# Stack栈



==总结：==

一种**后进先出(LIFO - Last In First Out)** 的数据结构



==特点：==

最近添加的元素总是最先被处理



==基本操作：==

- `Push()` ：将元素压入栈顶
- `Pop()` ：从栈顶弹出一个元素，并返回该元素。如果栈为空，则抛出异常。
- `Peek()` ：返回栈顶元素但不弹出。如果栈为空，则抛出异常。
- `Count` ：获取栈中元素的数量。

- `Clear()` ：清空栈。