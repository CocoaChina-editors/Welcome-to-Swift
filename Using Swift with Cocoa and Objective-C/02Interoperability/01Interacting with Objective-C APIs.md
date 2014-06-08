#与Objective-C的API交互
**互用性**是让Swift和Objective-C相接合的一种特性，使你能够在一种语言编写的文件中使用另一种语言。当你准备开始把Swift融入到你的开发流程中时，你应该懂得如何利用互用性来重新定义并提高你写Cocoa应用的方案。
互用性很重要的一点就是当你在写Swift代码时使用Objective-C的API接口。当你导入一个Objective-C框架后，你可以使用原生的Swift语法实例化它的Class并且与之交互。
##初始化
为了使用Swift实例化Objective-C的Class，你应该使用Swift语法调用它的一个初始化器。当Objective-C的`init`方法变化到Swift，他们用Swift初始化语法呈现。"init"前缀被截断当作一个关键字，用来表明该方法是初始化方法。那些以“initWith”开头的`init`方法，“With”也会被去除。从“init”或者“initWith”中分离出来的这部分方法名首字母变成小写，并且被当做是第一个参数的参数名。其余的每一部分方法名依次变味参数名。这些方法名都在圆括号中被调用。

举个例子，你在使用Objective-C时会这样做：

```
UITableView *myTableView = [[UITableView alloc] 
initWithFrame:CGRectZero style:UITableViewStyleGrouped];
```
在Swift中，你应该这样做：

```
let myTableView: UITableView = UITableView(frame: CGRectZero, style: .Grouped)
```

你不需要调用alloc，Swift能替你处理。注意，当使用Swift风格的初始化函数的时候，”init”不会出现。
你可以在初始化时显式的声明对象的类型，也可以忽略它，Swift能够正确判断对象的类型。

```
//Swift
let myTextField = UITextField(frame: CGRect(0.0, 0.0, 200.0, 40.0))
```
这里的`UITableView`和`UITextField`对象和你在Objective-C中使用的具有相同的功能。你可以用一样的方式使用他们，包括访问属性或者调用各自的类中的方法。

为了统一和简易，Objective-C的工厂方法也在Swift中映射为方便的初始化方法。这种映射能够让他们使用同样简洁明了的初始化方法。例如，在Objective-C中你可能会像下面这样调用一个工厂方法：
```
//Objective-C
UIColor *color = [UIColor colorWithRed:0.5 green:0.0 blue:0.5 alpha:1.0];
```

在Swift中，你应该这样做：
```
//Swift
let color = UIColor(red: 0.5, green: 0.0, blue: 0.5, alpha: 1.0)
```

##访问属性
在Swift中访问和设置Objective-C对象的属性时，使用点语法

```
///Swift
myTextField.textColor = UIColor.darkGrayColor()
myTextField.text = "Hello world"
if myTextField.editing {
    myTextField.editing = false
}
```
当get或set属性时，直接使用属性名称，不需要附加圆括号。注意，`darkGrayColor`后面附加了一对圆括号，这是因为`darkGrayColor`是`UIColor`的一个类方法，不是一个属性。
在Objective-C中，一个有返回值的无参数方法可以被作为一个隐式的访问函数，并且可以与访问器使用同样的方法调用。但在Swift中不再能够这样做了，只有使用`@property`关键字声明的属性才会被作为属性引入。

##方法
在Swift中调用Objective-C方法时，使用点语法。
当Objective-C方法转换到Swift时，Objective-C的`selector`的第一部分将会成为方法名并出现在圆括号的前面，而第一个参数将直接在括号中出现，并且没有参数名，而剩下的参数名与参数则一一对应的填入圆括号中。

举个例子，你在使用Objective-C时会这样做：

```
//Objective-C
[myTableView insertSubview:mySubview atIndex:2];
```

在Swift中，你应该这样做：

```
//Swift
myTableView.insertSubview(mySubview, atIndex: 2)
```

如果你调用一个无参方法，仍必须在方法名后面加上一对圆括号

```
//Swift
myTableView.layoutIfNeeded()
```

##id 兼容性
Swift包含一个叫做`AnyObject`的协议类型，表示任意类型的对象，就像Objective-C中的`id`一样。`AnyObject`协议允许你编写类型安全的Swift代码同时维持无类型对象的灵活性。因为`AnyObject`协议保证了这种安全，Swift将id对象导入为AnyObject。
举个例子，跟id一样，你可以为`AnyObject`类型的对象分配任何其他类型的对象，你也同样可以为它重新分配其他类型的对象。

```
//Swift
var myObject: AnyObject = UITableViewCell()
myObject = NSDate()
```
你也可以在调用Objective-C方法或者访问属性时不将它转换为具体类的类型。这包括了Objcive-C中标记为@objc的方法。

```
//Swift
let futureDate = myObject.dateByAddingTimeInterval(10)
let timeSinceNow = myObject.timeIntervalSinceNow
```

