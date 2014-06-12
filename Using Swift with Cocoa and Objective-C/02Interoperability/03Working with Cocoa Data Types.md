#Cocoa数据类型
作为对Objective-C互换性的一部分，Swift提供快捷高效的方式来处理Cocoa数据类型。

Swift会自动将一些Objective-C类型转换为Swift类型，并将一些Swift类型转换为Objective-C类型。同时，在Objective-C和Swift中您也可以使用一些具有互换性的数据类型。
那些可转换的数据类型或者具有互换性的数据类型被称为*bridged*数据类型。举个例子，在Swift中，您可以将一个`Array`值传递给一个要求为`NSArray`对象的方法。你也可以转换一个bridged类型和它的副本。当你使用`as`转换bridged类型或者那些由常量和变量所提供的类型时，Swift会桥接它们的数据类型。

Swift也提供一种简单便捷的覆盖方法来连接Foundation的数据类型，在后面的Swift语言中，你能在它的句法中感受到自然和统一。

##字符串
Swift会在`String`类型和`NSString`类型中自动转换。这意味着在可以使用`NSString`对象的地方，您可以使用一个属于Swift的`String`类型代替它，这样做会同时拥有它们数据类型的特点，`String`类型的插值，基于Swift设计的APIs以及`NSString`类更广的适用范围。因此，您几乎不必再在你的代码中使用`NSString`类。事实上，当Swift接入Objective-C APIs时，它将把所有`NSString`类型替换为`String`类型。当您在您的Objective-C代码中使用Swift类时，接入的API会将所有`String`类型替换成`NSString`类型。

为了允许字符串转换，只需接入Foundation。举个例子，您在Swift的一个字符串中调用了`capitalizedString`--一个`NSString`类的方法，此后Swift会自动将`String`转换为一个`NSString`对象并调用方法。这个方法甚至会返回一个Swift的`String`类型，因为它在接入的时候被替换了。

```
import Foundation
let greeting = "hello, world!"
let capitalizedGreeting = greeting.capitalizedString
// capitalizedGreeting: String = Hello, World!
```
如果您确实需要用到一个`NSString`对象，您可以用一个Swift的`String`值并转换它。`String`类型总是可以从一个`NSString`对象转换为一个Swift的`String`的值，因此，再没有必要去使用一个可选的类型转换器`()as?)`。您也可以再一个字符串中通过定义常量和变量来创建一个`NSString`对象。

```
import Foundation
let myString: NSString = "123"
if let integerValue = (myString as String).toInt()){
    println("\(myString) is the integer \(integerValue)") 
}
```

###本地化
在Objective-C中，常用`NSLocalizedString`类的宏来定位一个字符串。这集合的宏包括`NSLocalizedStringFromTableInBundle`和`NSLocalizedStringWithDefaultValue`。而在Swift中，只用一个函数就可以实现跟整个`NSLocalizedString`集一样的功能--`NSLocalizedString(key:tableName:bundle:value:comment:)`。这个`NSLocalizedString`函数分别为`tableName`，`bundle`和`value`参数提供了一个默认值。你可以用它来替换宏。

##数字
Swift会自动将已确定的数字类型`Int`和`Float`转换为`NSNumber`。这样的转换允许你基于其中一种类型创建一个`NSNumber`：

```
let n = 42
let m: NSNumber = n
```

你也能传递一个`Int`类型的值，比如传递给一个要求为`NSNumber`类型的参数。同时需要注意的是，`NSNumber`可以包含多种不同的类型，因此您不能把它传递给单一的一个`Int`值。

下面所列出的类型都会自动转换为`NSNumber`:

* `Int`
 
* `UInt`

* `Float`

* `Double`

* `Bool`

##类集合
Swift会自动将`NSArray`和`NSDictionary`类转换为Swift里等价的类。这意味着你将受益于Swift强大的算法和得天独厚的语法来处理集合--可互相转换的Foundation和Swift集合类型。

