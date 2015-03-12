> 翻译：[wongzigii](https://github.com/wongzigii)

> 校对：[ChildhoodAndy](https://github.com/dabing1022)

#与 Cocoa 数据类型共舞
-------------

本节内容包括：

-  [字符串（Strings）](#strings)
-  [数字（Numbers）](#numbers)
-  [类集合（Collection Classes）](#collection_classes)
-  [Foundation数据类型（Foundation Data Types）](#foundation_data_types)
-  [Foundation函数（Foundation Functions）](#foundation_functions)
-  [Core Foundation](#core_foundation)

作为对 Objective-C 互用性（互操作性）的一部分，Swift提供快捷高效的方式来处理 Cocoa 数据类型。

Swift 会自动将一些 Objective-C 类型转换为 Swift 类型，以及将 Swift 类型转换为 Objective-C 类型。在 Objective-C 和 Swift 中也有一些具有互用性的数据类型。那些可转换的数据类型或者具有互用性的数据类型被称为*bridged*数据类型。举个例子，在 Swift 中，您可以将一个`Array`值传递给一个要求为`NSArray`对象的方法。你也可以转换一个 bridged 类型和它的副本。当你使用`as`转换 bridged 类型或者那些由常量和变量所提供的类型时，Swift 会桥接它们的数据类型。

Swift 也提供一种简单便捷的覆盖方法来连接 Foundation 的数据类型，在后面的 Swift 语言中，你能在它的句法中感受到自然和统一。

<a name = "strings"></a>
##字符串

Swift会在`String`类型和`NSString`类型中自动转换。这意味着在可以使用`NSString`对象的地方，您可以使用一个属于 Swift 的`String`类型代替它，这样做会同时拥有它们数据类型的特点，`String`类型的插值，基于Swift设计的APIs以及`NSString`类更广的适用范围。因此，您几乎不必再在你的代码中使用`NSString`类。事实上，当 Swift 接入 Objective-C APIs 时，它将把所有`NSString`类型替换为`String`类型。当您在您的Objective-C代码中使用 Swift 类时，接入的API会将所有`String`类型替换成`NSString`类型。

为了允许字符串转换，只需接入 Foundation。举个例子，您在 Swift 的一个字符串中调用了`capitalizedString`--一个`NSString`类的方法，此后 Swift 会自动将`String`转换为一个`NSString`对象并调用方法。这个方法甚至会返回一个 Swift 的`String`类型，因为它在接入的时候被替换了。

```swift
import Foundation
let greeting = "hello, world!"
let capitalizedGreeting = greeting.capitalizedString
// capitalizedGreeting: String = Hello, World!
```
如果您确实需要用到一个`NSString`对象，您可以用一个 Swift 的`String`值并转换它。`String`类型总是可以从一个`NSString`对象转换为一个Swift的`String`的值，因此，再没有必要去使用一个可选的类型转换器`()as?)`。您也可以在一个字符串中通过定义常量和变量来创建一个`NSString`对象。

```swift
import Foundation
let myString: NSString = "123"
if let integerValue = (myString as String).toInt()){
    println("\(myString) is the integer \(integerValue)") 
}
```

###本地化
在Objective-C中，常用`NSLocalizedString`类的宏来定位一个字符串。这集合的宏包括`NSLocalizedStringFromTableInBundle`和`NSLocalizedStringWithDefaultValue`。而在Swift中，只用一个函数就可以实现跟整个`NSLocalizedString`集一样的功能，即`NSLocalizedString(key:tableName:bundle:value:comment:)`。这个`NSLocalizedString`函数分别为`tableName`，`bundle`和`value`参数提供了一个默认值。你可以用它来替换宏。


<a name = "numbers"></a>
##数字

Swift会自动将已确定的数字类型`Int`和`Float`转换为`NSNumber`。这样的转换允许你基于其中一种类型创建一个`NSNumber`：

```
let n = 42
let m: NSNumber = n
```

你也能传递一个`Int`类型的值，比如传递给一个要求为`NSNumber`类型的参数。同时需要注意的是，`NSNumber`可以包含多种不同的类型，因此您不能把它传递给单一的一个`Int`值。

下面所列出的类型都会自动转换为`NSNumber`：

* `Int`
* `UInt`
* `Float`
* `Double`
* `Bool`


<a name = "collection_classes"></a>
##类集合

Swift 会自动将`NSArray`和`NSDictionary`类转换为Swift里等价的类。这意味着你将受益于Swift强大的算法和得天独厚的语法来处理集合--可互相转换的 Foundation 和 Swift 集合类型。

###数组

Swift 会在`Array`类型和`NSArray`类型中自动转换。当你从一个 Swift 数组转换到一个`NSArray`对象，转换后的数组是一个`AnyObject[]`类型的数组。如果某个对象是 Objective-C 或者 Swift 类的实例，或者这个对象可以转换成另一种类型，那么这个对象则属于`AnyObject`类型的对象。你可以将任一`NSArray`对象转换成一个 Swift 数组，因为所有 Objective-C 的对象都是`AnyObject`类型的。正因如此，Swift 的编译器会在接入 Objective-C APIs 的时候将`NSArray`类替换成`AnyObject[]`。

当你将一个`NSArray`对象转换成一个 Swift 数组后，你也可以将数组强制类型转换成一个特定的类型。与从`NSArray`类转换到`AnyObject[]`不同的是，从`AnyObject`类型的对象转换成明确的类型并不会保证成功。由于直到运行时编译器才知道`AnyObject`的对象能否被强制转换为特定的类型，因此，从`AnyObject[]`转换为`SomeType[]`会返回一个 optional 的值。举个例子，如果你知道一个Swift数组只包含`UIView`类的实例(或者一个`UIView`类的子类)，你可以将`AnyObject`类型的数组元素强制转换为`UIView`对象。如果Swift数组中的元素在运行时不是`UIView`类型的对象，那么转换则会返回`nil`。

```swift
let swiftyArray = foundationArray as AnyObject[]
if let downcastedSwiftArray = swiftArray as? UIView[] {
    // downcastedSwiftArray contains only UIView objects
}
```

你也可以在for循环中将NSArray对象定向地强制转换为特定类型的Swift数组:

```swift
for aView: UIView! in foundationArray {
     // aView is of type UIView
}
```

>注意：这种转换是强制转换，如果转换不成功则会在运行时产生错误信息。

当你从 Swift 数组转换为`NSArray`对象时，Swift 数组里的元素必须是属于`AnyObject`的。例如，一个`Int[]`类型的 Swift 数组包含`Int`结构的元素。`Int`类型并不是一个类的实例，但由于`Int`类型转换成了`NSNumber`类，`Int`类型属于`AnyObject`类型的。因此，你可以将一个`Int[]`类型的Swift数组转换为`NSArray`对象。如果 Swift 数组里的一个元素不属于`AnyObject`类型，那么在运行时就会产生错误。

你也可以从 Swift 数组中创建一个`NSArray`对象。当你将一个常量或变量定义为一个`NSArray`对象并分配一个数组给它作为实例变量时，Swift 将会创建 `NSArray`对象，而不是 Swift 数组。

```swift
let schoolSupplies: NSArray = ["Pencil", "Eraser", "Notebkko"]
// schoolSupplies is an NSArray object containing NSString objects
```
上面的例子中，Swift 数组包含包含三个`String`字符串。由于从`String`类型转换为`NSString`类，数组字面量被转换成一个`NSArray`对象，并成功分配给`schoolSupplies`变量。

当您在 Objective-C 代码中使用 Swift 类或者协议时，接入的API会将全部所有类型的Swift数组代替为`NSArray`。若您将一个`NSArray`对象传递给Swift的API并要求数组元素为一个新的类型，运行时就会产生错误。如果 Swift API 返回一个不能被转换为`NSArray`类型的 Swift 数组，错误也会随之产生。

###字典
敬请期待


<a name = "foundation_data_types"></a>
##Foundation数据类型
Swift 也提供一种简单便捷的覆盖方法来连接定义在 Foundation 框架中的数据类型。在`NSSize`和`NSPoint`中使用覆盖方法，在剩下的 Swift 语言中，你能在它的句法中感受到自然和统一。比如，你可以使用如下语法创建一个`NSSize`类型的结构:

```swift
let size = NSSize(width: 20, height: 40)
```

覆盖方法也允许你以一种自然的方式调用 Foundation 的结构函数。

```swift
let rect = NSRect(x: 50, y: 50, width: 100, height: 100)
let width = rect.width    // equivalent of NSWidth(rect)
let maxX = rect.maxY      // equivalent of NSMaxY(rect)
```
Swift可以将`NSUInteger`和`NSInteger`转换为`Int`类型。这些类型都会在 Foundation APIs 中变为`Int`类型。在 Swift 中`Int`常被尽可能地用以连贯性，同时当你要求一个无符号整数类型时，`UInt`类型总是可使用的。


<a name = "foundation_functions"></a>
##Foundation函数

在 Swift 中，`NSLog`可在系统控制台输出信息。您可以像在 Objective-C 中使用过的语法格式那样使用此函数。

```swift
NSLog("%.7f", pi)         // Logs "3.1415927" to the console
```
同时，Swift 也提供像`print`和`println`那样的输出函数。多归于 Swift 的字符插值机制才让这些函数简单，粗暴，多效。这些函数不会在系统控制台输出信息，但在需要调用的时候却是可用的。

Swift 中不再存在`NSAssert`函数，取而代之的是`assert`函数。


<a name = "core_foundation"></a>
##Core Foundation

Swift中的 Core Foundation 类型是一个成熟的类。当出现内存管理注释时，Swift 会自动地管理 Core Foundation 对象的内存，这其中包括你实例化了的 Core Foundation 对象。在 Swift 中，你可以自由变换 Fundation 和 Core Foundation 类型。如果你想先转换为桥接 Foundation 类型时，你也可以桥接一些 toll-free bridged Core Foundation 类型到 Swift 标准库类型。

###重定义类型

当 Swift 导入 Core Foundation 类型时，编译器会重映射导入的类型名字。编译器会从每个类型名字的末端移除 *Ref*，这是因为所有的 Swift 类都属于引用类型，因此后缀是多余的。

Core Foundation 中的`CFTypeRef`类型会对`Anyobject`类型重映射。所以你以前使用的`CFTypeRef`，现在该换成`AnyObject`了。

###内存管理对象

在 Swift 中，从 annotated APIs 返回的 Core Foundation 对象能够自动进行内存管理--你不再需要调用自身的`CFRetain`，`CFRelease`，或者`CFAutorelease`函数。如果你从自身的C函数和 Objective-C 方法中返回一个 Core Foundation 对象，你需要用`CF_RETURNS_RETAINED`或者`CF_RETURNS_NOT_RETAINED`注释这个对象。当 Swift 代码中包含这些 APIs 时，编译器会在编译时自动调用内存管理。如果你只调用那些不会间接返回 Core Foundation 对象的 annotated APIs，那么现在你可以跳过本节的剩余部分了。否则，下面我们继续学习非托管的 Core Foundation 对象。

###非托管对象

当 Swift 导入 unannotated 的APIs时，编译器将不会自动地对返回的 Core Foundation 对象进行内存管理托管。Swift 将这些返回的 Core Foundation 对象封闭在一个`Unmanaged<T>`结构中。那些间接返回 Core Foundation 的对象也是非托管的。举个例子，这里有一个 unannotated 的 C 函数:

```swift
CFStringRef StringByAddingTwoStrings(CFStringRef string1, CFStringRef string2)
```

这里说明了Swift是怎么导入的:

```swift
func StringByAddingTwoStrings(CFString!, CFString!) -> Unmanaged<CFString>!
```
假设您从 unannotated APIs 接收了非托管的对象，在使用它之前，你必须将它转换为能够内存管理的对象。在这方面，Swift 可以帮你进行内存管理而不用自己动手。同时，`Unmanaged<T>`结构也提供了两个方法来把一个非托管对象转换为一个可内存管理的对象--`takeUnretainedValue()`方法和`takeRetainedValue()`方法。这两个方法会返回原始的，非封闭的对象类型。您可以根据您实际调用的APIs返回的unretained或retained的对象，来选择哪一方法更合适。

比如，假设这里有一个 C 函数，这个函数在返回值前不会释放`CFString`对象。在使用这个对象前，您使用`takeUnretainedValue()`函数，以将它转换为一个能够内存管理托管的对象。

```swift
let memoryManagedResult = StringByAddingTwoStrings(str1, str2).takeUnretainedValue()
// memoryManagedResult is a memory managed CFString
```

您也可以在一个非托管的对象中使用`retain()`，`release()`和`autorelease()`方法，但是这种做法并不值得推荐。
