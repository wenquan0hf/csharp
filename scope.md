# 作用域

名称的作用域（scope）是一个程序文本区域（region），在其中可以用过名称引用实体声明而不对该名称加以限定条件（qualification）。作用域可嵌套，内部作用域可重声明外部作用域名称的含义（但这并不会移除在第三章第三节中对其的限制——在嵌套块内不能声明与闭包块（enclosing block）内局部变量同名的局部变量）。因此可以说此外部作用域的名称在该内部作用域覆盖的程序文本区域内是隐藏（hidden）的，只能通过限定名来访问外部名称。

- 由 `namespace-member-declaration`（第九章第五节）声明的命名空间成员的作用域——如果没有其它 `namespace-declaration` 对其闭包的话——是整个（entire）程序文本。
- 由 `namespace-declaration` 内的 `namespace-member-declaration` 声明的命名空间成员的作用域是——如果假设该命名空间成员声明的完全限定名为 `N`——完全限定名为 `N` 或以 `N` 为始、后跟一个句号（period）的每个 `namespace-declaration` 的 `namespace-body`。
- 由 `extern-alias-directive` 定义的名称的作用域扩展到直接包含其编译单元或命名空间主体的 `using-directives`、`global-attributes` 和 `namespace-member-declarations`。`extern-alias-directive` 并不会为底层声明空间（underlying declaration space）增加任何成员。换句话说 `extern-alias-directive` 并不具传递性，相反其只会影响到在其内出现的 `compilation-unit` 或 `namespace-body`。
- 由 `using-directive`（第九章第四节）定义或导入的名称的作用域扩展到出现 `using-directive` 的 `compilation-unit` 或 `namespace-body` 的整个 `namespace-member-declarations`。`using-directive` 能使零或多个命名空间或类型名在特定的 `compilation-unit` 或 `namespace-body` 变得可用，但并不会为底层声明空间（underlying declaration space）增加任何成员。换句话说 `using-directive` 并不具传递性，相反其只会影响到在其出现的 `compilation-unit` 或 `namespace-body`。
- 由一个在 `class-declaration`（第十章第一节）中的 `type-parameter-list` 所声明的类型形参的作用域是该 `class-declaration` 的 `class-base`、`type-parameter-constraints-clauses` 以及 `class-body`。
- 由一个在 `struct-declaration`（第十一章第一节）中的 `type-parameter-list` 所声明的类型形参的作用域是该 `struct-declaration` 的 `struct-interfaces`、`type-parameter-constraints-clauses` 以及 `struct-body`。
- 由一个在 `interface-declaration`（第十三章第一节）中的 `type-parameter-list` 所声明的类型形参的作用域是该 `interface-declaration` 的 `interface-base`、`type-parameter-constraints-clauses` 以及 `interface-body`。
- 由一个在 `delegate-declaration`（第十五章第一节）中的 `type-parameter-list` 所声明的类型形参的作用域是该 `delegate-declaration` 的 `return-type`、`formal-parameter-list` 以及 `type-parameter-constraints-clauses`。
- 由 `class-member-declaration`（第十章第 1.6 节）所声明的成员的作用域位于该声明所出现的 `class-body` 之内。此外，类成员的作用域扩展到包含该成员且为可访问的（accessibility，第三章第 5.2 节）派生类的 `class-body`。
- 由 `struct-member-declaration`（第十一章第二节）声明的成员的作用域位于该声明出现的 `struct-body` 之内。
- 由 `enum-member-declaration`（第十四章第三节）所声明的成员的作用域位于该声明出现的 `enum-body` 内。
- 位于 `method-declaration`（第十章第六节）内所声明之参数的作用域是该 `method-declaration` 的 `method-body`。
- 位于 `indexer-declaration`（第十章第九节）内所声明之参数的作用域是该 `indexer-declaration` 的 `accessor-declarations`。
- 位于 `operator-declaration`（第十章第十节）内所声明之参数的作用域是该 `operator-declaration 的 block`。
- 位于 `constructor-declaration`（第十章第十一节）内所声明之参数的作用域是该 `constructor-declaration` 的 `constructor-initializer` 和 `block`。
- 位于 `lambda-declaration`（第七章第十五节）内所声明之参数的作用域是该 `lambda-expression` 的 `lambda-expression-body`。
- 位于 `anonymous-method-expression`（第七章第十五节）内所声明之参数的作用域是该 `anonymous-method-expression` 的 `block`。
- 位于 `labeled-statement`（第八章第四节）内所声明之标签的作用域是该声明所在的 `block`。
- 位于 `local-variable-declaration`（第八章第 5.1 节）内所声明之局部变量的作用域是该声明所在的 `block`。
- 位于 `switch` 语句（第八章第 8.3 节）的 `switch-block` 内所声明的局部变量的作用域是该 `switch-block`。
- 位于 `for` 语句（第八章第 8.3 节）的 `for-initializer` 内所声明的局部变量的作用域是该语句的 `for-initializer`、`for-condition`、`for-iterator` 以及所含之 `statement`。
- 位于 `local-constant-declaration`（第八章第 5.2 节）内声明的局部变量的作用域是该声明出现的 `block`。在该局部变量的 `constant-declarator` 之前的文本位置上引用该局部变量将出现一个「编译时错误」。
- 作为 `foreach-statement`、`using-statement`、`lock-statement` 或 `query-expression` 一部分所声明的变量的作用域由给定构造（construct）的扩展（expansion）所决定。