###数组
Swift会在`Array`类型和`NSArray`类型中自动转换。当你从一个Swift数组转换到一个`NSArray`对象，转换后的数组是一个`AnyObject[]`类型的数组。如果某个对象是Objective-C或者Swift类的实例，或者这个对象可以转换成另一种类型，那么这个对象则属于`AnyObject`类型的对象。你可以将任一`NSArray`对象转换成一个Swift数组，因为所有Objective-C的对象都是`AnyObject`类型的。正因如此，Swift的编译器会在接入Objective-C APIs的时候将`NSArray`类替换成`AnyObject[]`。

当你将一个`NSArray`对象转换成一个Swift数组后，你也可以将数组强制类型转换成一个特定的类型。与从`NSArray`类转换到`AnyObject[]`不同的是，从`AnyObject`类型的对象转换成明确的类型并不会保证成功。由于直到运行时编译器才知道`AnyObject`的对象能否被强制转换为特定的类型，因此，从`AnyObject[]`转换为`SomeType[]`会返回一个optional的值。举个例子，如果你知道一个Swift数组只包含`UIView`类的实例(或者一个`UIView`类的子类)，你可以将`AnyObject`类型的数组元素强制转换为`UIView`对象。如果Swift数组中得元素在运行时不是`UIView`类型的对象，那么转换则会返回`nil`。

```
let swiftyArray = foundationArray as AnyObject[]
if let downcastedSwiftArray = swiftArray as? UIView[] {
    // downcastedSwiftArray contains only UIView objects
}
```

你也可以在for循环中将NSArray对象定向地强制转换为特定类型的Swift数组:

```
for aView: UIView! in foundationArray {
     // aView is of type UIView
}
```

>注意：这种转换是强制转换，如果转换不成功则会在运行时产生错误信息。

当你从Swift数组转换为`NSArray`对象时，Swift数组里的元素必须是属于`AnyObject`的。例如，一个`Int[]`类型的Swift数组包含`Int`结构的元素。`Int`类型并不是一个类的实例，但由于`Int`类型转换成了`NSNumber`类，`Int`类型属于`AnyObject`类型的。因此，你可以将一个`Int[]`类型的Swift数组转换为`NSArray`对象。如果Swift数组里的一个元素不属于`AnyObject`类型，那么在运行时就会产生错误。

你也可以从Swift数组中创建一个`NSArray`对象。当你将一个常量或变量定义为一个`NSArray`对象并分配一个数组给它作为实例变量时，Swift将会创建一个`NSArray`对象，而不是一个Swift数组。

```
let schoolSupplies: NSArray = ["Pencil", "Eraser", "Notebkko"]
// schoolSupplies is an NSArray object containing NSString objects
```
上面的例子中，Swift数组包含包含三个`String`字符串。由于从`String`类型转换为`NSString`类，数组字面量被转换成一个`NSArray`对象，并成功分配给`schoolSupplies`变量。

当您在Objective-C代码中使用Swift类或者协议时，接入的API会将全部所有类型的Swift数组代替为`NSArray`。若您将一个`NSArray`对象传递给Swift的API并要求数组元素为一个新的类型，运行时就会产生错误。如果Swift API返回一个不能被转换为`NSArray`类型的Swift数组，错误也会产生。

###字典
敬请期待

##Foundation数据类型
Swift也提供一种简单便捷的覆盖方法来连接定义再Foundation框架中的数据类型。在`NSSize`和`NSPoint`中使用覆盖方法，在剩下的Swift语言中，你能在它的句法中感受到自然和统一。比如，你可以使用如下语法创建一个`NSSize`类型的结构:

```
let size = NSSize(width: 20, height: 40)
```

覆盖方法也允许你以一种自然的方式调用Foundation的结构函数。