然而，由于直到运行时才知道`AnyObject`的对象类型，所以有可能在不经意间写出不安全代码。另外，与Objective-C不同的是，如果你调用方法或者访问的属性AnyObject 对象没有声明，将会报运行时错误。比如下面的代码在运行时将会报出一个unrecognized selector error错误：

```
//Swift
myObject.characterAtIndex(5)
// crash, myObject does't respond to that method
```
但是，你可以通过Swift的optinals特性来排除这个Objective-C中常见的错误，当你用`AnyObject`对象调用一个Objective-C方法时，这次调用将会变成一次隐式展开optional（implicitly unwrapped optional）的行为。你可以通过optional特性来决定AnyObject类型的对象是否调用该方法，同样的，你可以把这种特性应用在属性上。

举个例子，在下面的代码中，第一和第二行代码将不会被执行因为`length`属性和`characterAtIndex:`方法不存在于NSDate对象中。`myLength`常量会被推测成可选的`Int`类型并且被赋值为`nil`。同样你可以使用`if-let`声明来有条件的展开这个方法的返回值，从而判断对象是否能执行这个方法。就像第三行做的一样。

```
//Swift
let myLength = myObject.length?
let myChar = myObject.characterAtIndex?(5)
if let fifthCharacter = myObject.characterAtIndex(5) {
    println("Found \(fifthCharacter) at index 5")
}
```
对于Swift中的强制类型转换，从AnyObject类型的对象转换成明确的类型并不会保证成功，所以它会返回一个可选的值。而你需通过检查该值的类型来确认转换是否成功。

```
//Swift
let userDefaults = NSUserDefaults.standardUserDefaults()
let lastRefreshDate: AnyObject? = userDefaults.objectForKey("LastRefreshDate")
if let date = lastRefreshDate as? NSDate {
    println("\(date.timeIntervalSinceReferenceDate)")
}
```

当然，如果你能确定这个对象的类型（并且确定不是`nil`），你可以添加`as`操作符强制调用。

```
//Swift
let myDate = lastRefreshDate as NSDate
let timeInterval = myDate.timeIntervalSinceReferenceDate
```

##使用nil

