
头文件（Header Files）
- 包含声明、宏定义、模板等代码的文件（通常以`.h`、`.hpp`、`.hxx`结尾）
- 主要作用是提供接口声明，让多个源文件可以共享代码
命名空间（Namespace）
- 用于组织代码，避免命名冲突的机制
- 将全局作用域划分为不同的逻辑区域

总结：**头文件提供声明，命名空间提供作用域**

把C++标准库想象成一个大型图书馆：
- **头文件** 就像 **图书目录** - 告诉你可以借哪些书（vector、string、cout等）
- **命名空间** 就像 **书架位置** - 告诉你在哪里找到这些书（在std书架上）

```c++
#include <iostream>
using namespace std;

int main() {
    vector<int> v;  // 错误！
    // 编译器：我知道要去std命名空间找vector
    // 但是：我根本不知道vector是什么东西（因为没有声明）
}
```

```c++
#include <iostream>
#include <vector> // 包含 vector 的声明
using namespace std;

int main() {
    vector<int> v;  // 正确！
    // 编译器：要去std命名空间找vector
}
```