```
let rect = NSRect(x: 50, y: 50, width: 100, height: 100)
let width = rect.width    // equivalent of NSWidth(rect)
let maxX = rect.maxY      // equivalent of NSMaxY(rect)
```
Swift可以将`NSUInteger`和`NSInteger`转换为`Int`类型。这些类型都会在Foundation APIs中变为`Int`类型。在Swift中`Int`常被尽可能地用以连贯性，同时当你要求一个无符号整数类型时，`UInt`类型总是可使用的。

##Foundation函数
在Swift中，`NSLog`可在系统控制台输出信息。您可以像在Objective-C中使用过的语法格式那样使用此函数。

```
NSLog("%.7f", pi)         // Logs "3.1415927" to the console
```
同时，Swift也提供像`print`和`println`那样的输出函数。这些函数简单，粗暴，多效，多归于Swift的字符插入法。这些函数不会在系统控制台输出信息，但在需要的时候却是存在可用的。

Swift中不再存在`NSAssert`函数，取而代之的是`assert`函数。

##Core Foundation
Swift中的Core Foundation类型是一个成熟的类。当出现内存管理注释时，Swift会自动地管理Core Foundation对象的内存，这其中包括你实例化了的Core Foundation对象。在Swift中，你可以自由变换Fundation和Core Foundation类型。你也可以

###重定义类型
当Swift导入Core Foundation类型时，编译器会重映射导入的类型名字。编译器会从每个类型名字的末端移除*Ref*，这是因为所有的Swift类都属于引用类型，因此后缀是多余的。

Core Foundation中的`CFTypeRef`类型会对`Anyobject`类型重映射。所以你以前使用的`CFTypeRef`，现在该换成`AnyObject`了。

###内存管理对象
在Swift中，从annotated APIs返回的Core Foundation对象能够自动进行内存管理--你不再需要调用自身的`CFRetain`，`CFRelease`，或者`CFAutorelease`函数。如果你从自身的C函数和Objective-C方法中返回一个Core Foundation对象，你需要用`CF_RETURNS_RETAINED`或者`CF_RETURNS_NOT_RETAINED`注释这个对象。当Swift代码中包含这些APIs时，编译器会在编译时自动调用内存管理。如果你只调用那些不会间接返回Core Foundation对象的annotated APIs，那么现在你可以跳过本节的剩余部分了。否则，让我们继续学习那些难管理的Core Foundation对象吧。

###Unmanaged Objects
当Swift导入还尚未被注释的APIs时，编译器将不会自动地对返回的Core Foundation对象进行内存管理。Swift将这些返回的Core Foundation对象封闭在一个`Unmanaged<T>`结构中。那些间接返回Core Foundation的对象也是难以管理的。举个例子，这里有一个unannotated的C函数:

```
CFStringRef StringByAddingTwoStrings(CFStringRef string1, CFStringRef string2)
```

这里说明了Swift是怎么导入的:

```
func StringByAddingTwoStrings(CFString!, CFString!) -> Unmanaged<CFString>!
```
假设您从unannotated APIs接收了一个难以管理的对象，在使用它之前，你必须要将它转换为一个能够内存管理的对象。在这方面，Swift可以帮你进行内存管理而不用自己动手。同时，`Unmanaged<T>`结构也提供了两个方法来把一个难以管理的对象转换为一个可内存管理的对象--`takeUnretainedValue()`方法和`takeRetainedValue()`方法。这两个方法会返回原始的，开放的对象类型。您可以根据您实际调用的APIs返回的unretained或retained的对象，来选择哪一方法更合适。
比如，假设这里有一个C函数，这个函数在返回值前不会释放`CFString`对象。在使用这个对象前，您使用takeUnretainedValue()函数，以将它转换为一个能够内存管理的对象。

```
let memoryManagedResult = StringByAddingTwoStrings(str1, str2).takeUnretainedValue()
// memoryManagedResult is a memory managed CFString
```

您也可以在一个非托管的对象中使用`retain()`，`release()`和`autorelease()`方法，但这种做法并不值得推荐。