在Objective-C中，对象的引用可以是值为`NULL`的原始指针（同样也是Objective-C中的`nil`）。而在Swift中，所有的值–包括结构体与对象的引用–都被保证为非空。作为替代，你将这个可以为空的值包装为optional type。当你需要宣告值为空时，你需要使用`nil`。你可以在[Optionals](http://)中了解更多。
因为Objective-C不会保证一个对象的值是否非空，Swift在引入Objective-C的API的时候，确保了所有函数的返回类型与参数类型都是optional，在你使用Objective-C的API之前，你应该检查并保证该值非空。
在某些情况下，你可能绝对确认某些Objective-C方法或者属性永远不应该返回一个nil的对象引用。为了让对象在这种情况下更加易用，Swift使用 implicitly unwrapped optionals 方法引入对象， implicitly unwrapped optionals 包含optional 类型的所有安全特性。此外，你可以直接访问对象的值而无需检查nil。当你访问这种类型的变量时， implicitly unwrapped optional 首先检查这个对象的值是否不存在，如果不存在，将会抛出运行时错误。

##扩展（Extensions）
Swift的扩展和Objective-C的类别（Category）相似。扩展为原有的类，结构和枚举丰富了功能，包括在Objective-C中定义过的。你可以为系统的框架或者你自己的类型增加扩展，只需要导入合适的模块并且保证你在Objective-C中使用的类、结构或枚举拥有相同的名字。
举个例子，你可以扩展`UIBezierPath`类来为它增加一个等边三角形，这个方法只需提供三角形的边长与起点。
```
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

你也可以使用扩展来增加属性（包括类的属性与静态属性）。然而，这些属性必须是通过计算才能获取的，扩展不会为类，结构体，枚举存储属性。下面这个例子为`CGRect`类增加了一个叫`area`的属性。

```
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

你同样可以使用扩展来为类添加协议而无需增加它的子类。如果这个协议是在Swift中被定义的，你可以添加comformance到它的结构或枚举中无论它们在Objective-C或在Swift中被定义。
你不能使用扩展来覆盖Objective-C类型中存在的方法与属性。

##闭包（Closures）
Objective-C中的`blocks`会被自动导入为Swift中的闭包。例如，下面是一个Objective-C 中的block 变量：
```
//Objective-C
void (^completionBlock)(NSData *, NSError *) = ^(NSData *data, NSError *error) {/* ... */}
```
而它在Swift中的形式为

```
//Swift
let completionBlock: (NSData, NSError) -> Void = {data, error in /* ... */}
```

Swift的闭包与Objective-C中的blocks能够和睦相处，所以你可以把一个Swift闭包传递给一个把block作为参数的Objective-C函数。Swift闭包与函数具有互通的类型，所以你甚至可以传递Swift函数的名字。
闭包与blocks语义上想通但是在一个地方不同：变量是可以直接改变的，而不是像block那样会拷贝变量。换句话说，Swift中变量的默认行为与Objective-C中__block变量一致。

##比较对象
当比较两个Swift中的对象时，可以使用两种方式。第一种，使用（==），判断两个对象内容是否相同。第二种，使用(===),判断常量或者变量是否为同一个对象的实例。

Swift与Objective-C一般使用 == 与 === 操作符来做比较。Swift的==操作符为源自NSObject的对象提供了默认的实现。在实现 == 操作符时，Swift调用 NSObject定义的 isEqual: 方法。NSObject类仅仅做了身份的比较，所以你需要在你自己的类中重新实现isEqual:方法。因为你可以直接传递Swift对象给Objective-C的API,你也应该为这些对象实现自定义的isEqual:方法，如果你希望比较两个对象的内容是否相同而不是仅仅比较他们是不是由相同的对象派生。

作为实现比较函数的一部分，确保根据[Object comparison](http://)实现对象的`hash`属性。更进一步的说，如果你希望你的类能够作为字典中的键，也需要遵从`Hashable`协议以及实现`hashValues`属性。

##Swift类型兼容性
当你定义了一个继承自`NSObject`或者其他Objective-C 类的Swift类，这些类都能与Objective-C无缝连接。所有的步骤都有Swift编译器自动完成，如果你从未在Objective-C代码中导入Swift 类，你也不需要担心类型适配问题。另外一种情况，如果你的Swift类并不来源自Objectve-C类而且你希望能在Objecive-C的代码中使用它，你可以使用下面描述的@objc属性。

`@objc`可以让你的Swift API在Objective-C中使用。换句话说，你可以通过在任何Swift方法、类、属性前添加`@objc`，来使得他们可以在Objective-C代码中使用。如果你的类继承自Objective-C,编译器会自动帮助你完成这一步。编译器还会在所有的变量、方法、属性前加@objc，如果这个类自己前面加上了`@objc`关键字。当你使用`@IBOutlet`,`@IBAction`,或者是`@NSManaged`属性时，`@objc`也会自动加在前面。这个关键字也可以用在Objetive-C中的target-action设计模式中，例如，`NSTimer`或者`UIButton`。

当你在Objective-C中使用Swift API，编译器基本对语句做直接的翻译。例如，`Swift API func playSong(name: String)`会被解释为`- (void)playSong:(NSString *)name`。然而，有一个例外：当在Objective-C中使用Swift的初始化函数，编译器会在方法前添加”initWith”并且将原初始化函数的第一个参数首字母大写。例如，这个Swift初始化函数`init (songName: String, artist: String`将被翻译为` - (instancetype)initWithSongName:(NSString *)songName artist:(NSString *)artist
`。
Swift 同时也提供了一个`@objc`关键字的变体，通过它你可以自定义在Objectiv-C中转换的函数名。例如，如果你的Swift 类的名字包含Objecytive-C中不支持的字符，你就可以为Objective-C提供一个可供替代的名字。如果你给Swift函数提供一个Objcetiv-C名字，要记得为带参数的函数添加（:）

```
//Swift
@objc(Squirrel)
class Белка {
    @objc(initWithName:)
    init (имя: String) { /*...*/ }
    @objc(hideNuts:inTree:)
    func прячьОрехи(Int, вДереве: Дерево) { /*...*/ }
}
```

当你在Swift类中使用`@objc(<#name#>)`关键字，这个类可以不需要命名空间即可在Objective-C中使用。这个关键字在你迁徙Objecive-C代码到Swift时同样也非常有用。由于归档过的对象存贮了类的名字，你应该使用`@objc(<#name#>)`来声明与旧的归档过的类相同的名字，这样旧的类才能被新的Swift类解档。

##Objective-C选择器(Selectors)
一个Objective-C 选择器类型指向一个Objective-C的方法名。在Swift时代， Objective-C 的选择器被`Selector`结构体替代。你可以通过字符串创建一个选择器，比如`let mySelector: Selector = "tappedButton:"`。因为字符串能够自动转换为选择器，所以你可以把字符串直接传递给接受选择器的方法。

```
//Swift
import UIKit
class MyViewController: UIViewController {
    let myButton = UIButton(frame: CGRect(x: 0, y: 0, width: 100, height: 50))
    
    init(nibName nibNameOrNil: String!, bundle nibBundleOrNil: NSBundle!) {
        super.init(nibName: nibName, bundle: nibBundle)
        myButton.targetForAction("tappedButton:", withSender: self)
    }
    
    func tappedButton(sender: UIButton!) {
        println("tapped button")
    }
}

```
>注意
>
>`performSelector:`方法和相关的调用选择器的方法没有导入到Swift中因为它们是不安全的。

如果你的Swift类继承自Objective-C的类，你的所有方法都可以用作Objective-C的选择器。另外，如果你的Swift类不是继承自Objective-C，如果你想要当选择器来使用你就需要在前面添加`@objc`关键字，详情请看[Swift Type Compatibility](http://)。

