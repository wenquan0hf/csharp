# 签名与重载

方法、实例构造器、索引器和操作符由其签名来鉴定：

- 方法签名由方法名、类型参数成员以及形参的类型与种类（值 value、引用 reference 或输出 output），按照从左到右的顺序构成。为此，出现在方法形参内的类型参数的识别并不根据它们的名字，而是依次根据它们在方法类型实参类表中的位置。
- 实例构造函数的签名由每个形参的类型和种类（值 value、引用 reference 或输出 output）从左到右构成。具体来说，实例构造函数签名不包含最右侧 `params` 修饰符（params modifier）指定的参数。
- 索引器签名由其每个形参的类型自左而右地构成。具体来说，索引器签名不包含元素类型，也不包含最右侧 `params` 修饰符（params modifier）指定的参数。
- 操作符签名由签名符名和形参类型自左而右构成。具体来说，操作符签名不包含结果类型。

签名能使用类、结构或接口的成员的重载机制：

- 允许类、结构或接口通过声明多个同名方法对其重载，只要它们的签名在类、结构或接口中是唯一的即可；
- 允许类或结构的实例构造函数通过声明多个实例构造函数对其重载，只要它们的签名在类或结构中是唯一的即可；
- 允许类、结构或接口中的索引器通过声明多个索引器对其重载，只要它们的签名在类、结构或接口中是唯一的即可；
- 允许类或结构的操作符通过声明多个操作符对其重载，只要它们的签名在类或结构中是唯一的即可。

虽然 `out` 和 `ref` 参数修饰符是构成签名的一部分，但不能仅通过 `ref` 和 `out` 修饰符来区分成员签名。如果两个成名声明为相同签名的类型（即使它们内的方法的形参修饰符从 `out` 变为 `ref`），那么将出现一个「编译时错误」。为了签名匹配的其他用途（比如隐藏 hidden 或重写 overriding），`ref` 和 `out` 是构成签名的一部分，以便相互不匹配。（这个限制允许 C# 程序能轻易转换为能运行在公共语言基础结构（Common Language Infrastructure，CLI）上，CLI 并未提供方式去定义只通过 `ref` 和 `out` 进行区别的方法）。

由于签名的缘故，`object` 类型和 `dynamic` 类型被认为是一样的。因此不能仅通过 `object` 和 `dynamic` 来区别在单一类型中的成员声明。

下例展示了一组重载的成员声明及其签名。

```
interface ITest
{
    void F();                  // F()
    void F(int x);             // F(int)
    void F(ref int x);         // F(ref int)
    void F(out int x);         // F(out int)   错误
    void F(int x, int y);      // F(int, int)
    int F(string s);           // F(string)
    int F(int x);              // F(int)       错误
    void F(string[] a);        // F(string[])
    void F(params string[] a); // F(string[])  错误
}
```

注意，任何 `ref` 和 `out` 参数修饰符（第十章第 6.1 节）都是签名的一部分。因此，`F(int)` 和 `F(ref int)` 都是唯一的签名。但是 `F(ref int)` 和 `F(out int)` 不能定义在同一个接口内，因为签名不能仅从 `ref` 和 `out` 加以区分。同样的，返回类型和 `params` 修饰符都不是签名的一部分，所以不可能仅基于返回类型或是否包含 `params` 修饰符来区别重载，故而上面的方法声明 `F(int)` 和 `F(params string[])` 会引发一个「编译时错误」。
