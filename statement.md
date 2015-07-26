# 声明  

C# 程序中的声明定义了程序的组成元素。C# 程序有系统地使用了命名空间（namespace，第九章），命名空间可以包含**类型声明（type declarations）**和**嵌套命名空间声明（nested namespace declarations）**。类型声明（type declarations，第九章第六节）用于定义类（class，第十章）、结构（struct，第十章第十四节）、接口（interface，第十三章）、枚举（enum，第十四章）以及委托（delegates，第十五章）。类型声明中允许的成员种类取决于类型声明的形式。比方说类声明可以包含声明常量（constants，第十章第四节）、字段（fields，第十章第五节）、方法（methods，第十章第六节）、属性（properties，第十章第七节）、事件（events，第十章第八节）、索引器（indexers，第十章第九节）、操作符（operators，第十章第十节）、实例构造函数（instance constructors，第十章第十一节）、静态构造函数（static constructors，第十章第十二节）、析构函数（destructors，第十章第十三节）和嵌套类型（nested types，第十章第 3.8 节）。

声明在其所属**声明空间（declaration space）**内定义一个名称。除重载成员（第三章第六节）外，同一声明空间（declaration space）内出现两个以上同名成员会引发「编译时错误（compile-time error）」。同一声明空不可同时包含不同类型的同名方法。比方说，声明空间绝无包含同名字段与方法之可能。

有数种不同的声明空间，其具体描述如下：

- 在程序的所有源文件内，未闭包于 `namespace-declaration` 内的 `namespace-member-declarations` 是属于一个叫做**全局声明空间（global declaration space）**的组合声明空间内的成员；
- 在程序的所有源文件内，位于 `namespace-declarations` 内的 `namespace-member-declarations` 若拥有相同的完全限定命名空间名，则其属于同一个组合声明空间内；
- 每个类、结构或接口的声明都将创架一个新的声明空间。名称将通过 `class-member-declarations`、`struct-member-declarations`、`interface-member-declarations` 或 `type-parameters` 引入此声明空间。除了重载实例构造函数和静态构造函数，类或结构不允许声明包含一个与其类名或结构名同名的成员。类、结构或结构允许重载声明方法和索引器。此外类或结构允许重载声明实例构造函数与操作符。比方说类、结构或接口可以包含多个声明为同名的方法，这些所声明的方法拥有不同的签名（signature，第三章第六节）。注意，基类并不在该类的声明空间内，基接口也不在该接口的声明空间内，所以派生类或接口允许声明一个与继承成员同名的成员。这类成员隐藏（hide）了它们继续的那些成员；
- 每一个委托声明都会创建一个新的声明空间。名称通过形参（formal parameters）`fixed-parameters` 与 `parameter-arrays` 以及 `type-parameters` 引入这个声明空间；
- 每个枚举声明都会创建一个新的声明空间。名称通过 `enum-member-declarations` 引入这个声明空间；
- 每一个方法声明（method declaration）、索引器声明（indexer declaration）、操作符声明（operator declaration）、实例构造函数声明（instance constructor declaration）和匿名函数（anonymous function）都会创建一个名曰局部变量声明空间（local variable declaration space）的新声明空间。名称通过形参 `fixed-parameters` 和 `parameter-arrays` 以及 `type-parameters` 引入这个声明空间。如果有函数成员或匿名函数成员的函数体，则将会被是为嵌套于局部变量声明空间内。局部变量声明控件和嵌套局部变量声明空间内包含同名元素将引发错误。因此在一个闭包声明空间内，如果包含嵌套声明空间的话，那么不能在其内部声明一个与闭包声明空间内变量或常量同名的局部变量或常量。只有一种可能使得两个声明空间内包含同名元素，那就是这两个声明空间彼此不相互包含。
- 每个 `block` 或 `switch-block`，以及 `for`、`foreach` 和 `using` 语句将为局部变量和局部常量创建局部变量声明空间。名称将通过 `local-variable-declarations` 和 `local-constant-declarations` 引入这个声明空间。注意，作为函数成员主体或匿名函数主体的块，或是位于函数成员主体或匿名函数主体内的块，将嵌套在本地变量声明空间内（这个本地变量声明空间是为其参数所声明的）。因此，如果某个方法的局部变量和参数同名，那么将引发一个错误。
- 每个 `block` 或 `switch-block` 为每个标签（label）创建相互隔离的声明空间。名称将通过 `labeled-statements` 引入该声明空间，并通过 `goto-statements` 来引用。块的**标签声明空间（label declaration space）**可包含任何嵌套块。因此在嵌套块内不能声明一个与其所在闭包块（enclosing block）标签同名的标签。

声明名称的文本顺序一般而言是不重要的（no significance）。具体而言，声明并使用命名空间、常量、方法、属性、事件、索引器、操作符、实例构造函数、析构函数、静态构造函数以及类型，其文本顺序并不重要。在以下情况下声明顺序比较重要：
- 字段声明和局部变量声明的声明顺序意味着其初始化器（如果有的话）的执行顺序；
- 局部变量必须在使用前定义（第三章第七节）；
- 当 `when constant-expression` 被省略时，枚举成员声明（第十四章第三节）的顺序是重要的。

命名空间的声明空间是开放的（open ended），两个相同完全限定名（fully qualified name）的命名空间拥有同一个声明空间。比方说：

```
namespace Megacorp.Data
{
    class Customer
    {
        ...
    }
}
namespace Megacorp.Data
{
    class Order
    {
        ...
    }
}
```

这两个命名空间位于同一个声明空间内，在此例中两个类的完全限定名分别是 `Megacorp.Data.Customer` 和 `Megacorp.Data.Order`。因为这两个声明位于同一个声明空间内，所以如果这两个类的完全限定名是一样的话，会引发一个「编译时错误」。

如上文所指定，块（block）的声明空间可以嵌套任意块。因此，在下例中，F 方法和 G 方法会引发「编译时错误」，因为外部块中声明了 i，那么内部块就不能重复声明（redeclared）。然而 H 方法和 I 方法是合法的，这是由于两个 i 的声明都位于相互独立的非嵌套块（separate non-nested blocks）内。

```
class A
{
    void F() {
        int i = 0;
        if (true) {
            int i = 1;			
        }
    }
    void G() {
        if (true) {
            int i = 0;
        }
        int i = 1;				
    }
    void H() {
        if (true) {
            int i = 0;
        }
        if (true) {
            int i = 1;
        }
    }
    void I() {
        for (int i = 0; i < 10; i++)
            H();
        for (int i = 0; i < 10; i++)
            H();
    }
}
```

