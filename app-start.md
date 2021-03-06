# 应用程序启动

拥有进入点（entry point）的程序集称**应用程序（application）**。当运行一应用程序时，将创建一新**应用程序域（application domain）**。同一个应用程序可在同一台机器（machine）上同时运行多个实例，并且每个实例都有自己的应用程序域。

应用程序域作为应用程序状态（application state）之容器（container），使应用程序相互隔离（isolation）。应用程序域是定义于应用及所用类库的类型之容器与边界。加载入不同的应用程序域的同一类型是相互泾渭分明的，而其实例化的对象也不会在应用程序域之间直接共享。比方说，对于这些类型的静态变量，每个应用程序域都自有一份其副本，同时这些类型的静态构造函数在应用程序域中至多运行一次。实现可以自由为应用程序域的创建和销毁提供指定实现（implementation-specific）策略或机制。

**应用程序启动（Application startup）**时，执行环境会调用一个特指的方法作为应用程序的进入点（entry point）。入口点方法一贯称为 Main，且可为下列签名中的一种：

- `static void Main() {...}`
- `static void Main(string[] args) {...}`
- `static int Main() {...}`
- `static int Main(string[] args) {...}`

如上所示，进入点可以选择 int 为其返回值。这个返回值通常被用在应用程序终止（application termination，第三章第二节）时。

进入点有个可选形参。这个参数可以用任何名称，但它的类型必须是 `string[]`。如果出现形参，那么当应用程序启动时，执行环境将通过命令行参数（command-line arguments）的方式创建并传递指定的 `string[]`实参。实参 `string[]`永不为空（null），但它可能长度为零（如果命令行没有指定实参的话）。

由于 C# 支持方法重构（overload），类或结构可以包含一个方法的多个定义，前提是每个重载版本都有不同的方法签名。然而，在一个程序内，类或结构内不能同时存在多个叫做 Main 的方法，因为 Main 被限定为只能作为应用程序的入口点。如果提供超过一个参数或者唯一参数类型不是 `string[]`，则允许重载多个 Main 版本。

应用程序可由多个类与结构组成，也许会有多个 Main 方法在这些类或结构内，由于 Main 的定义限定它只能是应用程序入口点，所以在这种情况下，外部机制（诸如命令行编译器选项）必须从中选择其一作为应用程序的进入点。

在 C# 中每一个方法都必须定义为类或结构的成员，一般情况下方法的声明可访问性（declared accessibility，第三章第 5.1 节）是由访问控制修饰符（access modifiers，第十章第 3.5 节）所决定的；同样，类型的声明声明可访问性也是由其访问控制修饰符所决定的。为了调用所给定类型的给定方法，类型与成员都必须可被访问（accessible），然应用程序进入点是特例。不管应用程序进入点的声明可访问性及其闭包类型的声明可访问性，执行环境总能访问到它们。

应用程序进入点方法（entry point method）不能放入泛型类声明内。

在其它各方面，进入点方法与非进入点方法的行为类似。





