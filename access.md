# 成员访问

成员声明可用于控制对其的访问。成员可访问性由该成员的声明的可访问性（declared accessibility，第三章第 5.1 节）以及包含该成员的类型的可访问性（如果存在的话）来确定的。

当允许访问指定成员时，我们称该成员是**可访问的（accessible）**。相反，当不允许访问指定成员时，我们称该成员是**不可访问的（inaccessible）**。

当引发访问的文本位于成员的可访问域（accessibility domain，第三章第 5.2 节）内，则该成员允许被访问。

## 声明的可访问性

成员的**声明可访问性（declared accessibility）**可为以下类型之一：

- **Public**，通过在成员声明时使用 `public` 修饰符来选择之，其直观含义为「不受限制的访问（access not limited）」；
- **Protected**，通过在成员声明时使用 `protected` 修饰符来选择之，其直观含义为「仅限该类及其派生类型内可访问（access limited to the containing class or types derived from the containing class）」；
- **Internal**，通过在成员声明时使用 `internal` 修饰符来选择之，其直观含义为「仅限本程序内可访问（access limited to this program）」；
- **Protected internal**（表示 Protected 或 Internal），通过在成员声明时同时使用 `protected` 和 `internal` 修饰符来选择之，其直观含义为「仅限本程序内或该类及其派生类性内可访问（access limited to this program or types derived from the containing class）」；
- **Private**，通过在成员声明时使用 `private` 修饰符来选择之，其直观含义为「仅限于该类型内部访问（access limited to the containing type）」。

声明成员时被允许使用的可访问性类型取决于该成员所处之上下文。而且当成员在声明时不带任何访问控制修饰符（access modifiers），那么声明所在的上下文会为其选择一个默认的声明可访问性。

- 命名空间隐式的为 `public` 声明可访问性。命名空间声明不允许有访问控制修饰符。
- 编译单元或命名空间内的类型声明允许使用 `public` 或 `internal` 声明可访问性，且默认的声明可访问性为 `internal`。
- 类成员可以从这五种声明可访问性中挑选一个，且默认的声明可访问性为 `private`。（注意，类中声明一个某类型的成员也可以从五种声明可访问性中挑选一个，尽管这个类型作为命名空间下的成员在声明时只有 `public` 或 `internal` 可选。）
- 结构成员拥有 `public`、`internal` 和 `private` 声明可访问性，且默认声明可访问性为 `private`（这是因为结构隐式密封 `sealed`）。结构成员如果是在这个结构内声明的（也就是说不是继承自其基结构），那么其声明可访问性不可是 `protected` 或 `protected internal`。（注意，当一个类型声明为某结构的成员时，可以从 `public`、`internal` 和 `private` 中选择一个声明可访问性，尽管这个类型在命名空间中的声明可访问性只能是 `public` 或 `internal` 的。）
- 接口成员隐式的为 `public` 声明可访问性。接口成员声明不允许有访问控制修饰符。
- 枚举成员隐式的为 `public` 声明可访问性。枚举成员声明不允许有访问控制修饰符。

## 可访问域

成员**可访问域（accessibility domain）**由其所在程序文本之区段（sections，不一定是连续的）所组成，于该域之内可访问成员。为了定义成员的可访问域，若成员未被声明于一个类型内则谓之「顶级（top-level）」，若成员被声明于另一个类型内则谓之「嵌套（nested）」。此外，程序的**程序文本（program text）**被定义为其所有源文件中的全部文本，而类型的程序文本也被定义为其类型（及其嵌套类型）声明中的全部文本。

预定义类型（predefined type，诸如 `object`、`int` 或 `double`）的可访问域是没有限制的（unlimited）。

顶级未绑定类型 T（第四章第 4.3 节）的可访问域被声明在程序 P 中应被如下定义：

- 如果 T 的声明可访问性是 `public` 的，则其可访问域是 P 的整个程序文本以及所有引用了 P 的程序；
- 如果 T 的声明可访问性是 `internal` 的，则其可访问域是 P 的整个程序文本。

