> 翻译：[Creolophus](https://github.com/Creolophus)

> 校正：[Evilcome](https://github.com/Evilcome) [ChildhoodAndy](https://github.com/dabing1022)


#与 Objective-C 的 API 交互

-----------------

本页包含内容：

-   [初始化](#initialization)
-   [可失败初始化](#Failable Initialization)
-   [访问属性](#accessing_properties)
-   [方法](#working_with_methods)
-   [id 兼容性（id Compatibility）](#id_compatibility)
-   [空值和可选值](#Nullability_and_Optionals)
-   [扩展（Extensions）](#extensions)
-   [闭包（Closures）](#closures)
-   [比较对象](#object_comparison)
-   [Swift 类型兼容性](#swift_type_compatibility)
-   [为Objective-C暴露Swift接口](#Exposing_Swift_Interfaces_in_Objective-C)
-   [动态分发](#Requiring_Dynamic_Dispatch)
-   [轻量级泛型](#Lightweight_Generics)
-   [Objective-C 选择器（Selectors）](#objective_c_selectors)

**互用性**是让 Swift 和 Objective-C 相接合的一种特性，使你能够在一种语言编写的文件中使用另一种语言。当你准备开始把 Swift 融入到你的开发流程中时，你应该懂得如何利用互用性来重新定义并提高你写 Cocoa 应用的方案。

互用性很重要的一点就是允许你在写 Swift 代码时使用 Objective-C 的 API 接口。当你导入一个 Objective-C 框架后，你可以使用原生的 Swift 语法实例化它的 Class 并且与之交互。

<a name="initialization"></a>
##初始化

为了使用 Swift 实例化 Objective-C 的 Class，你应该使用 Swift 语法调用它的一个初始化器。当 Objective-C 的`init`方法变化到 Swift，他们用 Swift 初始化语法呈现。“init”前缀被截断当作一个关键字，用来表明该方法是初始化方法。那些以“initWith”开头的`init`方法，“With”也会被去除。从“init”或者“initWith”中分离出来的这部分方法名首字母变成小写，并且被当做是第一个参数的参数名。其余的每一部分方法名依次变为参数名。这些方法名都在圆括号中被调用。

举个例子，你在使用 Objective-C 时会这样做：

```objective-c
UITableView *myTableView = [[UITableView alloc] 
initWithFrame:CGRectZero style:UITableViewStyleGrouped];
```
在 Swift 中，你应该这样做：

```swift
let myTableView: UITableView = UITableView(frame: CGRectZero, style: .Grouped)
```

你不需要调用`alloc`，Swift 能够正确的为你处理。注意，当使用 Swift 风格的初始化函数的时候，“init”不会出现。

你可以在初始化时显式的声明对象的类型，也可以忽略它，Swift 能够正确判断对象的类型。

```swift
let myTextField = UITextField(frame: CGRect(0.0, 0.0, 200.0, 40.0))
```

这里的`UITableView`和`UITextField`对象和你在 Objective-C 中使用的具有相同的功能。你可以用一样的方式使用他们，包括访问属性或者调用各自的类中的方法。

为了统一和简易，Objective-C 的工厂方法也在 Swift 中映射为方便的初始化方法。这种映射能够让他们使用同样简洁明了的初始化方法。例如，在 Objective-C 中你可能会像下面这样调用一个工厂方法：

```objective-c
UIColor *color = [UIColor colorWithRed:0.5 green:0.0 blue:0.5 alpha:1.0];
```

在 Swift 中，你应该这样做：

```swift
let color = UIColor(red: 0.5, green: 0.0, blue: 0.5, alpha: 1.0)
```

<a name="Failable Initialization"></a>
##可失败初始化

在 Objective-C 中，构造器会直接返回他们初始化的对象。为了通知调用者初始化失败，Objective-C 构造器会返回`nil`。在 Swift 中，这种模式被内置到语言特性中，被称为_可失败初始化_。在 iOS 和 OSX 系统框架中许多 Objective-C 构造器会被检查是否会初始化失败。如果初始化不会失败，这些 Objective-C 构造器便会以`init(...)`被导入，如果初始化可能会失败，则会以`init?(...)`被导入。在我们自己的 Objective-C 类以及未被检查的框架类中，构造器会以`init!(...)`被导入。例如，当图片文件在指定路径中不存在时，`UIImage(contentsOfFile:)`构造器初始化`UIImage`对象便会失败。如果初始化成功，我们可以用可选值绑定来对可失败初始化的结果进行解包。

```swift
if let image = UIImage(contentsOfFile: "MyImage.png") {
    // loaded the image successfully
} else {
    // could not load the image
}
```

<a name="accessing_properties"></a>
##访问属性

在 Swift 中访问和设置 Objective-C 对象的属性时，使用点语法

```swift
myTextField.textColor = UIColor.darkGrayColor()
myTextField.text = "Hello world"
```

当 get 或 set 属性时，直接使用属性名称，不需要附加圆括号。注意，`darkGrayColor`后面附加了一对圆括号，这是因为`darkGrayColor`是`UIColor`的一个类方法，不是一个属性。

在 Objective-C 中，一个有返回值的无参数方法可以被作为一个隐式的访问函数，并且可以与访问器使用同样的方法调用。但在 Swift 中不再能够这样做了，只有在 Objective-C 中 使用`@property`关键字声明的属性才会被作为属性引入。方法被导入和调用见[方法](#working_with_methods)描述。

<a name="working_with_methods"></a>
##方法

在 Swift 中调用 Objective-C 方法时，使用点语法。

当 Objective-C 方法转换到 Swift 时，Objective-C 的`selector`的第一部分将会成为方法名并出现在圆括号的前面，而第一个参数将直接在括号中出现，并且没有参数名，而剩下的参数名与参数则一一对应的填入圆括号中。选择器的所有部分在调用时都是必需的。

举个例子，你在使用 Objective-C 时会这样做：

```objective-c
[myTableView insertSubview:mySubview atIndex:2];
```

在 Swift 中，你应该这样做：

```swift
myTableView.insertSubview(mySubview, atIndex: 2)
```

如果你调用一个无参方法，仍必须在方法名后面加上一对圆括号

```swift
myTableView.layoutIfNeeded()
```

<a name="id_compatibility"></a>
##id 兼容性（id Compatibility）

Swift 包含一个叫做`AnyObject`的协议类型，表示任意类型的对象，就像 Objective-C 中的`id`一样。`AnyObject`协议允许你编写类型安全的 Swift 代码同时维持无类型对象的灵活性。因为`AnyObject`协议保证了这种安全，Swift 将`id`对象导入为`AnyObject`。

举个例子，跟`id`一样，你可以为`AnyObject`类型的对象分配任何其它类型的对象，你也同样可以为它重新分配其它类型的对象。

```swift
var myObject: AnyObject = UITableViewCell()
myObject = NSDate()
```

你也可以在调用 Objective-C 方法或者访问属性时不将它转换为具体类的类型。这包括了 Objective-C 中标记为`@objc`的方法。

```swift
let futureDate = myObject.dateByAddingTimeInterval(10)
let timeSinceNow = myObject.timeIntervalSinceNow
```

然而，由于直到运行时才知道`AnyObject`的对象类型，所以有可能在不经意间写出不安全代码。另外，与 Objective-C 不同的是，如果你调的用方法或者访问的属性`AnyObject`对象没有声明，将会报运行时错误。比如下面的代码在运行时将会报出一个`unrecognized selector error`错误：

```swift
myObject.characterAtIndex(5)
// crash, myObject does't respond to that method
```

但是，你可以通过 Swift 的可选值特性来排除这个 Objective-C 中常见的错误，当你用`AnyObject`对象调用一个 Objective-C 方法时，这次调用将会变成一次隐式展开可选值的行为。你可以通过可选值特性来决定`AnyObject`类型的对象是否调用该方法，同样的，你可以把这种特性应用在属性上。

> 注

> 通过`AnyObject`访问的属性永远返回一个可选值。

举个例子，在下面的代码中，第一和第二行代码将不会被执行因为`length`属性和`characterAtIndex:`方法不存在于 NSDate 对象中。`myLength`常量会被推测成可选的`Int`类型并且被赋值为`nil`。同样你可以使用`if-let`声明来有条件的展开这个方法的返回值，从而判断对象是否能执行这个方法。就像第三行做的一样。

```swift
let myLength = myObject.length?
let myChar = myObject.characterAtIndex?(5)
if let fifthCharacter = myObject.characterAtIndex(5) {
    println("Found \(fifthCharacter) at index 5")
}
```

对于 Swift 中的强制类型转换，从`AnyObject`类型的对象转换成明确的类型并不会保证成功，所以它会返回一个可选的值。而你需通过检查该值的类型来确认转换是否成功。

```swift
//Swift
let userDefaults = NSUserDefaults.standardUserDefaults()
let lastRefreshDate: AnyObject? = userDefaults.objectForKey("LastRefreshDate")
if let date = lastRefreshDate as? NSDate {
    println("\(date.timeIntervalSinceReferenceDate)")
}
```

当然，如果你能确定这个对象的类型（并且确定不是`nil`），你可以添加`as`操作符强制调用。

```swift
let myDate = lastRefreshDate as NSDate
let timeInterval = myDate.timeIntervalSinceReferenceDate
```

<a name="Nullability_and_Optionals"></a>
## 空值和可选值

在 Objective-C 中，对象的引用可以是值为`NULL`的原始指针（在 Objective-C 中的简称为`nil`）。而在Swift中，所有的值–包括结构体与对象的引用–都被保证为非空。作为替代，你将这个可以为空的值包装为optional type。当你需要宣告值为空时，你需要使用`nil`。你可以在[Optionals](https://github.com/CocoaChina-editors/Welcome-to-Swift/blob/master/The%20Swift%20Programming%20Language/02Language%20Guide/01The%20Basics.md#optionals)中了解更多。

因为Objective-C不会保证一个对象的值是否非空，Swift 在引入 Objective-C 的 API 的时候，确保了所有函数的返回类型与参数类型都是可选的，在你使用 Objective-C 的 API 之前，你应该检查并保证该值非空。
在某些情况下，你可能绝对确认某些Objective-C方法或者属性永远不应该返回一个nil的对象引用。为了让对象在这种情况下更加易用，Swift使用 implicitly unwrapped optionals 方法引入对象， implicitly unwrapped optionals 包含optional 类型的所有安全特性。此外，你可以直接访问对象的值而无需检查nil。当你访问这种类型的变量时， implicitly unwrapped optional 首先检查这个对象的值是否不存在，如果不存在，将会抛出运行时错误。


In Objective-C, you work with references to objects using raw pointers that could be NULL (referred to as nil in Objective-C). In Swift, all values—including structures and object references—are guaranteed to be non–null. Instead, you represent a value that could be missing by wrapping the type of the value in an optional type. When you need to indicate that a value is missing, you use the value nil. For more information about optionals, see Optionals in The Swift Programming Language.

Objective-C can use nullability annotations to designate whether a parameter type, property type, or return type, can have a NULL or nil value. Individual type declarations can be audited using the __nullable and __nonnull annotations, or entire regions can be audited for nullability using the NS_ASSUME_NONNULL_BEGIN and NS_ASSUME_NONNULL_END macros. If no nullability information is provided for a type, Swift cannot distinguish between optional and non-optional references, and imports it as an implicitly unwrapped optional.

Types declared to be nullable, either with a __nonnull annotation or in an audited region, are imported by Swift as a non-optional.
Types declared to be nullable with a __nullable annotation, are imported by Swift as a optional.
Types declared without a nullability annotation are imported by Swift as an implicitly unwrapped optional.
For example, consider the following Objective-C declarations:

```Objective-C
@property (nullable) id  nullableProperty;
@property (nonnull) id nonNullProperty;
@property id unannotatedProperty;
 
NS_ASSUME_NONNULL_BEGIN
- (id)returnsNonNullValue;
- (void)takesNonNullParameter:(id)value;
NS_ASSUME_NONNULL_END
 
- (nullable id)returnsNullableValue;
- (void)takesNullableParameter:(nullable id)value;
 
- (id)returnsUnannotatedValue;
- (void)takesUnannotatedParameter:(id)value;
```
下面是他们如何被导入 Swift 中的：

```Swift
var nullableProperty: AnyObject?
var nonNullProperty: AnyObject
var unannotatedProperty: AnyObject!
 
func returnsNonNullValue() -> AnyObject
func takesNonNullParameter(value: AnyObject)
 
func returnsNullableValue() -> AnyObject?
func takesNullableParameter(value: AnyObject?)
 
func returnsUnannotatedValue() -> AnyObject!
func takesUnannotatedParameter(value: AnyObject!)
```
大多数 Objective-C 的系统框架，包括 Foundation，都已经提供了可空的注解，允许你使用惯用和类型安全的方式与值打交道。

<a name="extensions"></a>
##扩展（Extensions）

Swift 的扩展和 Objective-C 的类别（Category）相似。扩展为原有的类，结构和枚举丰富了功能，包括在 Objective-C 中定义过的。你可以为系统的框架或者你自己的类型增加扩展，只需要导入合适的模块并且保证你在 Objective-C 中使用的类、结构或枚举拥有相同的名字。

举个例子，你可以扩展`UIBezierPath`类来为它增加一个等边三角形，这个方法只需提供三角形的边长与起点。

```swift
//Swift
extension UIBezierPath {
    convenience init(triangleSideLength: Float, origin: CGPoint) {
        self.init()
        let squareRoot = Float(sqrt(3))
        let altitude = (squareRoot * triangleSideLength) / 2
        moveToPoint(origin)
        addLineToPoint(CGPoint(triangleSideLength, origin.x))
        addLineToPoint(CGPoint(triangleSideLength / 2, altitude))
        closePath()
    }
}
```

你也可以使用扩展来增加属性（包括类的属性与静态属性）。然而，这些属性必须是通过计算才能获取的，扩展不会为类，结构体，枚举添加存储属性。下面这个例子为`CGRect`类增加了一个叫`area`的属性。

```swift
//Swift
extension CGRect {
    var area: CGFloat {
    return width * height
    }
}
let rect = CGRect(x: 0.0, y: 0.0, width: 10.0, height: 50.0)
let area = rect.area
// area: CGFloat = 500.0
```

你同样可以使用扩展来为类添加协议而无需增加它的子类。如果这个协议是在 Swift 中被定义的，你可以添加 comformance 到它的结构或枚举中无论它们在 Objective-C 或在 Swift 中被定义。

你不能使用扩展来覆盖 Objective-C 类型中存在的方法与属性。

<a name="closures"></a>
##闭包（Closures）

Objective-C 中的`blocks`会被自动导入为 Swift 中的闭包。例如，下面是一个 Objective-C 中的 block 变量：

```objective-c
//Objective-C
void (^completionBlock)(NSData *, NSError *) = ^(NSData *data, NSError *error) {/* ... */}
```

而它在 Swift 中的形式为

```swift
//Swift
let completionBlock: (NSData, NSError) -> Void = {data, error in /* ... */}
```

Swift 的闭包与 Objective-C 中的 blocks 能够和睦相处，所以你可以把一个 Swift 闭包传递给一个把 block 作为参数的 Objective-C 函数。Swift 闭包与函数具有互通的类型，所以你甚至可以传递 Swift 函数的名字。

闭包与 blocks 语义上相通但是在一个地方不同：变量是可以直接改变的，而不是像 block 那样会拷贝变量。换句话说，Swift 中变量的默认行为与 Objective-C 中 __block 变量一致。

<a name="object_comparison"></a>
##比较对象

当比较两个 Swift 中的对象时，可以使用两种方式。第一种，使用（==），判断两个对象内容是否相同。第二种，使用(===)，判断常量或者变量是否为同一个对象的实例。

Swift 与 Objective-C 一般使用 == 与 === 操作符来做比较。Swift 的 == 操作符为源自 NSObject 的对象提供了默认的实现。在实现 == 操作符时，Swift 调用 NSObject 定义的 isEqual: 方法。

NSObject 类仅仅做了身份的比较，所以你需要在你自己的类中重新实现 isEqual: 方法。因为你可以直接传递 Swift 对象给 Objective-C 的 API，你也应该为这些对象实现自定义的 isEqual: 方法，如果你希望比较两个对象的内容是否相同而不是仅仅比较他们是不是由相同的对象派生。

作为实现比较函数的一部分，确保根据[Object comparison](//https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/DevPedia-CocoaCore/ObjectComparison.html#//apple_ref/doc/uid/TP40008195-CH37)实现对象的`hash`属性。更进一步的说，如果你希望你的类能够作为字典中的键，也需要遵从`Hashable`协议以及实现`hashValues`属性。

<a name="swift_type_compatibility"></a>
## Swift 类型兼容性

当你在 Swift 中创建了一个继承自 Objective-C 类的子类时，该类以及该类的成员-属性，方法，下标和构造器，便会在 Objective-C 中自动可用。在某些情况下，你需要更细粒度的控制如何将 Swift API 暴漏给 Objective-C。如果你的 Swift 类没有继承自 Objective-C 的类，又或者你想更改暴漏给 Objective-C 代码的接口符号名称，你便可以使用`@objc`属性。如果你正在使用如键值观察的 API 来动态替换方法的实现，也可以通过使用`dynamic`修饰符来获得对 Objective-C 运行时被自动派发的成员的访问。

### 在 Objective-C 中暴漏 Swift 接口

当你定义一个继承自`NSObject`类或者其他 Objective-C 类的 Swift 子类时，该类便会自动兼容 Objective-C。Swift 编译器已经为你做好了这部分所需要的工作。

所有的步骤都由 Swift 编译器自动完成，如果你从未在 Objective-C 代码中导入 Swift 类，你也不需要担心类型适配问题。另外一种情况，如果你的 Swift 类并不继承于 Objective-C 类而你希望能在 Objective-C 的代码中使用它，你可以使用下面描述的`@objc`属性。

`@objc`可以让你的 Swift API 在 Objective-C 中使用。换句话说，你可以通过在任何 Swift 方法、类、属性前添加`@objc`，来使得他们可以在 Objective-C 代码中使用。如果你的类继承自 Objective-C，编译器会自动帮助你完成这一步。编译器还会在所有的变量、方法、属性前加 @objc，如果这个类自己前面加上了`@objc`关键字。当你使用`@IBOutlet`，`@IBAction`，或者是`@NSManaged`属性时，`@objc`也会自动加在前面。这个关键字也可以用在 Objetive-C 中的 target-action 设计模式中，例如，`NSTimer`或者`UIButton`。

当你在 Objective-C 中使用 Swift API，编译器通常会对语句做直接的翻译。例如，Swift API `func playSong(name: String)`会被解释为`- (void)playSong:(NSString *)name`。然而，有一个例外：当在 Objective-C 中使用 Swift 的初始化函数，编译器会在方法前添加“initWith”并且将原初始化函数的第一个参数首字母大写。例如，这个 Swift 初始化函数`init (songName: String, artist: String`将被翻译为` - (instancetype)initWithSongName:(NSString *)songName artist:(NSString *)artist
`。

Swift 同时也提供了一个`@objc`关键字的变体，通过它你可以自定义在 Objective-C 中转换的函数名。例如，如果你的 Swift 类的名字包含 Objective-C 中不支持的字符，你就可以为 Objective-C 提供一个可供替代的名字。如果你要为 Swift 函数提供一个 Objective-C 名字，记得为带参数的函数添加（:）

```swift
//Swift
@objc(Squirrel)
class Белка {
    @objc(initWithName:)
    init (имя: String) { /*...*/ }
    @objc(hideNuts:inTree:)
    func прячьОрехи(Int, вДереве: Дерево) { /*...*/ }
}
```

当你在 Swift 类中使用`@objc(<#name#>)`关键字，这个类可以不需要命名空间即可在 Objective-C 中使用。这个关键字在你迁徙 Objecive-C 代码到 Swift 时同样也非常有用。由于归档过的对象存贮了类的名字，你应该使用`@objc(<#name#>)`来声明与旧的归档过的类相同的名字，这样旧的类才能被新的 Swift 类解档。

<a name="Exposing_Swift_Interfaces_in_Objective-C"></a>
## 为Objective-C暴露Swift接口

<a name="Requiring_Dynamic_Dispatch"></a>
## 请求动态分配


<a name="Lightweight_Generics"></a>
## 轻量级泛型

使用轻量级泛型参数的 Objective-C 声明类型，如`NSArray`，`NSSet`以及`NSDictionary`，在被导入到 Swift 时会附带上他们内容保存的类型。

例如，考虑下面的 Objective-C 属性声明：

```objective-c
@property NSArray<NSDate *>* dates;
@property NSSet<NSString *>* words;
@property NSDictionary<KeyType: NSURL *, NSData *>* cachedData;
```

下面是 Swift 导入后：

```swift
var dates: [NSDate]
var words: Set<String>
var cachedData: [NSURL: NSData]
```

> 注意
> 除了 Foundation 中的集合类， Objective-C 的轻量级泛型会被 Swift 忽略掉。任何其他使用轻量级泛型的类型在导入到 Swift 中时会被视为无参数化。

<a name="objective_c_selectors"></a>
## Objective-C 选择器

Objective-C 选择器是一种指向 Objective-C 方法名的类型。在 Swift 里，Objective-C 的选择器被`Selector`结构体替代。你可以通过字符串创建一个选择器，比如`let mySelector: Selector = "tappedButton:"`。因为字符串字面量能够自动被转换为选择器，所以你可以把字符串字面量直接传递给任何能够接受选择器的方法。

```swift
import UIKit
class MyViewController: UIViewController {
    let myButton = UIButton(frame: CGRect(x: 0, y: 0, width: 100, height: 50))
    
    override init?(nibName nibNameOrNil: String?, bundle nibBundleOrNil: NSBundle?) {
        super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)
        myButton.addTarget(self, action: "tappedButton:", forControlEvents: .TouchUpInside)
    }
    
    func tappedButton(sender: UIButton!) {
        print("tapped button")
    }
    
    required init?(coder: NSCoder) {
        super.init(coder: coder)
    }
}
```

> 注意

>`performSelector:`方法和相关的调用选择器的方法没有导入到 Swift 中因为它们本质上是不安全的。

如果你的 Swift 类继承自 Objective-C 的类，你的所有方法都可以用作 Objective-C 的选择器。反之，如果你的 Swift 类不是继承自 Objective-C，你需要在想要使用的选择器前面加上`@objc`属性前缀，详情请看[Swift 类型兼容性](#swift_type_compatibility)。

