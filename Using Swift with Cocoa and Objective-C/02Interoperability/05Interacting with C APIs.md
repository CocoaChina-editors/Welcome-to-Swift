# 与 C 的 API 交互

作为与Objective-C语言的互用性的一部分，Swift也对一些C语言的类型和特性保持了兼容性。如果你的代码有需要，Swift也提供了一些方式来使用常见的C结构和模式。

## 基本数据类型

Swift提供了一些与C语言基本类型如`char`,`int`,`float`和`double`等的对应类型。然而，这些类型和Swift核心基本类型之间不能进行隐式转换，如Int。因此，只有你的代码明确要求时才使用这些类型，其它任何可能的情况下都应该使用Int。

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

任何用宏`NS_ENUM`来声明的C风格的枚举，都会被Swfit导入为一个Swfit枚举类型。无论枚举值是在系统框架还是在自己的代码中定义的，当它们导入到Swift时，它们的前缀名将被截掉。

例如，看这个Objective-C枚举的声明：

````
    //Objective-C
    typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
        UITableViewCellStyleDefault,
        UITableViewCellStyleValue1,
        UITableViewCellStyleValue2,
        UITableViewCellStyleSubtitle
    };

````

在Swift中，会被导入为这样：

````
    //Swift
    enum UITableViewCellStyle: Int {
        case Default
        case Value1
        case Value2
        case Subtitle
    }

````

当你需要使用一个枚举值时，使用以点（.）开头的枚举名称：

````
    //Swift
    let cellStyle: UITableViewCellStyle = .Default

````

## 选项集

对使用宏`NS_OPTIONS`声明的C风格的枚举，Swift会把它导入为一个Swfit选项集类型。选项集像枚举一样，会把前缀截掉，只剩下选项值名称。

例如，看这个Objective-C选项的声明：

```
	//Objective-C
	typedef NS_OPTIONS(NSUInteger, NSJSONReadingOptions) {
		NSJSONReadingMutableContainers = (1UL << 0),
		NSJSONReadingMutableLeaves = (1UL << 1),
		NSJSONReadingAllowFragments = (1UL << 2)
	};
```

在Swift中，它被导入为这样：

```
	//Swift
	struct NSJSONReadingOptions : OptionSetType {
		init(rawValue: UInt)
    
    	static var MutableContainers: NSJSONReadingOptions { get }
    	static var MutableLeaves: NSJSONReadingOptions { get }
    	static var AllowFragments: NSJSONReadingOptions { get }
    }
```

在Objective-C中，一个选项集是整数值的一个位掩码。你可以使用按位或操作符（`|`）来组合选项值，使用按位与操作符（`&`）以检测选项值。创建一个选项集，可以使用常量值或者表达式。一个空的选项集使用常数`0`来表示。

在Swift中，选项集使用一个遵循`OptionSetType`协议的结构体来表示，其中每个选项值都是一个静态变量。选项集类似于Swift的集合类型`Set`，你可以用`insert(_:)`或者`unionInPlace(_:)`方法来添加选项值，用`remove(_:)`或者`subtractInPlace(_:)`方法来删除选项值，用`contains(_:)`方法来检测选项值。创建一个选项集的值可以使用一个数组字面量，里面的选项值像枚举一样都用点（`.`）开头。创建一个空的选项集可以使用一个空的数组字面量，也可以调用默认初始化函数。

```
	//Swift
	let options: NSDataBase64EncodingOptions = [
		.Encoding76CharacterLineLength,
    	.EncodingEndLineWithLineFeed
    ]
	let string = data.base64EncodedStringWithOptions(options)
```

## 共用体

Swift仅部分支持C的共用体（union）类型。在导入混有C的共用体或者位段（bitfields）的类型时，例如Foundation的`NSDecimal`类型，Swift不能存取不支持的字段。但是，参数和/或返回值为这些类型的C和Objective-C的API是能够在Swift中使用的。

## 指针

Swift尽可能避免让您直接访问指针。然而，当您需要直接操作内存的时候，Swift也为您提供了多种指针类型。下面的表使用`Type`作为类型名称的占位符。

对于返回类型，变量和参数，使用如下形式：

| C 句法 | Swift 句法 |
| ------ | ------ |
| const Type * | UnsafePointer\<Type\> |
| Type * | UnsafeMutablePointer\<Type\> |

对于类（class）类型，使用如下形式：

| C 句法 | Swift 句法 |
| ------ | ------ |
| Type * const * | UnsafePointer\<Type\> |
| Type * __strong * | UnsafeMutablePointer\<Type\> |
| Type ** | AutoreleasingUnsafeMutablePointer\<Type\> |


### 常量指针

当一个方法被声明为接受`UnsafePointer<Type>`参数时，这个函数可以接受下列任何一个类型作为参数：

* `nil`，作为空指针传入；
* 一个`UnsafePointer<Type>`，`UnsafeMutablePointer<Type>`， 或者`AutoreleasingUnsafeMutablePointer<Type>`的值，在必要情况下会转换成`UnsafePointer<Type>`的值；
* 一个`String`类型的值，如果`Type`是`Int8`或者`UInt8`的话。该字符串会自动在一个缓冲区内被转换为UTF8，该缓冲区在本次调用期间有效；
* 一个左值操作数为`Type`类型的输入输出表达式，传入的是这个左值的内存地址；
* 一个`[Type]`值，传入该数组的起始指针，并且它的生命周期将在本次调用期间被延长。

如果您这样定义了一个函数：

````
    //Swift
    func takesAPointer(x: UnsafePointer<Float>) { /*...*/ }
````

那么您可以使用以下任何一种方式来调用这个函数：

````
    //Swift
    var x: Float = 0.0
	var p: UnsafePointer<Float> = nil
	takesAPointer(nil)
	takesAPointer(p)
	takesAPointer(&x)
	takesAPointer([1.0, 2.0, 3.0])
````

如果函数被声明为使用一个`UnsafePointer<Void>`参数，那么这个函数接受任何`Type`的`UnsafePointer<Type>`类型的操作数。
￼
如果您这样定义了一个函数：

````
    //Swift
    func takesAVoidPointer(x: UnsafePointer<Void>)  { /* ... */ }
````
那么您可以使用以下任何一种方式来调用这个函数：
￼
````
    //Swift
    var x: Float = 0.0, y: Int = 0
	var p: UnsafePointer<Float> = nil, q: UnsafePointer<Int> = nil
	takesAVoidPointer(nil)
	takesAVoidPointer(p)
	takesAVoidPointer(q)
	takesAVoidPointer(&x)
	takesAVoidPointer(&y)
	takesAVoidPointer([1.0, 2.0, 3.0] as [Float])
	let intArray = [1, 2, 3]
	takesAVoidPointer(intArray)
````

### 可变指针

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
