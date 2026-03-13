
`ref` 改变参数本身默认按值传递的传递方式
令该参数的传递方式变成按[引用类型](参数传递方式.md)传递

`ref` 的使用示例：
``` C#
// 调用处：使用 ref 调用
InitPlotStack(ref myStack, plotList);

// 定义方法：对应参数前标明 ref
private void InitPlotStack(ref Stack<DialoguePiece> plotStack, List<DialoguePiece> plot)
{
    plotStack = new Stack<DialoguePiece>();
}
```




