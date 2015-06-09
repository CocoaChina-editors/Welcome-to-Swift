> 翻译：[wongzigii](https://github.com/wongzigii)、[星夜暮晨](https://github.com/SemperIdem)

> 校对：[ChildhoodAndy](https://github.com/dabing1022)

#与 Cocoa 数据类型共舞
-------------

本节内容包括：

-  [字符串（Strings）](#strings)
-  [数值（Numbers）](#numbers)
-  [集合类（Collection Classes）](#collection_classes)
-  [错误（Errors）](#errors)
-  [Foundation数据类型（Foundation Data Types）](#foundation_data_types)
-  [Foundation函数（Foundation Functions）](#foundation_functions)
-  [Core Foundation](#core_foundation)

作为对 Objective-C 互用性（互操作性）的一部分，Swift提供快捷高效的方式来处理 Cocoa 数据类型。

Swift 会自动将一些 Objective-C 类型转换为 Swift 类型，以及将 Swift 类型转换为 Objective-C 类型。在 Objective-C 和 Swift 中也有一些具有互用性的数据类型。那些可转换的数据类型或者具有互用性的数据类型被称为*bridged*数据类型。举个例子，在 Swift 中，我们可以将一个`Array`值传递给一个要求为`NSArray`对象的方法。我们也可以转换一个 bridged 类型和它的副本。当我们使用`as`转换 bridged 类型或者那些由常量和变量所提供的类型时，Swift 会桥接它们的数据类型。

Swift 也提供一种简单便捷的覆盖方法来连接 Foundation 的数据类型，在后面的 Swift 语言中，我们能在它的句法中感受到自然和统一。

<a name = "strings"></a>
##字符串

Swift会在`String`类型和`NSString`类型中自动转换。这意味着在可以使用`NSString`对象的地方，我们可以使用一个属于 Swift 的`String`类型代替它，这样做会同时拥有它们数据类型的特点，比如说`String`类型的字符串插值，基于Swift设计的API，以及`NSString`类的多种功能。因此，我们几乎不必再在我们的代码中使用`NSString`类。事实上，当 Swift 接入 Objective-C API 时，它将把所有`NSString`类型替换为`String`类型。当我们在我们的Objective-C代码中使用 Swift 类时，接入的API会将所有`String`类型替换成`NSString`类型。

为了允许字符串转换，只需导入Foundation框架。举个例子，我们在 Swift的一个字符串中可以访问`capitalizedString`，这是`NSString`类的一个属性，然后 Swift 会自动将`String`转换为一个`NSString`对象来访问这个属性。这个属性甚至会返回一个 Swift的`String`类型，因为它在导入的时候被替换了。

```swift
import Foundation
let greeting = "hello, world!"
let capitalizedGreeting = greeting.capitalizedString
// capitalizedGreeting: String = Hello, World!
```
如果我们确实需要用到一个`NSString`对象，我们可以用一个 Swift 的`String`值并转换它。`String`类型总是可以从一个`NSString`对象转换为一个Swift的`String`的值，因此，再没有必要去使用一个可选的类型转换器(`as?`)。我们也可以在一个字符串中通过定义常量和变量来创建一个`NSString`对象。

```swift
import Foundation
let myString: NSString = "123"
if let integerValue = Int(myString as String) {
    print("\(myString) is the integer \(integerValue)")
}
// prints "123 is the integer 123"
```

###本地化
在Objective-C中，常用`NSLocalizedString`类的宏来定位一个字符串。这集合的宏包括`NSLocalizedString`，` NSLocalizedStringFromTable`，`NSLocalizedStringFromTableInBundle`，和`NSLocalizedStringWithDefaultValue`。而在Swift中，只用一个函数就可以实现跟整个`NSLocalizedString`集一样的功能，即`NSLocalizedString(key:tableName:bundle:value:comment:)`。这个`NSLocalizedString`函数分别为`tableName`，`bundle`和`value`参数提供了一个默认值。我们可以用它来替换宏。


<a name = "numbers"></a>
##数值

Swift会自动将已确定的数值类型`Int`和`Float`转换为`NSNumber`。这样的转换允许我们基于其中一种类型创建一个`NSNumber`：

```
let n = 42
let m: NSNumber = n
```

我们也能传递一个`Int`类型的值，比如传递给一个要求为`NSNumber`类型的参数。同时需要注意的是，`NSNumber`可以包含多种不同的类型，因此我们不能把它传递给单一的一个`Int`值。

下面所列出的类型都会自动转换为`NSNumber`：

* `Int`
* `UInt`
* `Float`
* `Double`
* `Bool`


<a name = "collection_classes"></a>
##集合类

Swift 会自动将`NSArray`、`NSSet`和`NSDictionary`类转换为Swift里等价的类：`Array`、`Set`和`Dictionary`。这意味着我们将受益于Swift强大的算法和得天独厚的语法来处理集合--可互相转换的 Foundation 和 Swift 集合类型。

###数组(Arrays)

Swift 会在`Array`类型和`NSArray`类型中自动转换。当我们从一个 Swift 数组转换成一个`NSArray`对象后，转换后的结果则是一个`[ObjectType]`类型的数组。如果`NSArray`对象没有指明一个确切的参数类型，那么它将会转换成`[AnyObject]`类型的Swift数组。

比如说，我们看到以下Objective-C声明：

```
@property NSArray<NSDate *>* dates;
- (NSArray<NSDate *> *)datesBeforeDate:(NSDate *)date;
- (void)addDatesParsedFromTimestamps:(NSArray<NSString *> *)timestamps;
```

那么，转换为Swift，则是这样子的：
```
var dates: [NSDate]
func datesBeforeDate(date: NSDate) -> [NSDate]
func addDatesParsedFromTimestamps(timestamps: [String])
```

如果某个对象是 Objective-C 或者 Swift 类的实例，或者这个对象可以转换成另一种类型，那么这个对象则属于`AnyObject`类型的对象。我们可以将任一`NSArray`对象转换成一个 Swift 数组，因为所有 Objective-C 的对象都是`AnyObject`类型的。正因如此，Swift 的编译器会在接入 Objective-C APIs 的时候将`NSArray`类替换成`AnyObject[]`。

当我们将一个`NSArray`对象转换成一个 Swift 数组后，我们也可以将数组强制类型转换成一个特定的类型。与从`NSArray`类转换到`AnyObject[]`不同的是，从`AnyObject`类型的对象转换成明确的类型并不会保证成功。由于直到运行时编译器才知道`AnyObject`的对象能否被强制转换为特定的类型，因此，从`AnyObject[]`转换为`SomeType[]`会返回一个 optional 的值。举个例子，如果我们知道一个Swift数组只包含`UIView`类的实例(或者一个`UIView`类的子类)，我们可以将`AnyObject`类型的数组元素强制转换为`UIView`对象。如果Swift数组中的元素在运行时不是`UIView`类型的对象，那么转换则会返回`nil`。

```swift
let swiftyArray = foundationArray as AnyObject[]
if let downcastedSwiftArray = swiftArray as? UIView[] {
    // downcastedSwiftArray contains only UIView objects
}
```

我们也可以在for循环中将NSArray对象定向地强制转换为特定类型的Swift数组:

```swift
for aView: UIView! in foundationArray {
     // aView is of type UIView
}
```

>注意：这种转换是强制转换，如果转换不成功则会在运行时产生错误信息。

当我们从 Swift 数组转换为`NSArray`对象时，Swift 数组里的元素必须是属于`AnyObject`的。例如，一个`Int[]`类型的 Swift 数组包含`Int`结构的元素。`Int`类型并不是一个类的实例，但由于`Int`类型转换成了`NSNumber`类，`Int`类型属于`AnyObject`类型的。因此，我们可以将一个`Int[]`类型的Swift数组转换为`NSArray`对象。如果 Swift 数组里的一个元素不属于`AnyObject`类型，那么在运行时就会产生错误。

我们也可以从 Swift 数组中创建一个`NSArray`对象。当我们将一个常量或变量定义为一个`NSArray`对象并分配一个数组给它作为实例变量时，Swift 将会创建 `NSArray`对象，而不是 Swift 数组。

```swift
let schoolSupplies: NSArray = ["Pencil", "Eraser", "Notebkko"]
// schoolSupplies is an NSArray object containing NSString objects
```
上面的例子中，Swift 数组包含包含三个`String`字符串。由于从`String`类型转换为`NSString`类，数组字面量被转换成一个`NSArray`对象，并成功分配给`schoolSupplies`变量。

当我们在 Objective-C 代码中使用 Swift 类或者协议时，接入的API会将全部所有类型的Swift数组代替为`NSArray`。若我们将一个`NSArray`对象传递给Swift的API并要求数组元素为一个新的类型，运行时就会产生错误。如果 Swift API 返回一个不能被转换为`NSArray`类型的 Swift 数组，错误也会随之产生。

###集合(Sets)

除了数组以外，Swift还会自动在`Set`类型和`NSSet`类之间进行转换。当我们将一个带有参数类型的`NSSet`对象转换为Swift集合之后，得到的结果是`Set<ObjectType>`类型的集合。而如果`NSSet`对象没有指明其参数类型，那么它将会转换为`Set<AnyObject>`类型的Swift集合。

比如说，我们看到以下Objective-C声明：

```
@property NSSet<NSString *>* words;
- (NSSet<NSString *> *)wordsMatchingPredicate:(NSPredicate *)predicate;
- (void)removeWords:(NSSet<NSString *> *)words;
```

那么，转换为Swift，则是这个样子的：

```
var words: Set<String>
func wordsMatchingPredicate(predicate: NSPredicate) -> Set<String>
func removeWords(words: Set<String>)
```

我们能够将所有`NSSet`对象转换为Swift集合，因为所有的Objective-C对象都可以被转换为`AnyObject`。所有的`NSSet`对象都能够转换为Swift对象，因此Swift编译器将会在导入Objective-C API的时候，将所有的`NSSet`类转换为`Set<AnyObject>`。同理，当我们在Objective-C中使用Swift类或者协议的时候，导入器将会将Swift集合重新映射为Objective-C兼容的类型：`NSSet`对象。

当我们将`NSSet`对象转换为Swift集合后，还可以将集合下转为其他指定类型。就如同Swift数组的下转一样，Swift集合的下转不确保一定成功。对`Set<AnyObject>`下转为指定类型的结果需要使用`as?`操作符，以确保其是可选值。

我们仍然可以直接从Swift数组字面量中直接创建一个`NSSet`对象，它同样遵循上面提到的转换规则。当我们明确地将某个常量或者变量定义为`NSSet`对象，并且使用一个数组字面量来赋值的时候，Swift将会创建一个`NSSet`对象，而不是Swift集合。

###字典(Dictionaries)

Swift同样可以在`Dictionary`和`NSDictionary`类当中自动转换。当我们将带有参数类型的`NSDictionary`对象转换为Swift字典之后，得到的结果是一个`[ObjectType]`类型的字典。如果`NSDictionary`对象没有指明参数类型，那么它将会被转换为`[NSObject:AnyObject]`类型的Swift字典。

比如说，我们看到以下Objective-C声明：

```
@property NSDictionary<NSURL *, NSData *>* cachedData;
- (NSDictionary<NSURL *, NSNumber *> *)fileSizesForURLsWithSuffix:(NSString *)suffix;
- (void)setCacheExpirations:(NSDictionary<NSURL *, NSDate *> *)expirations;
```

那么，转换为Swift，则是这个样子的：

```
var cachedData: [NSURL: NSData]
func fileSizesForURLsWithSuffix(suffix: String) -> [NSURL: NSNumber]
func setCacheExpirations(expirations: [NSURL: NSDate])
```

我们能够将所有的`NSDictionary`对象转换为Swift字典，因为所有的Objective-C对象都兼容`AnyObject`。重申一下，某个对象能够“兼容”`AnyObject`，指的是其是Objective-C的一个实例，或者是Swift的类，亦或者是能够转换为这两者之一的东西。所有的`NSDictionary`对象都能够转换为Swift字典，因此Swift编译器会在导入Objective-C API的时候，将所有的`NSDictionary`类替换成`[NSObject: AnyObject]`。同理，当我们在Objective-C中使用Swift类或者协议的时候，导入器将会将Swift字典重新映射为Objective-C兼容的类型：`NSDictionary`对象。

当我们将`NSDictionary`对象转换为Swift字典后，还可以将字典*下转*为其他指定类型。就如同Swift数组的下转一样，Swift字典的下转不确保一定成功。对`[NSObject: AnyObject]`下转为指定类型的结果需要使用`as?`操作符，以确保其是可选值。

当我们进行反向转换，也就是将Swift字典转换为`NSDictionary`对象的过程中，其键值都必须是某个类的实例，或者能够被转换为某个类的实例。

我们仍然可以直接从Swift数组字面量中直接创建一个`NSDictionary`对象，它同样遵循上面提到的转换规则。当我们明确地将某个常量或者变量定义为`NSDictionary`对象，并且使用一个数组字面量来赋值的时候，Swift将会创建一个`NSDictionary`对象，而不是Swift字典。

<a name = "errors"></a>
##错误

Swift能够自动在`ErrorType`类型和`NSError`类之间转换，会发生错误的Objective-C方法等价于Swift中的`throw`方法，而会发生错误的Swift方法通过Objecitive-C错误约定，也等价于产生错误的Objective-C方法。

实现`ErrorType`协议，并且使用`@objc`特性声明的Swift枚举类型，会产生一个`NS_ENUM`声明和一个`NSString`常量，以能够在产生的头文件中设定对应的错误范围。比如说，有以下Swift枚举声明代码：

```
@objc public enum CustomError: Int, ErrorType {
    case A, B, C
}
```

那么，在相应的生成头文件中的Objectivive-C声明就是：

```
// Project-Swift.h
typedef SWIFT_ENUM(NSInteger, CustomError) {
  CustomErrorA = 0,
  CustomErrorB = 1,
  CustomErrorC = 2,
};
static NSString * const CustomErrorDomain = @"Project.CustomError";
```

关于更多Swift和Objective-C API中错误处理的相关信息，请参阅[Error Handling](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/AdoptingCocoaDesignPatterns.html#//apple_ref/doc/uid/TP40014216-CH7-ID10)。

<a name = "foundation_data_types"></a>
##Foundation数据类型
Swift 也提供一种简单便捷的覆盖方法来连接定义在 Foundation 框架中的数据类型。在`CGSize`和`CGPoint`中使用覆盖方法，我们就能在它的句法中感受到Swift语言的自然和统一。比如，我们可以使用如下语法创建一个`CGSize`类型的结构:

```swift
let size = CGSize(width: 20, height: 40)
```

覆盖方法也允许我们以一种自然的方式调用 Foundation 的结构函数。

```swift
let rect = CGRect(x: 50, y: 50, width: 100, height: 100)
let width = rect.width    // equivalent of CGRectGetWidth(rect)
let maxX = rect.maxY      // equivalent of CGRectGetMaxY(rect)
```
Swift可以将`NSUInteger`和`NSInteger`转换为`Int`类型。这些类型都会在 Foundation APIs 中变为`Int`类型。在 Swift 中`Int`常被尽可能地用以连贯性，同时当我们要求一个无符号整数类型时，`UInt`类型总是可使用的。

<a name = "foundation_functions"></a>
##Foundation函数

在 Swift 中，`NSLog`可在系统控制台输出信息。我们可以像在 Objective-C 中使用过的语法格式那样使用此函数。

```swift
NSLog("%.7f", pi)
// Logs "3.1415927" to the console
```
同时，Swift 也提供像`print(_:)`这样的输出函数。多亏于 Swift 的字符插值机制才让这些函数简单，粗暴，高效。这些函数不会在系统控制台输出信息，但在需要调用的时候却是可用的。

Swift 中不再存在`NSAssert`函数，取而代之的是`assert`函数。


<a name = "core_foundation"></a>
##Core Foundation

Swift中的 Core Foundation 类型是一个成熟的类。当出现内存管理注释时，Swift 会自动地管理 Core Foundation 对象的内存，这其中包括我们实例化了的 Core Foundation 对象。在 Swift 中，我们可以自由变换 Fundation 和 Core Foundation 类型。如果我们想先转换为桥接 Foundation 类型时，那么也可以桥接一些 toll-free bridged Core Foundation 类型到 Swift 标准库类型。

###重映射类型(Remapped Types)

当 Swift 导入 Core Foundation 类型时，编译器会重映射导入的类型名字。编译器会从每个类型名字的末端移除 *Ref*，这是因为所有的 Swift 类都属于引用类型，因此后缀是多余的。

Core Foundation 中的`CFTypeRef`类型会对`Anyobject`类型重映射。所以我们以前使用的`CFTypeRef`，现在该换成`AnyObject`了。

###内存管理对象(Memory Managed Objects)

在 Swift 中，从 annotated APIs 返回的 Core Foundation 对象能够自动进行内存管理--我们不再需要调用自身的`CFRetain`，`CFRelease`，或者`CFAutorelease`函数。

如果我们从自身的C函数和 Objective-C 方法中返回一个 Core Foundation 对象，我们需要用`CF_RETURNS_RETAINED`或者`CF_RETURNS_NOT_RETAINED`注释这个对象。我们同样可以使用`CF_IMPLICIT_BRIDGING_ENABLED`和`CF_IMPLICIT_BRIDGING_DISABLED`宏命令来封装C函数声明，这些函数遵循Core Foundation的管理规定、命名规定，以便能够根据命名退导出内存管理机制能。

如果我们只调用那些不会间接返回 Core Foundation 对象的 annotated APIs，那么现在我们可以跳过本节的剩余部分了。否则，下面我们继续学习非托管的 Core Foundation 对象。

###非托管对象(Unmanaged Objects)

当 Swift 导入 unannotated 的APIs时，编译器将不会自动地对返回的 Core Foundation 对象进行内存管理托管。Swift 将这些返回的 Core Foundation 对象封闭在一个`Unmanaged<T>`结构中。那些间接返回 Core Foundation 的对象也是非托管的。举个例子，这里有一个 unannotated 的 C 函数:

```swift
CFStringRef StringByAddingTwoStrings(CFStringRef string1, CFStringRef string2)
```

这里说明了Swift是怎么导入的:

```swift
func StringByAddingTwoStrings(CFString!, CFString!) -> Unmanaged<CFString>!
```
假设我们从 unannotated APIs 接收了非托管的对象，在使用它之前，我们必须将它转换为能够内存管理的对象。在这方面，Swift 可以帮我们进行内存管理而不用自己动手。同时，`Unmanaged<T>`结构也提供了两个方法来把一个非托管对象转换为一个可内存管理的对象--`takeUnretainedValue()`方法和`takeRetainedValue()`方法。这两个方法会返回原始的，非封闭的对象类型。我们可以根据我们实际调用的APIs返回的unretained或retained的对象，来选择哪一方法更合适。

比如，假设这里有一个 C 函数，这个函数在返回值前不会释放`CFString`对象。在使用这个对象前，我们使用`takeUnretainedValue()`函数，以将它转换为一个能够内存管理托管的对象。

```swift
let memoryManagedResult = StringByAddingTwoStrings(str1, str2).takeUnretainedValue()
// memoryManagedResult is a memory managed CFString
```

我们也可以在一个非托管的对象中使用`retain()`，`release()`和`autorelease()`方法，但是这种做法并不值得推荐。

要了解更多信息，请参考[Memory Management Programming Guide for Core Foundation](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/CFMemoryMgmt.html#//apple_ref/doc/uid/10000127i)中的[Memory Management Programming Guide for Core Foundation](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148)一节。
