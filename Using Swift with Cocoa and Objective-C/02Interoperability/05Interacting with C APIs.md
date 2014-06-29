> 翻译：[shockinglee](https://github.com/shockinglee)

> 校对：[shanyimin](https://github.com/shanyimin) [ChildhoodAndy](https://github.com/dabing1022)

# 与 C 语言交互编程
--------------------------

本节包含内容：

-   [基本数据类型（Primitive Types）](#primitive_types)
-   [枚举（Enumerations）](#enumerations)
-   [指针（Pointer）](#pointer)
-   [全局常量（Global Constants）](#global_constants)
-   [预处理指令（Preprocessor Directives）](#preprocessor_directives)


作为一种可与 Objective-C 相互调用的语言，Swift 也具有一些与 C 语言的类型和特性，如果你的代码有需要，Swift 也提供了和常见的 C 代码结构混合编程的编程方式。

<a name="primitive_types"></a>
## 基本数据类型

Swift 提供了一些和 C 语言的基本类型如`char`，`int`，`float`，`double`等价的 Swift 基本数据类型。然而，这些 Swift 的核心基本类型之间并不能隐式的相互转换，如 Int。因此，只有你的代码明确要求它们时再使用这些类型，而 Int 可以在任何你想使用它的时候使用。

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

<a name="enumerations"></a>
## 枚举

Swift 引进了用宏`NS_ENUM`来标记的任何 C 风格的枚举类型。这意味着无论枚举值是在系统框架还是在自定义的代码中定义的，当他们导入到 Swift 时，他们的前缀名称将被截断。例如，看这个 Objective-C 枚举：

```objective-c
//Objective-C
typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
	UITableViewCellStyleDefault,
	UITableViewCellStyleValue1,
	UITableViewCellStyleValue2,
	UITableViewCellStyleSubtitle
};
```

在 Swift 中这样来实现：

```swift
//Swift
enum UITableViewCellStyle: Int {
	case Default
	case Value1
	case Value2
	case Subtitle
	}
```

当您需要指向一个枚举值时，使用以点（.）开头的枚举名称：

```swift
//Swift
let cellStyle: UITableViewCellStyle = .Default

```

Swift 也引进了标有`NS_OPTIONS`宏选项。而选项的行为类似于引进的枚举，选项还可以支持一些位操作，如 ＆，| 和 〜。在 Objective-C 中，你用一个空的选项设置标示恒为零（0）。在 Swift 中，使用 `nil`代表没有任何选项。

<a name="pointer"></a>
## 指针

Swift 尽可能避免让您直接访问指针。然而，当您需要直接操作内存的时候，Swift 也为您提供了多种指针类型。下面的表使用 Type 作为占位符类型名称来表示语法的映射。

对于参数，使用以下映射：

| C 句法 | Swift 句法 |
| ------ | ------ |
| const void * | CConstVoidPointer |
| void * | CMutableVoidPointer |
| const Type * | CConstPointer\<Type\> |
| Type * | CMutablePointer\<Type\> |

对于返回类型，变量和参数类型的多层次指针，使用以下映射：

| C 句法 | Swift 句法 |
| ------ | ------ |
| void * | COpaquePointer |
| Type * | UnsafePointer\<Type\> |

对于类（class）类型，使用以下映射：

| C 句法 | Swift 句法 |
| ------ | ------ |
| Type * const * | CConstPointer\<Type\> |
| Type * __strong * | CMutablePointer\<Type\> |
| Type ** | AutoreleasingUnsafePointer\<Type\> |


### C 可变指针

当一个函数被声明为接受`CMutablePointer<Type>`参数时，这个函数可以接受下列任何一个类型作为参数：

* `nil`，作为空指针传入
* 一个`CMutablePointer<Type>`类型的值
* 一个操作数是 `Type` 类型的左值的输入输出表达式，作为这个左值的内存地址传入
* 一个输入输出 `Type[]` 值，作为一个数组的起始指针传入，并且它的生命周期将在这个调用期间被延长

如果您像这样声明了一个函数：

```swift
//Swift
func takesAMutablePointer(x: CMutablePointer<Float>) { /*...*/ }
```

那么您可以使用以下任何一种方式来调用这个函数：

```swift
//Swift
var x: Float = 0.0
var p: CMutablePointer<Float> = nil
var a: Float[] = [1.0, 2.0, 3.0]

takesAMutablePointer(nil)
takesAMutablePointer(p)
takesAMutablePointer(&x)
takesAMutablePointer(&a)
```

当函数被声明使用一个`CMutableVoidPointer`参数，那么这个函数接受任何和`CMutablePointer<Type>`相似类型的`Type`操作数。

如果您这样定义了一个函数：

```swift
//Swift
func takesAMutableVoidPointer(x: CMutableVoidPointer) { /* ... */ }
```
那么您可以使用以下任何一种方式来调用这个函数：

```swift
//Swift
var x: Float = 0.0, y: Int = 0
var p: CMutablePointer<Float> = nil, q: CMutablePointer<Int> = nil
var a: Float[] = [1.0, 2.0, 3.0], b: Int[] = [1, 2, 3]

takesAMutableVoidPointer(nil)
takesAMutableVoidPointer(p)
takesAMutableVoidPointer(q)
takesAMutableVoidPointer(&x)
takesAMutableVoidPointer(&y)
takesAMutableVoidPointer(&a)
takesAMutableVoidPointer(&b)
```

### C 常指针

当一个函数被声明为接受`CConstPointer<Type>`参数时，这个函数可以接受下列任何一个类型作为参数：

-  `nil`，作为空指针传入
-  一个`CMutablePointer<Type>`, `CMutableVoidPointer`, `CConstPointer<Type>`, `CConstVoidPointer`,  或者在必要情况下转换成`CConstPointer<Type>`的`AutoreleasingUnsafePointer<Type>`值
-  一个操作数是 `Type` 类型的左值的输入输出表达式，作为这个左值的内存地址传入
-  一个`Type[]`数组值，作为一个数组的起始指针传入，并且它的生命周期将在这个调用期间被延长


如果您这样定义了一个函数：

```swift
//Swift
func takesAConstPointer(x: CConstPointer<Float>) { /*...*/ }
```

那么您可以使用以下任何一种方式来调用这个函数：

```swift
//Swift
var x: Float = 0.0
var p: CConstPointer<Float> = nil

takesAConstPointer(nil)
takesAConstPointer(p)
takesAConstPointer(&x)
takesAConstPointer([1.0, 2.0, 3.0])
```

当函数被声明使用一个`CConstVoidPointer`参数，那么这个函数接受任何和`CConstPointer<Type>` 相似类型的`Type`操作数。
￼
如果您这样定义了一个函数：

```swift
//Swift
￼￼￼￼func takesAConstVoidPointer(x: CConstVoidPointer) { /* ... */ }
```

那么您可以使用以下任何一种方式来调用这个函数：

```swift
//Swift
var x: Float = 0.0, y: Int = 0
var p: CConstPointer<Float> = nil, q: CConstPointer<Int> = nil 

takesAConstVoidPointer(nil)
takesAConstVoidPointer(p)
takesAConstVoidPointer(q)
takesAConstVoidPointer(&x)
takesAConstVoidPointer(&y)
takesAConstVoidPointer([1.0, 2.0, 3.0]) 
takesAConstVoidPointer([1, 2, 3])
```

### 自动释放不安全指针

当一个函数被声明为接受`AutoreleasingUnsafePointer<Type>`参数时，这个函数可以接受下列任何一个类型作为参数：

* `nil`，作为空指针传入
* 一个`AutoreleasingUnsafePointer<Type>`值
* 其操作数是原始的，复制到一个临时的没有所有者的缓冲区的一个输入输出表达式，该缓冲区的地址传递给调用，并返回时，缓冲区中的值加载，保存，并重新分配到操作数。

**注意：这个列表没有包含数组。**


如果您这样定义了一个函数：

```swift
//Swift
func takesAnAutoreleasingPointer(x: AutoreleasingUnsafePointer<NSDate?>) { /* ... */ }
```

那么您可以使用以下任何一种方式来调用这个函数：

```swift
//Swift
var x: NSDate? = nil
var p: AutoreleasingUnsafePointer<NSDate?> = nil
￼￼￼￼￼
takesAnAutoreleasingPointer(nil)
takesAnAutoreleasingPointer(p)
takesAnAutoreleasingPointer(&x)
```

**注意：C 语言函数指针没有被 Swift 引进。**

<a name="global_constants"></a>
## 全局常量

在 C 和 Objective-C 语言源文件中定义的全局常量会自动地被 Swift 编译引进并做为 Swift 的全局常量。

<a name="preprocessor_directives"></a>
## 预处理指令

Swift 编译器不包含预处理器。取而代之的是，它充分利用了编译时属性，生成配置，和语言特性来完成相同的功能。因此，Swift 没有引进预处理指令。

### 简单宏

在 C 和 Objective-C，您通常使用的#define指令定义的一个宏常数，在 Swift，您可以使用全局常量来代替。例如：一个全局定义`#define FADE_ANIMATION_DURATION 0.35`，在 Swift 可以使用`let FADE_ANIMATION_DURATION = 0.35`来更好的表述。由于简单的用于定义常量的宏会被直接被映射成 Swift 全局量，Swift 编译器会自动引进在 C 或 Objective-C 源文件中定义的简单宏。

### 复杂宏

在 C 和 Objective-C 中使用的复杂宏在 Swift 中并没有与之对应的定义。复杂宏是那些不用来定义常量的宏，而是用来定义包含小括号（），函数的宏。您在 C 和 Objective-C 使用复杂的宏是用来避免类型检查的限制和相同代码的重复劳动。然而，宏也会产生Bug和重构的困难。在 Swift 中你可以直接使用函数和泛型来达到同样的效果。因此，在 C 和 Objective-C 源文件中定义的复杂宏在 Swift 是不能使用的。

### 编译配置

Swift 代码和 Objective-C 代码以不同的方式进行条件编译。Swift 代码可以根据生成配置的评价配进行有条件的编译。生成配置包括 `true` 和 `false` 字面值，命令行标志，和下表中的平台测试函数。您可以使用-D \<＃Flag＃\>指定命令行标志。

| 函数 | 有效参数 |
| --- | --- |
| os() | OSX, iOS |
| arch() | x86_64, arm, arm64, i386 |

>注意

>arch(arm) 的生成配置不会为64位 ARM 设备返回`true`，当代码运行在为32位的 iOS 模拟器器时，arch(i386) 的生成配置返回`true`。

一个简单的条件编译需要以下代码格式：

```swift
#if build configuration
	statements
#else
	statements
#endif
````

一个由零个或多个有效的 Swift 语句声明的`statements`，可以包括表达式，语句和控制流语句。您可以添加额外的构建配置要求，条件编译说明用 && 和 | | 操作符以及 ！ 操作符，添加条件控制块用 ＃elseif：

```swift
#if build configuration && !build configuration
	statements
#elseif build configuration
	statements
#else
	statements
#endif 
```

与 C 语言编译器的条件编译相反，Swift 条件编译语句必须完全是自包含和语法有效的代码块。这是因为 Swift 代码即使没有被编译，也要全部进行语法检查。 