在命名空间、类、结构或枚举成员的作用域内，可以在位于该成员声明之前的文本位置上引用该成员。比方说

```
class A
{
    void F() {
        i = 1;
    }
    int i = 0;
}
```

在此，F 在 i 声明之前引用它是合法的。

在局部变量的作用域内，当引用局部变量的文本位置（textual position）在该局部变量声明之前（local-variable-declarator）会出现一个「编译时错误」。比方说

```
class A
{
    int i = 0;
    void F() {
        i = 1;   // 错误，在使用前先声明
        int i;
        i = 2;
    }
    void G() {
        int j = (j = 1);     // 有效
    }
    void H() {
        int a = 1, b = ++a;  // 有效
    }
}
```

在上面方法 `F` 中，第一个对 `i` 的赋值实际上并不会引用外部作用域的字段。相反，它引用了本地局部变量，而其结果是引发一个「编译时错误」，因为在文本上要求先声明变量。在方法 `G` 中，为 `j` 进行声明的同时在其初始化器内使用 `j` 是有效的，这是因为并没有在 `local-variable-declarator` 之前使用。在方法 `H` 中，后面的 `local-variable-declarator` 正确引用了局部变量（该局部变量在同一个 `local-variable-declarator` 内前面那个 `local-variable-declarator ` 中声明了）。

本地布局变量的作用域规则被设计为保证（guarantee）表达式上下文（expression context）中使用的名称的含义与在块中使用的含义是相同的。如果局部变量的作用域只从其声明之处其，到块尾部截止，那么在上例中，第一个赋值将分配给实例变量（instance variable），第二次赋值将分配给局部变量，如果块中的语句之后重新排列，会引发「编译时错误」。

块（block）中名称的含义可能因名称的使用上下文的不同而有所不同。在下例中，

```
using System;
class A {}
class Test
{
    static void Main() {
        string A = "hello, world";
        string s = A;         // 表达式上下文
        Type t = typeof(A);   // 类型上下文
        Console.WriteLine(s); // 输出 "hello, world"
        Console.WriteLine(t); // 输出 "A"
    }
}
```

在表达式上下文中的名称 `A` 引用本地变量 `A`，在类型上下文中引用类型 `A`。

## 名称隐藏