声明在程序 P 的类型 T 内的嵌套成员 M 的可访问类型的定义，遵照以下规则（注意，M 自身可能也是一个类型）：

- 如果 M 的声明可访问性是 `public`，那么 M 的可访问域与 T 的可访问域一致；
- 如果 M 的声明可访问性是 `protected internal`，设 D 为程序文本 P 与所有派生自 T 类型的程序文本（在 P 外部声明的）的并集（union），则 M 的可访问域为 T 的可访问域与 D 的交集（intersection）；
- 如果 M 的声明可访问性是 `protected`，设 D 为 T 的程序文本与所有派生自 T 类型的程序文本的并集（union），则 M 的可访问域为 T 的可访问域与 D 的交集（intersection）；
- 如果 M 的声明可访问性是 `internal`，则 M 的可访问域为 T 的可访问域与 P 的程序文本的交集（intersection）；
- 如果 M 的声明可访问性是 `private`，则 M 的可访问域就是 T 的程序文本。

由这些定义可以得知，嵌套成员的可访问域至少是该成员声明所在的类型的程序文本。此外还能发现这么一点，成员的可访问域绝不比该成员声明所在类型的可访问域更广。

直观地说，当访问类型或成员 M 时，遵循以下步骤进行计算以确保其可被访问到：

- 首先，如果 M 声明于一个类型（相反于编译单元或命名空间）内，则当该类型不可被访问将引发一个「编译时错误」；
- 其次，如果 M 是 `piblic`，则允许其被访问；
- 再次，如果 M 是 `protected internal`，那么访问发生在 M 声明所在的程序内或访问发生在派生自 M 的类型（第三章第 5.3 节）进行访问时，允许其被访问；
- 第四，如果 M 是 `protected`，那么访问发生在 M 声明所在的类内或访问发生在派生自 M 的类型（第三章第 5.3 节）进行访问时，允许其被访问；
- 第五，如果 M 是 `internal`，那么访问发生在 M 声明所在的程序内，允许其被访问；
- 第六，如果 M 是 `private`，那么访问发生在 M 声明所在的类型内，允许其被访问；
- 否则，类型或成员是不可访问的，同时将引发一个「编译时错误」。

在下面这个例子中，

```
public class A
{
    public static int X;
    internal static int Y;
    private static int Z;
}
internal class B
{
    public static int X;
    internal static int Y;
    private static int Z;
    public class C
    {
        public static int X;
        internal static int Y;
        private static int Z;
    }
    private class D
    {
        public static int X;
        internal static int Y;
        private static int Z;
    }
}
```

类和成员拥有如下可访问域：

- `A` 和 `A.X` 的可访问域不受限制；
- `A.Y`、`B`、`B.X`、`B.Y`、`B.C`、`B.C.X` 以及 `B.C.Y` 的可访问域是程序文本所在的程序内；
- `A.Z` 的可访问域是 A 的程序文本；
- `B.Z` 和 `B.D` 的可访问域是 B 的程序文本，包括 `B.C` 和 `B.D` 的程序文本；
- `B.C.Z` 的可访问域是 `B.C` 的程序文本；
- `B.D.X` 和 `B.D.Y` 的可访问域是 B 的程序文本，包括 `B.C` 和 `B.D` 的程序文本；
- `B.D.Z` 的可访问域是 `B.D` 的程序文本；

如示例所示，成员的可访问域永不会大于其所在类型（的可访问域）。比方说即便成员 X 的声明可访问性都是 `public`，但除了 `A.X` 外其余成员都受制于其所在类型。

如第三章第四节所解释的，所有来自基类的成员（除了实例构造函数、析构函数和静态构析函数）由派生类型所继承。这甚至包括了基类的私有成员。然而，所包含的私有成员的可访问域只能在其声明的类型内部的程序文本内。比方说：

```
class A
{
    int x;
    static void F(B b) {
        b.x = 1;        // 正确
    }
}
class B: A
{
    static void F(B b) {
        b.x = 1;        // 错误，x 不可访问
    }
}
```

