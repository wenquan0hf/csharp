# 自动内存管理

C# 使用（employs）自动内存管理（automatic memory management），这解放了开发人员必须手动分配与释放对象内存战勇的麻烦。自动内存管理策略由垃圾回收器（garbage collector）实现。对象的内存管理生命周期如下：

1. 当对象被创建时，为它分配内存，运行构造函数，视该对象为存活（live）对象。
2. 如果一个对象（或其任何一个部分）不能在后续执行（continuation of execution）中被访问——除了析构函数——那么这个对象将被视为不再使用并符合销毁条件（eligible for destruction）。C# 编译器和垃圾回收器会选择分析代码，并确定哪个对象的引用在未来会被使用。比方说，如果某范围内某个局部变量是某对象的唯一存在的引用，但在当前执行点（current execution point）之后的所有过程中（procedure）都不会再引用这个变量，那么垃圾回收器可能（但不是绝对）会理解为这个对象不再被使用。
3. 一旦对象符合销毁条件（eligible for destruction），那么在稍后某个时间（at some unspecified later time）将运行这个对象的析构函数（第十章第十三节）（如果有析构函数的话）。在正常情况下，对象的析构函数只会运行一次（once only），但特定实现（implementation-specific）的 APIs 可能会允许忽视（overridden）这个行为。
4. 一旦运行该对象的析构函数，那么这个对象（或其任何一部分）都不能在后续执行中被访问到——包括运行中的析构函数——这个对象将被视为不可访问（inaccessible）并符合回收条件（eligible for collection）。
5. 最后，在该对象符合回收条件后的某个时刻，垃圾回收器释放分配给该对象的内存。

垃圾回收器维护着对象使用（object usage）的信息，透过这些信息为内存管理作出决策，诸如何处内存存放了新建对象，何时迁移（relocate）一个对象以及一个对象何时开始不被使用或不可访问。

和其它有垃圾回收器的语言类似，C# 的设计也在努力使垃圾回收器能实现更广泛的（wide range）内存管理策略（memory management policies）比方说，C# 并不强求析构函数一定要运行，并不强求对象一满足条件就要立即被回收，并不强求析构函数一定要按照某个特定顺序执行或是在某个特定线程上执行。

垃圾回收器的行为是可控的，在某种程度上讲，可以通过 `System.GC` 上的静态方法（来实现）。通过这个类可以请求执行一次回收操作、运行（或不运行）析构函数，等。

由于垃圾回收器在决定「何时回收（collect）对象并执行析构函数」这一点上充分自由（allowed wide latitude），因此一个合格的实现也许会产生与下面所示代码有所不同的输出。在程序

```
using System;
class A
{
    ~A() {
        Console.WriteLine("Destruct instance of A");
    }
}
class B
{
    object Ref;
    public B(object o) {
        Ref = o;
    }
    ~B() {
        Console.WriteLine("Destruct instance of B");
    }
}
class Test
{
    static void Main() {
        B b = new B(new A());
        b = null;
        GC.Collect();
        GC.WaitForPendingFinalizers();
    }
}
```

中，创建了 A 和 B 的实例。当 null 值被赋予变量 b 之后，这些资源成为符合回收条件，这是由于自此之后用户所写的任何代码都不可能访问到它们。输出的结果可能是


```
Destruct instance of A
Destruct instance of B
```

或者

```
Destruct instance of B
Destruct instance of A
```

，这是由于语言并未对对象被垃圾回收的顺序强加限制。

尽管很相近，但区别「符合销毁条件（eligible for destruction）」与「符合回收条件（eligible for collection）」还是比较重要的，比方说：

```
using System;
class A
{
    ~A() {
        Console.WriteLine("Destruct instance of A");
    }
    public void F() {
        Console.WriteLine("A.F");
        Test.RefA = this;
    }
}
class B
{
    public A Ref;
    ~B() {
        Console.WriteLine("Destruct instance of B");
        Ref.F();
    }
}
class Test
{
    public static A RefA;
    public static B RefB;
    static void Main() {
        RefB = new B();
        RefA = new A();
        RefB.Ref = RefA;
        RefB = null;
        RefA = null;
        // A and B now eligible for destruction
        GC.Collect();
        GC.WaitForPendingFinalizers();
        // B now eligible for collection, but A is not
        if (RefA != null)
            Console.WriteLine("RefA is not null");
    }
}
```

在上面程序中，如果立即回收器（garbage collector）选择在运行 B 的析构函数前先运行 A 的析构函数，那么这个程序也许会输出：

```
Destruct instance of A
Destruct instance of B
A.F
RefA is not null
```

注意，尽管 A 的实例未被使用，依旧调用了 A 的析构函数，同时 A 的方法也有可能被其它析构函数调用（此例中为 F）。同时还要注意，析构函数的运行可能导致对象在主程序中再次变的可访问。在此例中，B 析构函数的运行导致了先前并未使用的 A 实例在通过引用 `Test.RefA` 时变得再次可访问。在调用 `WaitForPendingFinalizers` 之后，B 的实例便符合回收条件了，但由于引用了 `Test.RefA`，所以实例 A 还不能被回收。

为了避免混淆和意外行为，建议类的析构函数仅对自己内部的字段数据进行清理，不要去干涉其它所引用的对象或静态字段。

另一个替代析构函数的方法是让类实现 `System.IDisposable` 接口。这将允许对象的客户端（client of the object）决定何时释放自身资源，通常会在 `using` 语句（第八章第十三节）通过资源的方式访问该对象。