实体（entity）的作用域（scope）往往比实体声明空间包含（encompasses）更多的程序文本。具体来说，实体的作用域可能会引入新的声明空间，而其中或许会包含与该实体同名的实体。这类声明导致原始实体变为**隐藏的（hidden）**。相反，如果实体没有被隐藏，那么我们说它是**可见的（visible）**。

当作用域通过嵌套交叉（overlap）或当作用域通过继承交叉，那么会导致名称隐藏。两个被隐藏的类型的特征在下面两节中进行介绍。

## 通过嵌套隐藏

在命名空间内嵌套命名空间或类型，或者在类或结构内嵌套类型，以及性参与局部变量的声明，都将导致嵌套隐藏（hiding through nesting）名称。举个例子。

```
class A
{
    int i = 0;
    void F() {
        int i = 1;
    }
    void G() {
        i = 1;
    }
}
```

在方法 `F` 内，实例变量 `i` 被局部变量 `i` 所隐藏，但在方法 `G` 内，`i` 依旧引用实例变量。

当内部作用域的名称隐藏了外部作用域的名称时，它将隐藏该名称的所有重载。下例中，

```
class Outer
{
    static void F(int i) {}
    static void F(string s) {}
    class Inner
    {
        void G() {
            F(1);        // 调用 Outer.Inner.F
            F("Hello");  // 错误
        }
        static void F(long l) {}
    }
}
```

调用（call）`F(1)` 将调用（invokes）内部声明的 `F`，这是因为所有外部出现的 `F` 都被内部声明所隐藏。同样的，调用 `F("Hello")` 的结果是出现「编译时错误」。

## 通过继承隐藏

当类或结构重新声明（redeclare）从基类继承的名称时，会发生通过继承隐藏（hiding through inheritance）其名称。这种类型的名称隐藏采取以下形式：

- 常量、字段、属性、事件或类型引入类或结构后，会隐藏所有基类中的同名成员。
- 方法引入类或结构将隐藏所有同名的非方法基类成员（non-method base class members）和所有同签名（方法名、参数数量、修饰符与类型）的基类方法。
- 索引器引入类或结构将隐藏所有同签名（参数数量与类型）的基类索引。

管理运算符声明（governing operator declarations，第十章第十节）的规则使其不可能在派生类中声明一个与其基类内同签名的运算符。因此，操作符不能相互隐藏。

与从作用域外部对名称进行隐藏相反，通过继承的作用域隐藏名称可访问性会报出警告。下例中，

```
class Base
{
    public void F() {}
}
class Derived: Base
{
    public void F() {}  // 警告，隐藏了继承到的名称
}
```

派生类 `Derived` 中 `F` 的声明会导致一个警告。隐藏所继承的名称实际上不是一个错误，因为这会妨碍基类自身的单独改进。比方说，上述情况可能会发生因为 `Base` 的后续版本可能会引入一个 `F` 方法（而这个方法在之前版本中没有）。如果上述情况是错误的，那么对单独版本控制的基类的任何变化都会潜在导致派生类变得无效。

因隐藏继承到的名字而引发的警告可通过 `new` 修饰符消除：

```
class Base
{
    public void F() {}
}
class Derived: Base
{
    new public void F() {}
}
```

修饰符 `new` 表示派生类 `Derived` 中的 `F` 是「新的」，它将隐藏所继承到的（同签名）成员。新成员的声明将隐藏其所继承到的，当且仅当位于新成员的作用域内。

```
class Base
{
    public static void F() {}
}
class Derived: Base
{
    new private static void F() {} // 只会隐藏 Derived 内的 Base.F
}
class MoreDerived: Derived
{
    static void G() { F(); }       // 调用 Base.F
}
```

上例中，派生类中的 F 声明隐藏了（hide）其从基类继承到的 F。但由于派生类中新 F 的可访问性是 `private`，因此它的作用域达不到 `MoreDerived`。因此 `MoreDerived.G` 调用（call） `F()` 方法依旧合法，它将调用 `Base.F`。

