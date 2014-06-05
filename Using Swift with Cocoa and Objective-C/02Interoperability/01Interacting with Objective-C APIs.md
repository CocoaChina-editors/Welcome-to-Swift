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
但是，你可以通过Swift的optinals特性来排除这个Objective-C中常见的错误，当你用`AnyObject`对象调用一个Objective-C方法时
