# 配合 C-API

作为与Objective-C语言的互操作性的一部分，Swift也保持了一些与C语言的类型和功能的兼容性，如果你的代码有需要，Swift还提供了常见的C结构和模式的一种工作方式。

## 基本数据类型

Swift提供了一些C语言基本类型如*char*,*int*,*float*和*double*等的当量品。然而，这些类型和Swift核心基本类型之间没有隐式转换，如Int。因此，只有你的代码明确要求他们时再使用这些类型，而Int可以在任何你想使用它的时候使用。

| C 类型 | Swift 类型 |
| ------ | ------ |
| bool | CBool |
| char, signed char | CChar |
| unsigned char | CUnsignedChar |
| short | CShort |
| unsigned short | CUnsignedShort |
| int | CInt |
| unsigned int | CUnsignedInt |
| long | CLong |
| unsigned long | CUnsignedLong |
| long long | CLongLong |
| unsigned long long | CUnsignedLongLong |
| wchar_t | CWideChar |
| char16_t | CChar16 |
| char32_t | CChar32 |
| float | CFloat |
| double | CDouble |

## 枚举

Swift引进了Swift枚举标作为任何用宏NS_ENUM来标记的C风格的枚举。这意味着无论枚举值是在系统框架还是在自定义的代码中定义的，当他们导入到Swift时，他们的前缀名称将被截断。例如，看这个Objective-C枚举：

````
    //Objective-C
    typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
        UITableViewCellStyleDefault,
        UITableViewCellStyleValue1,
        UITableViewCellStyleValue2,
        UITableViewCellStyleSubtitle
    };

````

在Swift中这样来实现：

````
    //Swift
    enum UITableViewCellStyle: Int {
        case Default
        case Value1
        case Value2
        case Subtitle
    }

````

当您需要指向一个枚举值时，使用以点（.）开头的枚举名称：

````
    //Swift
    let cellStyle: UITableViewCellStyle = .Default

````

Swift也引进了标有NS_OPTIONS宏选项。而选项的行为类似于引进的枚举，选项还可以支持一些位操作，如＆，|和〜。在Objective-C中，你用一个空的选项设置标示恒为零（0）。在Swift中，使用*nil*代表没有任何选项。

## 指针

Swift尽可能避免让您直接访问指针。然而，当您需要直接操作内存的时候，Swift也为您提供了多种指针类型。下面的表使用Type作为占位符类型名称来表示语法的映射。

对于参数，以下映射适用：

| C 句法 | Swift 句法 |
| ------ | ------ |
| const void * | CConstVoidPointer |
| void * | CMutableVoidPointer |
| const Type * | CConstPointer\<Type\> |
| Type * | CMutablePointer\<Type\> |

对于返回类型，变量和参数类型的多层次指针，以下映射应用：

| C 句法 | Swift 句法 |
| ------ | ------ |
| void * | COpaquePointer |
| Type * | UnsafePointer\<Type\> |

对于类（class）类型，以下映射适用：

| C 句法 | Swift 句法 |
| ------ | ------ |
| Type * const * | CConstPointer\<Type\> |
| Type * __strong * | CMutablePointer\<Type\> |
| Type ** | AutoreleasingUnsafePointer\<Type\> |


### C 可变指针

当一个函数被声明为接受*CMutablePointer\<Type\>*参数时，这个函数可以接受下列任何一个类型作为参数：

* *nil*,作为空指针传入
* 一个*CMutablePointer\<Type\>*类型的值
* 一个操作数是Type类型的左值的输入输出表达式，作为这个左值的内存地址传入
* 一个输入输出Type[]值，作为一个数组的起始指针传入，并且它的生命周期将在这个调用期间被延长

如果您像这样声明了一个函数：

````
    //Swift
    func takesAMutablePointer(x: CMutablePointer<Float>) { /*...*/ }

````

那么您可以使用以下任何一种方式来调用这个函数：

````
    //Swift
    var x: Float = 0.0
    var p: CMutablePointer<Float> = nil
    var a: Float[] = [1.0, 2.0, 3.0]

    takesAMutablePointer(nil)
    takesAMutablePointer(p)
    takesAMutablePointer(&x)
    takesAMutablePointer(&a)
````

当函数被声明使用一个*CMutableVoidPointer*参数，那么这个函数接受任何和*CMutablePointer\<Type\>*相似类型的Type操作数。

如果您这样定义了一个函数：

````
    //Swift
    func takesAMutableVoidPointer(x: CMutableVoidPointer) { /* ... */ }
````
那么您可以使用以下任何一种方式来调用这个函数：

````
    //Swift
    var x: Float = 0.0, y: Int = 0
    var p: CMutablePointer<Float> = nil, q: CMutablePointer<Int> = nil
    var a: Float[] = [1.0, 2.0, 3.0], b: Int = [1, 2, 3]

    takesAMutableVoidPointer(nil)
    takesAMutableVoidPointer(p)
    takesAMutableVoidPointer(q)
    takesAMutableVoidPointer(&x)
    takesAMutableVoidPointer(&y)
    takesAMutableVoidPointer(&a)
    takesAMutableVoidPointer(&b)
````

### C 常指针

当一个函数被声明为接受*CConstPointer\<Type\>*参数时，这个函数可以接受下列任何一个类型作为参数：

