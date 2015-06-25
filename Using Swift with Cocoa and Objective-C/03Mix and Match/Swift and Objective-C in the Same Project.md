> 翻译：[haolloyin](https://github.com/haolloyin)

> 校对：[ChildhoodAndy](https://github.com/dabing1022)


# 在同个工程中使用 Swift 和 Objective-C
------------------------------

本节包含内容：

-   [Mix and Match 概述（Mix and Match Overview）](#mix_and_match_overview)
-   [在同个应用的 target 中导入（Importing Code from Within the Same App Target）](#importing_code_from_within_the_same_app_target)
-   [在同个 Framework 的 target 中导入（Importing Code from Within the Same Framework Target）](#importing_code_from_within_the_same_framework_target)
-   [导入外部 framework（Importing External Frameworks）](#importing_external_frameworks)
-   [在 Objective-C 中使用 Swift（Using Swift from Objective-C）](#using_swift_from_objective-c)
-   [为 Objective-C 接口重写 Swift 名称](#overriding_swift_names_for_Objective)
-   [Product 模块命名（Naming Your Product Module）](#naming_your_product_module)
-   [问题解决提示（Troubleshooting Tips and Reminders）](#troubleshooting_tips_and_reminders)



Swift 与 Objective-C 的兼容能力使你可以在同一个工程中同时使用两种语言。你可以用这种叫做 `mix and match` 的特性来开发基于混合语言的应用，可以用 Swift 的最新特性实现应用的一部分功能，并无缝地并入已有的 Objective-C 的代码中。

<a name="mix_and_match_overview"></a>
## Mix and Match 概述

Objective-C 和 Swift 文件可以在一个工程中并存，不管这个工程原本是基于 Objective-C 还是 Swift。你可以直接往现有工程中简单地添加另一种语言的源文件。这种自然的工作流使得创建混合语言的应用或框架 target，与用单独一种语言时一样简单。

混合语言的工作流程只有一点点区别，这取决于你是在写应用还是写框架。下面描述了普通的用两种语言在一个 target 中导入模型的情况，后续章节会有更多细节。

![DAG_2x.png](https://raw.githubusercontent.com/haolloyin/Welcome-to-Swift/translate/Using%20Swift%20with%20Cocoa%20and%20Objective-C/03Mix%20and%20Match/DAG_2x.png?raw=true)

<a name="importing_code_from_within_the_same_app_target"></a>
## 在同个应用的 target 中导入

如果你在写混合语言的应用，可能需要用 Swift 代码访问 Objective-C 代码，或者反之。下面的流程描述了在非框架 target 中的应用。

### 将 Objective-C 导入 Swift

在一个应用的 target 中导入一些 Objective-C 文件供 Swift 代码使用时，你需要依赖于 Objective-C 的桥接头文件（`bridging header`）来暴露给 Swift。当你添加 Swift 文件到现有的 Objective-C 应用（或反之）时，Xcode 会自动创建这些头文件。

![bridgingheader_2x.png](https://raw.githubusercontent.com/haolloyin/Welcome-to-Swift/translate/Using%20Swift%20with%20Cocoa%20and%20Objective-C/03Mix%20and%20Match/bridgingheader_2x.png?raw=true)

如果你同意，Xcode 会在源文件创建的同时生成头文件，并用 product 的模块名加上 `-Bridging-Header.h` 命名。关于 product 的模块名，详见 [Naming Your Product Module](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-XID_85)。

你应该编辑这个头文件来对 Swift 暴露出 Objective-C 代码。

#### 在同一 target 中将 Objective-C 代码导入到 Swift 中

1. 在 Objective-C 桥接头文件中，`import`任何你想暴露给 Swift 的头文件，例如：

```objective-c
#import "XYZCustomCell.h"
#import "XYZCustomView.h"
#import "XYZCustomViewController.h"
```

2. 确保在 `Build Settings` 中 Objective-C 桥接头文件的 `build setting` 是基于 Swfit 编译器，即 `Code Generation` 含有头文件的路径。这个路径必须是头文件自身的路径，而不是它所在的目录。

这个路径应该是你工程的相对路径，类似 `Info.plist` 在 `Build Settings` 中指定的路径。在大多数情况下，你不需要修改这个设置。

在这个桥接头文件中列出的所有 public 的 Objective-C 头文件都会对 Swift 可见。之后当前 target 的所有 Swift 文件都可以使用这些头文件中的方法，不需要任何 import 语句。用 Swift 语法使用这些 Objective-C 代码，就像使用系统自带的 Swift 类一样。

```swift
let myCell = XYZCustomCell()
myCell.subtitle = "A custom cell"
```

### 将 Swift 导入到 Objective-C

当你在将 Swift 代码导入到 Objective-C 中时，你依赖于 Xcode 生成的头文件来将这些文件暴漏给 Objective-C。这个自动生成的文件是一个 Objective-C 头文件，它包含了你的 target 中所有 Swift 代码中定义的接口。可以把这个 Objective-C 头文件看作 Swift 代码的 `umbrella header`。头文件名称以 product 模块名加 `-Swift.h` 来命名。（关于 product 的模块名，详见[Naming Your Product Module](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-XID_85)）。

默认情况下，生成的头文件包含了标记有`public`修饰符的 Swift 声明接口。它还包含那些打上，如果您的应用程序的目标有一个Objective-C的桥接头内部修改。标有private修饰符声明不会出现在所生成的报头。私人声明没有接触到Objective-C的，除非它们被明确标有@IBAction，@IBOutlet，或@objc为好。如果您的应用程序的目标是编译测试启用，单元测试目标可以访问任何声明与内部修饰，仿佛他们与公众修饰符通过预先@testable的产品模块导入语句声明。

你不需要做任何事情来生成这个头文件，只需要将它导入到你的 Objective-C 代码来使用它。注意这个头文件中的 Swift 接口包含了它所使用到的所有 Objective-C 类型。如果你在 Swift 代码中使用你自己的 Objective-C 类型，确保先将对应的 Objective-C 头文件导入到你的 Swift 代码中，然后才将 Swift 自动生成的头文件导入到 Objective-C .m 源文件中来访问 Swift 代码。

##### 在同一 target 中将 Swift 代码导入到 Objective-C 中

- 在相同 target 的 Objective-C .m 源文件中，用下面的语法来导入Swift 代码：

```objective-c
#import "ProductModuleName-Swift.h"
```

target 中任何 Swift 文件将会对 Objective-C .m 源文件可见，包括这个 import 语句。关于在 Objective-C 代码中使用 Swift 代码，详见 [Using Swift from Objective-C](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-XID_84)。

|              | 导入到 Swift | 导入到 Objective-C  |
| -------------|:-----------:|:------------:|
| Swift 代码    | 不需要import语句  | #import "ProductModuleName-Swift.h”  |
| Objective-C 代码     | 不需要import语句；需要 Objective-C bridging头文件| #import "Header.h"     |


<a name="importing_code_from_within_the_same_framework_target"></a>
## 在同个 Framework 的 target 中导入

如果你在写一个混合语言的框架，可能会从 Swift 代码访问 Objective-C 代码，或者反之。

### 将 Objective-C 导入 Swift

要将一些 Objective-C 文件导入到同个框架 target 的 Swift 代码中去，你需要将这些文件导入到 Objective-C 的 `umbrella header` 来供框架使用。

##### 在同一 framework 中将 Objective-C 代码导入到 Swift 中

确保将框架 target 的 `Build Settings > Packaging > Defines Module` 设置为 `Yes`。然后在你的 `umbrella header` 头文件中导入你想暴露给 Swift 访问的 Objective-C 头文件，例如：

```objective-c
#import <XYZ/XYZCustomCell.h>
#import <XYZ/XYZCustomView.h>
#import <XYZ/XYZCustomViewController.h>
```

Swift 将会看到所有你在 `umbrella header` 中公开暴露出来的头文件，框架 target 中的所有 Swift 文件都可以访问你 Objective-C 文件的内容，不需要任何 import 语句。

```swift
let myCell = XYZCustomCell()
myCell.subtitle = "A custom cell"
```

### 将 Swift 导入 Objective-C

要将一些 Swift 文件导入到同个框架的 target 的 Objective-C 代码去，你不需要导入任何东西到 `umbrella header` 文件，而是将 Xcode 为你的 Swift 代码自动生成的头文件导入到你的 Obj .m 源文件去，以便在 Objective-C 代码中访问 Swift 代码。

##### 在同一 framework 中将 Swift 代码导入到 Objective-C 中

确保将框架 target 的 `Build Settings > Packaging` 中的 `Defines Module` 设置为 `Yes`。用下面的语法将 Swift 代码导入到同个框架 target 下的 Objective-C .m 源文件去。

```objective-c
// OBJECTIVE-C
#import <ProductName/ProductModuleName-Swift.h>
```

这个 import 语句所包含的 Swift 文件都可以被同个框架 target 下的 Objective-C .m 源文件访问。关于在 Objective-C 代码中使用 Swift 代码，详见 [Using Swift from Objective-C](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-XID_84)。

|              | 导入到 Swift | 导入到 Objective-C  |
| -------------|:-----------:|:------------:| 
| Swift 代码    | 不需要import语句  | #import "ProductName/ProductModuleName-Swift.h"  |
| Objective-C 代码     | 不需要import语句；需要 Objective-C umbrella头文件| #import "Header.h"     |


<a name="importing_external_frameworks"></a>
## 导入外部 Framework

你可以导入外部框架，不管这个框架是纯 Objective-C，纯 Swift，还是混合语言的。import 外部框架的流程都是一样的，不管这个框架是用一种语言写的，还是包含两种语言。当你导入外部框架时，确保 `Build Setting > Pakaging > Defines Module` 设置为 `Yes`。

用下面的语法将框架导入到不同 target 的 Swift 文件中：

```swift
import FrameworkName
```

用下面的语法将框架导入到不同 target 的 Objective-C .m 文件中：

```objective-c
@import FrameworkName;
```


|           | 导入到 Swift | 导入到 Objective-C  |
| -------------|:-----------:|:------------:| 
|任意语言框架 | import FrameworkName | @import FrameworkName; |

<a name="using_swift_from_objective-c"></a>
## 在 Objective-C 中使用 Swift

当你将 Swift 代码导入 Objective-C 之后，便可用常规的 Objective-C 语法来使用 Swift 类。

```objective-c
MySwiftClass *swiftObject = [[MySwiftClass alloc] init];
[swiftObject swiftMethod];
```

Swift 的类或协议必须用 `@objc`属性来标记，以便在 Objective-C 中可访问。这个 属性告诉编译器这块 Swift 代码可以从 Objective-C 代码中访问。如果你的 Swift 类是 Objective-C 类的子类，编译器会自动为你添加 `@objc`。详见 [Swift Type Compatibility](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html#//apple_ref/doc/uid/TP40014216-CH4-ID53)。

你可以访问在 Swift 类或协议中使用用`@objc`属性标记的任何对象，只要该对象与 Objective-C 兼容。不包括以下 Swift 独有的特性：

-  范型（Generics）

-  元组（Tuples）

-  Swift 中定义的枚举不包括`Int`原始值类型（Enumerations defined in Swift without Int raw value type）

-  Swift 中定义的结构体（Structures defined in Swift）   

-  Swift 中定义的顶层函数（Top-level functions defined in Swift）

-  Swift 中定义的全局变量（Global variables defined in Swift）

-  Swift 中定义的类型别名（Typealiases defined in Swift）

-  Swift风格可变参数（Swift-style variadics）

-  嵌套类型（Nested types）

-  柯里化函数（Curried functions）

例如，使用范型类型作为参数，或者返回元组的方法将不能在 Objective-C 中使用。

> 注意
> 你不能在 Objective-C 继承一个 Swift 类。

### 在 Objective-C 头文件中引用 Swift 类

这样前向声明 Swift 类：

```objective-c
// OBJECTIVE-C
// MyObjective-CClass.h

@class MySwiftClass;

@interface MyObjective-CClass : NSObject
- (MySwiftClass *)returnSwiftObject;
/* ... */
@end
```

<a name="overriding_swift_names_for_Objective-C_interfaces"></a>
## 为 Objective-C 接口重写 Swift 名称

Swift 编译器自动的将 Objective-C 代码作为常规 Swift 代码导入。它将 Objective-C 的类工厂方法作为 Swift 构造器导入，以及将 Objective-C 的枚举类型名称截断处理。

在你的代码中也许存在不能够被自动处理的边界情况。如果你需要更改导入到 Swift 中的 Objective-C 方法，枚举，或者可选 set 值，你可以使用`NS_SWIFT_NAME`宏来自定义导入的声明。

### 类工厂方法

如果 Swift 编译器无法识别类工厂方法，你可以使用`NS_SWIFT_NAME`宏，来正确导入构造器的 Swift 签名。例如：

```objective-c
+ (instancetype)recordWithRPM:(NSUInteger)RPM NS_SWIFT_NAME(init(RPM:));
```

如果 Swift 编译器错误的将一个方法识别为类工厂方法，你可以使用`NS_SWIFT_NAME`宏，来正确导入构造器的 Swift 签名。例如：

```objective-c
 (id)recordWithQuality:(double)quality NS_SWIFT_NAME(record(quality:));
```

### 枚举

默认情况下，Swift 将枚举值的名称前缀做截断来导入枚举。如果要自定义枚举值的名称，你可以使用`NS_SWIFT_NAME`宏来传递 Swift 枚举值名称。例如：

```objective-c
typedef NS_ENUM(NSInteger, ABCRecordSide) {
  ABCRecordSideA,
  ABCRecordSideB NS_SWIFT_NAME("FlipSide"),
};
```

<a name="naming_your_product_module"></a>
## Product 模块命名

Xcode 为 Swift 代码生成的头文件的名称，以及 Xcode 创建的 Objective-C 桥接头文件名称，都是从你的 product 模块名生成的。默认你的 product 模块名和 product 名一样。然而，如果你的 product 名有特殊字符（nonalphanumeric，非数字、字母的字符），例如点号，那么它们会被下划线（`_`）替换之后作为你的 product 模块名。如果 product 名以数字开头，那么第一个数字会用下划线替换掉。

你可以给 product 模块名提供一个自定义的名称，Xcode 会用这个名称来命名桥接的和自动生成的头文件。你只需要在修改在 `build setting` 中的 `Product Module Name` 即可。

<a name="troubleshooting_tips_and_reminders"></a>
## 问题解决提示

- 把 Swift 和 Objective-C 文件看作相同的代码集合，并注意命名冲突。
- 如果你使用了框架，确保在`Packaging`下的`Defines Module`编译设置被设置为 `Yes`。
- 如果你使用了 Objective-C 桥接头文件，确保 Swift 编译器中 Objective-C 桥接头文件的编译设置`Code Generation`有一个与项目相关的头文件的路径。这个路径必须是头文件自身的路径，而不是它所在的目录。
- Xcode 使用你的工程模块名，而不是以`target`的名称来命名 Objective-C 桥接头文件以及为 Swift 代码 自动生成的头文件。详见 [Naming Your Product Module](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-ID138)。
- 为了在 Objective-C 中可用， Swift 类必须是 Objective-C 类的子类，或者用 `@objc` 标记。
- 当你将 Swift 导入到 Objective-C 中时，记住 Objective-C 不会将 Swift 独有的特性转化成 Objective-C 对应的特性。详见列表 [Using Swift from Objective-C](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-ID136)。
- 如果你在 Swift 代码中使用你自己的 Objective-C 类型，确保先将对应的 Objective-C 头文件导入到你的 Swift 代码中，然后才将 Swift 自动生成的头文件导入到 Objective-C .m 源文件中来访问 Swift 代码。
- 用`private`修饰符标记的 Swift 声明不会出现在自动生成的头文件中。私有声明不会暴漏给 Objective-C，除非它们被明确标记有`@IBAction`，`@IBOutlet`或者`@objc`等。
- 对于应用 targets 而言，如果有 Objective-C 桥接头文件时，被`internal`修饰符标记的声明会出现在自动产生的头文件中。
- 对于框架 targets 而言，只有被`public`修饰符标记的声明才会出现在自动生成的头文件中。你仍然可以在框架中的 Objective-C 部分使用被`internal`修饰符标记的 Swift 方法和属性，只要它们声明所在的类继承自 Objective-C 类。关于访问级别修饰符的更多信息，请查看[The Swift Programming Language](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html#//apple_ref/doc/uid/TP40014097)中的[访问控制(Access Control)](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html#//apple_ref/doc/uid/TP40014097-CH41)
