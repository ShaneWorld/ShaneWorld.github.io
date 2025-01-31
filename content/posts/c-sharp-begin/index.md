---
title: C# 初学笔记
date: 2025-01-26T13:57:48+08:00
description: C# 初学笔记
lastmod: 2025-01-26T13:57:48+08:00
avatar: /me/shane.png
cover: https://gitee.com/shanegitee/pictures/raw/master/c-sharp-begin.webp
categories:
  - programming language
tags:
  - C#
draft: false
---

## Arch Linux Neovim 配置 C# 开发环境

安装 Dotnet：

```bash
sudo pacman -S dotnet-sdk
```

Neovim 安装 `csharp-language-server`，并在 lsp 配置文件中添加：

```lua
csharp_ls = {}
```

创建新项目：

```bash
dotnet new console -o HelloWorld
```

主要代码在 `Programs.cs` 文件中。编译或运行项目：

```bash
# 编译项目
dotnet build
# 运行项目
dotnet run
```

## C# 笔记

C# 是面向对象、面向组件的，由微软基于 C 和 C++ 开发。

### 程序结构

1. `using` 语句
2. `namespace` 声明，一个命名空间中包含很多类
3. `class` 声明，包含多个方法，定义类的行为
4. `Main` 方法，程序的入口

注意：

- C# 是大小写敏感的
- 不同于 Java，文件名可以和类名不相同
- `Main` 函数需要大写

### 基本语法

#### 注释

- `//` 单行注释
- `/* */` 多行注释。

#### 标识符

- 标识符必须以字母、下划线或 `@` 开头，后面可以跟一系列字母、数字、下划线、`@`
- 第一个字符不能是数字
- 不能包含任何嵌入的空格和符号
- 不能是关键字，**除非包含 `@` 前缀**
- 区分大小写
- 不能和类、库名称重合

#### 顶级语句(Top-Level statement)

```cs
using System;

namespace MyApp
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello, World!");
        }
    }
}
```

可以简化为：

```cs
using System;

Console.WriteLine("Hello, World!");
```

注意：

- 只能在一个源文件中使用
- 如果使用顶级语句，则隐式包含程序入口
- 在顶级语句中定义的变量和方法可以在整个文件中访问到

### 数据类型

1. 值类型

    如果要得到某个变量在特定平台上的尺寸，可以使用 `sizeof` 方法。

2. 引用类型

    - 指向一个内存地址。内置引用类型：`object`、 `dynamic`、`string`
    - `object` 是**所有通用类型**的基类，可以分配任何类型，但是，**分配之前需要
    先进行类型转换**。

        - 装箱：值类型转换为对象类型
        - 拆箱：对象类型转换为值类型
    
    - `dynamic` 类型可以存储任何类型的值, **类型检查是在运行时发生的**。通常使
    用静态类型以获得更好的动态性能。
    - `string` 类型可以使用两种形式分配:

        - `String str = "hello"`
        - `@"hello"`：转义字符当作普通字符对待
    

3. 指针类型

    存储另一个类型的内存地址。

