# 应用程序终止  

**应用程序终止**将返回控制权给执行环境。

应用程序**进入点**方法的返回类型是 `int`，该值作为应用程序**终止状态代码**（application's termination status code）返回。该代码的目的是允许与执行环境通讯以告知成功或失败。

如果进入点方法的返回类型是 `void`，遇到该方法的关门大括号 `}` 或执行到一个没有表达式的返回语句（return statement），则终止状态代码为 0。

先于应用程序的终止，未被垃圾回收的对象的析构函数将被调用，除非这类清理工作已被取消（suppressed）（比方说通过调用库方法`GC.SuppressFinalize`）。