B 类从 A 类继承了私有（private）成员 `x`。因为这个成员是私有的，所以它只能在 A 的类主体内部可被访问。因此，可以通过 `A.F` 方法访问 `b.x`，但不能通过 `B.F` 访问 `b.x`。

## 实例成员的受保护访问

当一个以 `protected` 修饰的实例成员被程序文本之外的类访问时，或者当一个以 `protected internal` 丢失的实例成员被程序文本之外的类访问时，访问必须发生在此类的派生类内。此外，这个访问必须通过在派生类实例或构造自它的类型的实例来访问。这个限制（restriction）组织了一个派生类访问另一个派生类的 `protected` 成员（即使它们派生自同一个基类）。

假设 B 为基类（它声明了一个受保护的（protected）实例成员 M），并设 B 为其派生类。在 D 的类主体（class-body）内部，须按照以下形式之一访问 M：

- `M` 形式的非限定（unqualified）的 `type-name` 或 `primary-expression`；
- `E.M` 形式的 `primary-expression`，假设类型 E 为 T 或其派生类，T 为 类型 D 或构造自 D 的类型；
- `base.M` 形式的 `primary-expression`

除这些形式的访问外，派生类可以在构造初始化器（constructor-initializer，第十章第 11.1 节）内访问到受保护的（protected）基类实例构造函数

```
public class A
{
    protected int x;
    static void F(A a, B b) {
        a.x = 1;        // 正确
        b.x = 1;        // 正确
    }
}
public class B: A
{
    static void F(A a, B b) {
        a.x = 1;        // 错误，必须通过 B 的实例访问
        b.x = 1;        // 正确
    }
}
```

上例 A 中，可以通过 A 和 B 访问到 x，因为这两次访问都发生在 A 的实例或其派生类中。然而，在 B 中，不能通过 A 的实例去访问 x，因为 A 不是 B 的派生类。

```
class C<T>
{
    protected T x;
}
class D<T>: C<T>
{
    static void F() {
        D<T> dt = new D<T>();
        D<int> di = new D<int>();
        D<string> ds = new D<string>();
        dt.x = default(T);
        di.x = 123;
        ds.x = "test";
    }
}
```

上例中的三个对 x 的赋值动作都是合法的，因为它们都通过构造自泛型类型的类类型的实例进行的。

## 可访问性约束

在 C# 语言的一些构造中要求类型至少与其成员或其它类型具有相同的可访问性（be at least as accessible as）。如果 T 的可访问域（accessibility domain）是 M 的可访问域的超集（superset），那么我们可以说类型 T 至少拥有与成员或类型 M 相同的可访问性。换句话说，如果在任何 M 可被访问的上下文中，T 都可被访问，那么 T 至少拥有 M 的可访问性。

有以下这些可访问性约束：

- 类类型的直接基类必须至少与该类类型自身具有相同的可访问性；
- 接口显式继承的基接口必须至少与该接口类型自身具有相同的可访问性；
- 委托类型的返回类型与形参类型必须至少与该委托自身具有相同的可访问性；
- 常量类型必须至少与该常量自身具有相同的可访问性；
- 字段类型必须至少与该字段自身拥有相同的可访问性；
- 方法的返回类型与形参类型必须至少与该方法自身具有相同的可访问性；
- 属性类型必须至少与该属性自身具有相同的可访问性；
- 事件类型必须至少与该事件自身具有相同的可访问性；
- 索引器的类型和形参类型必须至少与其自身具有相同的可访问性；
- 操作符的返回类型和形参类型必须至少与其自身具有相同的可访问性；
- 实例构造函数的参数类型必须至少与其自身具有相同的可访问性。

在下例中，

```
class A {...}
public class B: A {...}
```

B 类将出现「编译时错误」，因为 A 类不具备至少与 B 类相同的可访问性。同样，在下例中，

```
class A {...}
public class B
{
    A F() {...}
    internal A G() {...}
    public A H() {...}
}
```

H 方法将出现「编译时错误」，因为 H 方法所返回的类型 A 不具备至少与该方法相同的可访问性。