[参考](https://www.runoob.com/csharp/csharp-data-types.html)

### 类型转换

分为**显式转换**和**隐式转换**两种。

- 隐式转换：较小范围 -> 较大范围，不会丢失数据。
- 显示转换：较大范围 -> 较小范围，会丢失数据。

```cs
// 隐式转换
byte b = 10;
int i = b;

// 显示转换（强制转换）
int i = 10;
byte b = (byte)i;

double value = 1.2;
int intValue = (int)value; // 会损失精度
```

C# 也提供了多种类型转换的方法，[参考](https://www.runoob.com/csharp/csharp-type-conversion.html)，使用这些方法可以处理异常。

### 运算符

C# 中的运算符和其他编程语言差不多。C# 中同样支持 `||`，`&&`，`+=` 等运算符。

其他运算符：

| 运算符 | 描述 |
|:------:|:----:|
| `sizeof()`| 返回数据类型大小|
| `typeof()` | 返回 class 的类型|
| `&` | 返回变量的地址 |
| `*` | 变量的指针 |
| `?:` | 条件表达式 |
| `is` | 判断对象是否为某一类型 |
| `as` | 强制转换，转换失败也不会抛出异常 |

### 封装

访问修饰符：

- `public`：所有对象都可以访问
- `private`：对象本身在对象内部可以访问
- `protected`：只有该类对象及其子类可以访问
- `internal`：同一个程序集的对象可以访问
- `protected internal`：访问限于当前程序集或派生自包含类的类型

### 方法

使用 `ref` 关键字按引用传递参数。

```cs
namespace NumberManipulate {
  class Operation {
    public void swap(ref int x, ref int y) {
      int temp = x;
      x = y;
      y = temp;
    }

    static void Main(string[] args) {
      int x = 100;
      int y = 200;
      Operation n = new Operation();
      n.swap(ref x, ref y);
      Console.WriteLine("x: " + x);
      Console.WriteLine("y: " + y);
    }
  }
}
```

使用 `out` 关键字按照输出传递参数。使用 `return` 只能返回一个值，但是使用 `out`
可以将方法输出的数据赋值给自己。

```cs
namespace NumberManipulate {
  class Operation {
    public void swap(out int x, out int y) {
      Console.WriteLine("first: ");
      x = Convert.ToInt32(Console.ReadLine());
      Console.WriteLine("second: ");
      y = Convert.ToInt32(Console.ReadLine());
    }

    static void Main(string[] args) {
      Operation n = new Operation();
      int a, b;
      n.swap(out a, out b);
      Console.WriteLine("a: " + a);
      Console.WriteLine("b: " + b);
    }
  }
}
```
上述例子可以看出，整个参数传递在传递没有初始化变量的是否特别有用。

### 可空类型

C# 提供 `Nullable` 类型。

```cs
int? i = 3;
```

上例中 `i` 是一个可空类型，如果其值为 `null`，也是可以接受的。

`??` 表示合并运算符。如果第一个操作数的值为 `null`，则运算符返回第二个操作数的
值，否则返回第一个操作数的值。

```cs
static void Main(string[] args) {
  double? num1 = null;
  double? num2 = 12.1;
  double num3;
  num3 = num1 ?? 5.34; // 如果 num1 为 null，则返回 5.34
}
```

### 数组

声明一个数组：

```cs
// 声明一个有 10 个元素的数组
int[] numbers = new int[10];
// 声明一个有 2 个元素的数组并初始化数组的值
int[] numbers = new int[2]{1, 2};
// 初始化的情况下可以省略元素个数
int[] numbers = new int[]{1, 2};
```

数组的赋值：**目标和源指向相同的内存地址**

数组遍历：`foreach` 循环

```cs
static void Main(string[] args) {
  int[] numbers = new int[2]{1, 2};
  // 遍历
  foreach (int num in numbers) {
    Console.WriteLine(num);
  }
}
```

### 结构体

```cs
struct Books {
  public string title;
  public string author;
  public string subject;
  public int book_id;
}

// 通过以下方法初始化
static void Main(string[] args) {
  Books book;
  Books book2 = new Books();
  book.title = "demo";
  book.author = "shane";
  book.subject = "math";
  book.book_id = 1234;
}
```

注意：

- 结构体适合表示轻量数据
- 结构可定义构造函数，但不能定义析构函数。但是，不能为结构体定义无参构造函数。无参构造函数(默认)是自动定义的，且不能被改变
- 不能继承，可实现一个或多个接口
- 不能指定访问修饰符
- 使用 `new` 操作符创建一个结构对象时，会调用适当的构造函数来创建结构。但是也可以不使用 `new` 关键字实例化。如果不使用，则必须在所有字段都被初始化后，字段才被赋值，对象才被使用
- 结构变量通常分配在栈上，这使得它们的创建和销毁速度更快。但是，如果将结构用作类的字段，且这个类是引用类型，那么结构将存储在堆上

### 枚举

```cs
enum Days { Sun, Mon, tue, Wed, thu, Fri, Sat };
```

### 类

对象是类的实例，构成类的方法和变量统称为类的成员。类的声明形式如下：

```cs
<access specifier> class class_name {

}
```

需要注意：

- 类默认的访问修饰符为 `internal`，成员的默认访问修饰符为`private`。
- 访问类的成员使用 `.` 运算符。

C# 类同样包含构造函数。**构造函数的名称和类名完全相同且没有任何返回类型**。默认的构造函数**没有任何参数**，有参构造函数称为**参数化构造函数**。

类的**析构函数**是类的一个特殊成员函数，用于**在程序结束之前释放资源**。析构函数**不能继承或重载**。

```cs
class Box {
    int width = 2;
    int height = 3;

    // 构造函数
    public Box(){
      Console.WriteLine("Box created");
    }

    // 析构函数
    ~Box() {
      Console.WriteLine("Box deleted");
    }
}
```

使用 `static` 修饰的变量为静态成员变量，意味着无论有多少个实例对象被创建，只有一个该静态成员的副本。

如果将一个函数使用 `static` 修饰，则该函数只能访问静态变量。静态函数在对象被创建之前就已经被创建。

### 继承

父类称为**基类**，子类称为**派生类**。C# **不支持类的多重继承**，但是支持**接口的多重继承**。

```cs
class Human {
  public float height;
  public int age;

  public void helloworld() {
    Console.WriteLine("hello world");
  }
}

// : 可以省略
class Student : Human {
  public void AnotherMethod() {
    // 使用 base 调用父类的成员
    base.helloworld();
  }
}
```

子类可以使用 `base` 调用父类的成员，除非该成员在父类中被 `private` 访问修饰符修饰。

父类对象应该在子类对象之前被创建，可以在成员初始化列表中初始化父类。

```cs
class Rectangle {
   // 成员变量
   protected double length;
   protected double width;
   public Rectangle(double l, double w)
   {
      length = l;
      width = w;
   }
}

class Tabletop : Rectangle {
   private double cost;
   public Tabletop(double l, double w) : base(l, w) {

   }
}
```

一个接口可以继承自一个或者多个其他接口，派生接口可以扩展父接口的成员列表，但是**不能改变他们的访问修饰符**。

C# **不支持多重继承**，但是可以**通过继承多个接口**实现。

### 多态性

多态表现为一个接口，多个功能。

- 静态多态性
    - 函数重载
    - 运算符重载
- 动态多态性
    使用 `abstract` 关键字创建抽象类

    ```cs
    abstract class Shape
    {
        abstract public int area();
    }
    class Rectangle:  Shape
    {
       private int length;
       private int width;
       public Rectangle( int a=0, int b=0)
       {
          length = a;
          width = b;
       }
       public override int area ()
       { 
          Console.WriteLine("Rectangle 类的面积：");
          return (width * length); 
       }
    }
    ```

    当一个定义在类中的函数需要在继承类中实现时，可以使用虚方法，虚方法使用 `virtual` 关键字声明。

### 运算重载符

重定义或重载内置的运算符，通过**关键字 `operator` 后跟运算符的符号来定义**。

```cs
class Box
{
  public int length;
  public int width;

  public static Box operator+ (Box a, Box b)
  {
    Box box = new Box();
    box.length = a.length + b.length;
    box.width = a.width + b.width;
    return box;
  }
}
```

### 接口

```cs
interface IMyInterface
{
  void MethodToImplement();
}
```

### 命名空间

区分名称的方式。

### 预处理指令

编译器在实际编译开始之前对信息进行预处理。通过这些指令，可以**控制编译器如何编译文件或编译哪些部分**。

所有的预处理指令都是以 `#` 开始，且在一行上，只有空白字符可以出现在预处理指令之前。**预处理指令不是语句，所以不以 `;` 结尾**。

| 指令       | 描述                                                                 |
|:------------:|:--------------------------------------------------------------------:|
| `#define`  | 定义一个符号，可以用于条件编译。                                           |
| `#undef`   | 取消定义一个符号。                                                       |
| `#if`      | 开始一个条件编译块，如果符号被定义则包含代码块。                                 |
| `#elif`    | 如果前面的 `#if` 或 `#elif` 条件不满足，且当前条件满足，则包含代码块。                 |
| `#else`    | 如果前面的 `#if` 或 `#elif` 条件不满足，则包含代码块。                           |
| `#endif`   | 结束一个条件编译块。                                                       |
| `#warning` | 生成编译器警告信息。                                                       |
| `#error`   | 生成编译器错误信息。                                                       |
| `#region`  | 标记一段代码区域，可以在 IDE 中折叠和展开这段代码，便于代码的组织和阅读。               |
| `#endregion` | 结束一个代码区域。                                                       |
| `#line`    | 更改编译器输出中的行号和文件名，可以用于调试或生成工具的代码。                           |
| `#pragma`  | 用于给编译器发送特殊指令，例如禁用或恢复特定的警告。                                   |
| `#nullable` | 控制可空性上下文和注释，允许启用或禁用对可空引用类型的编译器检查。                         |

使用预处理指令有以下作用：

- 提高代码可读性：使用 `#region` 可以帮助分隔代码块，提高代码的组织性。
- 条件编译：通过 `#if` 等指令可以在开发和生产环境中编译不同的代码，方便调试和发布。
- 警告和错误：通过 `#warning` 和 `#error` 可以在编译时提醒开发人员需要注意的特定的问题。

### 正则表达式

详见 [菜鸟教程](https://www.runoob.com/csharp/csharp-regular-expressions.html)。

### 异常处理

使用 `try catch finally` 语句进行异常处理。

```cs
try
{

}
catch(ExceptionName e1)
{
    // 抛出异常
    Throw e;
}
catch (ExceptionName e2)
{

}
// finally 语句是必须会执行的
finally
{

}
```

### 文件的输入和输出

参见 [菜鸟教程](https://www.runoob.com/csharp/csharp-file-io.html)。

### C# 属性

属性是类和结构体中用于封装数据的成员，可以看作是对字段的包装器，通常由 `get` 和 `set` 访问器构成。

```cs
public class DeBugInfo {
  public int BugNo {get;}
  public string Developer {get;}
  public string LastReview {get;}
  public string Message {get; set;}


  public DeBugInfo(int bg, string dev, string lr) {
    BugNo = bg;
    Developer = dev;
    LastReview = lr;
  }
}
```
`get` 和 `set` 是属性访问器，`get` 表明该属性只读，一旦初始化就不能修改。`set` 表明该属性可以修改。属性访问器（accessor）包含有助于获取（读取或计算）或设置（写入）属性的可执行语句。

上述代码中的 `public string Message {get; set;}` 等同于：

```cs
public string _message;

public string Message {
    get {
        return _message
    }
    set {
        _message = value;
    }
}
```

上述代码中，构造函数中没有初始化 `message`，因此其默认值为 `Null`，但是因为该属性是可以修改的（`set`），因此我们可以选择性地为该属性赋值。比如：

```cs
[DeBugInfo(1, "shane", "31/Jan/2025", Message = "This is a Box class.")]
// 或者
[DeBugInfo(1, "shane", "31/Jan/2025"]
```

### C# 反射

反射指程序可以访问、检测和修改它本身状态或行为的一种能力。您可以使用反射动态地创建类型的实例，将类型绑定到现有对象，或从现有对象中获取类型。然后，可以调用类型的方法或访问其字段和属性。

反射（Reflection）有下列用途：

- 它允许在运行时查看特性（attribute）信息。
- 它允许审查集合中的各种类型，以及实例化这些类型。
- 它允许延迟绑定的方法和属性（property）。
- 它允许在运行时创建新类型，然后使用这些类型执行一些任务。

在下一章中有具体的例子，使用反射获取所有*特性元信息*。

### C# 特性

允许开发者向代码元素（如类、方法、属性、字段等）添加额外的信息。这些信息可以在编译时或运行时被访问，从而影响程序的行为或提供额外的功能。

特性常常配合**反射**使用。

1. `[AttributeUsage]` 定义特性的使用范围。指定了自定义特性可以应用到哪些代码元素上（如类、方法、属性等），以及是否允许多次应用到同一个目标上，如：

    ```cs
    [AttributeUsage(AttributeTargets.Class |
                    AttributeTargets.Constructor |
                    AttributeTargets.Field |
                    AttributeTargets.Method |
                    AttributeTargets.Property,
                    AllowMultiple = true)]
    ```

2. 特定都需要继承自 `System.Attribute`

    ```cs
    public class MyCustomAttribute : Attribute
    {
        public string Description { get; }

        public MyCustomAttribute(string description)
        {
            Description = description;
        }
    }
    ```
    上述代码中的 `get;` 是访问器，表明该属性是只读的，一旦初始化后，只能读取，不能修改。

3. 实例：

    ```cs
    using System.Reflection;

    // 获取类上的 DeBugInfo 特性
    var classAttributes = typeof(Box).GetCustomAttributes(typeof(DeBugInfo), false);
    foreach (DeBugInfo attr in classAttributes)
    {
        Console.WriteLine("Class DeBugInfo:");
        Console.WriteLine($"  BugNo: {attr.BugNo}");
        Console.WriteLine($"  Developer: {attr.Developer}");
        Console.WriteLine($"  LastReview: {attr.LastReview}");
        Console.WriteLine($"  Message: {attr.Message}\n");
    }

    // 获取方法上的 DeBugInfo 特性
   var methodAttributes = typeof(Box).GetMethod("GetArea").GetCustomAttributes(typeof(DeBugInfo), false);
    foreach (DeBugInfo attr in methodAttributes)
    {
        Console.WriteLine("Method DeBugInfo:");
        Console.WriteLine($"  BugNo: {attr.BugNo}");
        Console.WriteLine($"  Developer: {attr.Developer}");
        Console.WriteLine($"  LastReview: {attr.LastReview}");
        Console.WriteLine($"  Message: {attr.Message}\n");
    }


    [AttributeUsage(
        AttributeTargets.Method |
        AttributeTargets.Class |
        AttributeTargets.Constructor
        , AllowMultiple = true)]

    public class DeBugInfo : Attribute {
      public int BugNo {get;}
      public string Developer {get;}
      public string LastReview {get;}
      public string Message {get; set;}


      public DeBugInfo(int bg, string dev, string lr) {
        BugNo = bg;
        Developer = dev;
        LastReview = lr;
      }
    }

    [DeBugInfo(1, "shane", "31/Jan/2025", Message = "This is a Box class.")]
    class Box {
      protected double length;
      protected double width;
      
      public Box(double l, double w) {
        length = l;
        width = w;
      }

      [DeBugInfo(2, "shane", "31/Jan/2025", Message = "This is a getArea method.")]
      public double GetArea() {
        return width * length;
      }
    }
    ```
### C# 索引器

### C# 委托