* *nil*,作为空指针传入
* 一个*CMutablePointer\<Type\>, CMutableVoidPointer, CConstPointer\<Type\>, CConstVoidPointer*, 或者在必要情况下转换成*CConstPointer\<Type\>*的*AutoreleasingUnsafePointer\<Type\>*值
* 一个操作数是Type类型的左值的输入输出表达式，作为这个左值的内存地址传入
* 一个Type[]数组值，作为一个数组的起始指针传入，并且它的生命周期将在这个调用期间被延长

如果您这样定义了一个函数：

````
    //Swift
    func takesAConstPointer(x: CConstPointer<Float>) { /*...*/ }
````

那么您可以使用以下任何一种方式来调用这个函数：

````
    //Swift
    var x: Float = 0.0
    var p: CConstPointer<Float> = nil

    takesAConstPointer(nil)
    takesAConstPointer(p)
    takesAConstPointer(&x)
    takesAConstPointer([1.0, 2.0, 3.0])
````


当函数被声明使用一个*CConstVoidPointer*参数，那么这个函数接受任何和*CConstPointer\<Type\>*相似类型的Type操作数。
￼
如果您这样定义了一个函数：

````
    //Swift
    ￼￼￼￼func takesAConstVoidPointer(x: CConstVoidPointer) { /* ... */ }
````
那么您可以使用以下任何一种方式来调用这个函数：
￼
````
    //Swift
    var x: Float = 0.0, y: Int = 0
    var p: CConstPointer<Float> = nil, q: CConstPointer<Int> = nil takesAConstVoidPointer(nil)
    takesAConstVoidPointer(p)
    takesAConstVoidPointer(q)
    takesAConstVoidPointer(&x)
    takesAConstVoidPointer(&y)
    takesAConstVoidPointer([1.0, 2.0, 3.0]) takesAConstVoidPointer([1, 2, 3])
````

### 自动释放不安全指针

当一个函数被声明为接受*AutoreleasingUnsafePointer\<Type\>*参数时，这个函数可以接受下列任何一个类型作为参数：

* *nil*,作为空指针传入
* 一个*AutoreleasingUnsafePointer\<Type\>*值
* 其操作数是原始的，复制到一个临时的没有所有者的缓冲区的一个输入输出表达式，该缓冲区的地址传递给调用，并返回时，缓冲区中的值加载，保存，并重新分配到操作数。

**注：这个列表没有包含数组。**

如果您这样定义了一个函数：
````
    //Swift
    func takesAnAutoreleasingPointer(x: AutoreleasingUnsafePointer<NSDate?>) { /* ... */ }
````
那么您可以使用以下任何一种方式来调用这个函数：
````
    //Swift
    var x: NSDate? = nil
    var p: AutoreleasingUnsafePointer<NSDate?> = nil
￼￼￼￼￼
    takesAnAutoreleasingPointer(nil)
    takesAnAutoreleasingPointer(p)
    takesAnAutoreleasingPointer(&x)
````

**注：C语言函数指针没有被Swift引进。**

## 全局常量

在C和Objective-C语言源文件中定义的全局常量会自动地被Swift编译引进并做为Swift的全局常量。

## 预处理指令

Swift编译器不包含预处理器。取而代之的是，它充分利用了编译时属性，生成配置，和语言特性来完成相同的功能。因此，Swift没有引进预处理指令。

### 简单宏

在C和Objective-C，您通常使用的#define指令定义的一个基本常数，在Swift，您可以使用全局常量来代替。例如：一个全局定义*#define FADE_ANIMATION_DURATION 0.35*，在Swift可以使用*let FADE_ANIMATION_DURATION = 0.35*来更好的表述。由于简单的用于定义常量的宏会被直接被映射成Swift全局量，Swift编译器会自动引进在C或Objective-C源文件中定义的简单宏。

### 复杂宏

在C和Objective-C中使用的复杂宏在Swift中并没有副本。复杂宏是那些不用来定义常量的宏，包含，函数式宏。您在C和Objective-C使用复杂的宏以避免类型检查的限制或避免重新键入大量的样板代码。然而，宏也会产生Bug和重构的困难。在Swift中你可以使用函数和泛型来达到同样的效果，没有任何的妥协。因此，在C和Objective-C源文件中定义的复杂宏在Swift是不能使用的。

### 编译配置

Swift代码和C、Objective-C代码被有条件的编译也是用不同的方式的。SWIFT代码可以根据生成配置的评价可以有条件地编译。生成配置包括true和false字面值，命令行标志，和下表中的平台测试函数。您可以使用-D \<＃Flag＃\>指定命令行标志。

| 函数 | 有效参数 |
| --- | --- |
| os() | OSX, iOS |
| arch() | x86_64, arm, arm64, i386 |

>注意：arm 的生成配置不会为64位arm设备返回*true*，i386 的生成配置当为32位iOS 模拟器编译代码时返回*true*。

一个简单的有条件编译可以像下面这段代码：

````
    #if build configuration
      statements
    #else
      statements
    #endif

````

一个由零个或多个有效的Swift语句声明的*statements*，可以包括表达式，语句和控制流语句。您可以添加额外的构建配置要求，条件编译说明用&&和| |操作符，否定生成配置！操作符，添加条件控制块用＃elseif：

````
    #if build configuration && !build configuration
      statements
    #elseif build configuration
      statements
    #else
      statements
    #endif
````

与C语言编译器的条件编译相反，Swift条件编译语句必须完全是自包含和语法有效的代码块。这是因为即使它没有被编译的Swift代码也是被进行语法检查。
