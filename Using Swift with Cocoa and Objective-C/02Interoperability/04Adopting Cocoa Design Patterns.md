> 翻译：[JaceFu](https://github.com/JaceFu) [ChildhoodAndy](https://github.com/dabing1022)

> 校正：[LunaticM](https://github.com/LunaticM) [ChildhoodAndy](https://github.com/dabing1022)


#采用Cocoa设计模式
-----------------
本页包含内容：

-   [委托（Delegation）](#Delegation)
-   [错误处理（Error Handling）](#error_handling)
-   [键值观察（Key-Value Observing）](#Key-Value_Observing)
-   [Target-Action模式（Target-Action）](#Target_Action)
-   [类型匹配与统一规范（Introspection）](#Introspection)
-   [API 可用性](#API_Availability)

使用 Cocoa 现有的一些设计模式，是帮助开发者开发一款拥有合理设计思路、稳定的性能、良好的可扩展性应用的有效方法之一。这些模式都依赖于在 Objective-C 中定义的类。因为 Swift 与 Objective-C 的互用性，所以你依然可以在 Swift 代码中使用这些设计模式。在一些情况下，你甚至可以使用 Swift 语言的特性扩展或简化这些 Cocoa 设计模式，使这些设计模式更强大、更易于使用。

<a name="Delegation"></a>
## 委托
在 Swift 和 Objective-C 中，委托通常由一个定义交互方法和遵循规范的委托属性的协议表示。与 Objective-C 相比，当你在 Swift 中继承一个委托时，虽然继承模式不变，但是内部的实现已经改变了。就像在 Objective-C 中，在你向委托发送消息之前，不管它是不是 `nil` 你都会去查看，如果定义的方法是非必须实现的方法，不管委托有没有实现这个方法，你也都会去查看。而在 Swift 中，通过保持类型安全的特性，可以有效的消除这些繁琐、不必要的行为问题。

下面列出的代码可以说明这个过程：

1. 检查 `myDelegate` 不为 `nil`。
2. 检查 `myDelegate` 是否实现了继承的 `window:willUseFullScreenContentSize:` 方法。
3. 如果` myDelegate` 不为 `nil` 并且实现了 `window:willUseFullScreenContentSize:` 方法，那么调用该方法，将该方法的返回值分配给名为 `fullScreenSize` 的属性。
4. 将该方法的返回值输出在控制台。

```swift
// @inteface MyObject : NSObject
// @property (nonatomic, weak) id<NSWindowDelegate> delegate;
// @end
if let fullScreenSize = myDelegate?.window?(myWindow, willUseFullScreenContentSize: mySize) {
    println(NSStringFromSize(fullScreenSize))
}
```

> 注意：
> 在一个完全使用 Swift 编写的 app 中，在定义 `delegate` 属性时，它作为一个不定值的 `NSWindowDelegate` 对象，并将初始值设为 `nil`。
 
## 错误处理

在 Cocoa 中，产生错误的方法将`NSError`指针参数作为最后一个参数，当错误产生时，该参数会被`NSError`对象填充。Swift 自动的将 Objective-C 中产生错误的方法转换为 Swift 的原生错误处理功能。

> 注意

> 产生错误的方法，例如代理方法或者采用一个`NSError`对象作为参数的完成处理函数，不会被 Swift 处理为`throw`的方法。

例如，考虑下面的来自于`NSFileManager`的 Objective-C 方法：

```objective-c
- (BOOL)removeItemAtURL:(NSURL *)URL
                  error:(NSError **)error;
```
在 Swift 中，它会被这样的导入：

```swift
func removeItemAtURL(URL: NSURL) throws
```
注意到`removeItemAtURL(_:)`方法被 Swift 导入时，返回值类型为 Void，没有错误参数，而是一个`throws`声明。

如果 Objective-C 方法的最后一个非闭包参数是`NSError **`类型，Swift 则会将之替换为`throws`关键字，以表明该方法可以抛出一个错误。如果 Objective-C 方法的错误参数也是它的第一个参数，Swift 则会尝试通过删除选择器的第一部分中的`AndReturnError`后缀来进一步简化方法的名称，如果存在的话。如果另一种方法是用所得选择器声明的，那么该方法名将不可改变。

如果产生错误的 Objective-C 的方法返回一个用来表示方法调用成功或失败的`BOOL`值，Swift 会把函数的返回值转换为`Void`。同样的，如果产生错误的 Objective-C 方法返回一个`nil`值来表明方法调用的失败，Swift 会把函数的返回值转换为非可选值类型。

否则，如果没有约定可以推断，该方法保持不变。



### 捕获和处理错误

在 Objective-C 中，错误处理是可选的，意味着方法产生的错误会被忽略除非你提供了一个错误指针。在 Swift 中，调用一个会抛出错误的方法要求显示的进行错误处理。

下面是如何在 Objective-C 中处理调用方法产生的错误：

```objective-c
NSFileManager *fileManager = [NSFileManager defaultManager];
NSURL *URL = [NSURL fileURLWithPath:@"/path/to/file"];
NSError *error = nil;
BOOL success = [fileManager removeItemAtURL:URL error:&error];
if (!success && error) {
    NSLog(@"Error: %@", error.domain);
}
```

下面是 Swift 中等同的代码：

```swift
let fileManager = NSFileManager.defaultManager()
let URL = NSURL.fileURLWithPath("/path/to/file")
do {
    try fileManager.removeItemAtURL(URL)
} catch let error as NSError {
    print("Error: \(error.domain)")
}
```

### 抛出错误

如果一个错误出现在了 Objective-C 方法中，那么该错误被用来填充方法的错误指针参数。

```swift
// an error occurred
if (errorPtr) {
   *errorPtr = [NSError errorWithDomain:NSURLErrorDomain
                                   code:NSURLErrorCannotOpenFile
                               userInfo:nil];
}
```

如果一个错误出现在了 Swift 方法中，那么该错误便会被抛出，并且会自动的传递给调用者：

```swift
// an error occurred
throw NSError(domain: NSURLErrorDomain, code: NSURLErrorCannotOpenFile, userInfo: nil)
```

如果 Objective-C 代码调用 Swift 方法抛出了错误，那么该错误会被自动的传递给桥接的 Objective-C 方法的错误指针参数。

例如，考虑`NSDocument`中的`readFromFileWrapper(_:ofType:)`方法。在 Objective-C 中，这个方法的最后一个参数是`NSError **`。当在 Swift 的`NSDocument`的子类中重写该方法时，该方法会用`throws`替代错误指针参数。

```swift
class SerializedDocument: NSDocument {
    static let ErrorDomain = "com.example.error.serialized-document"
    
    var representedObject: [String: AnyObject] = [:]
    
    override func readFromFileWrapper(fileWrapper: NSFileWrapper, ofType typeName: String) throws {
        guard let data = fileWrapper.regularFileContents else {
            throw NSError(domain: NSURLErrorDomain, code: NSURLErrorCannotOpenFile, userInfo: nil)
        }
        
        if case let JSON as [String: AnyObject] = try NSJSONSerialization.JSONObjectWithData(data, options: []) {
            self.representedObject = JSON
        } else {
            throw NSError(domain: SerializedDocument.ErrorDomain, code: -1, userInfo: nil)
        }
    }
}
```
如果方法不能够使用常规的文件的内容来创建一个对象，则会抛出一个`NSError`对象。如果方法是从 Swift 代码中调用的，那么该错误会被传递到它的调用域。如果该方法是在 Objective-C 代码中被调用，错误将会传递到错误指针参数里。

在 Objective-C 中，错误处理是可选的，意味着方法产生的错误会被忽略除非你提供了一个错误指针。在 Swift 中，调用一个会抛出错误的方法要求显式的进行错误处理。

> 注意

> 尽管 Swift 的错误处理类似 Objective-C 的异常处理，但它是完全独立的功能。如果一个 Objective-C 方法抛出了一个运行时异常，Swift 则会触发一个运行时错误。没有办法直接在 Swift 中恢复来自 Objective-C 的异常。任何在 Objective-C 代码中的异常处理行为必须用 Swift 来实现。

<a name="Key-Value_Observing"></a>
##键值观察

键值观察是一种机制，该机制允许对象获得其他对象的特定属性的变化的通知。只要你的类继承自 NSObject 类，你便可在 Swift 类里使用键值观察。你可以在 Swift 中使用下面三步来实现键值观察：

1. 为你想要观察的属性添加动态修改符。关于`dynamic`更多信息，请见[Requiring Dynamic Dispatch](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html#//apple_ref/doc/uid/TP40014216-CH4-ID57)

```swift
class MyObjectToObserve: NSObject {
    dynamic var myDate = NSDate()
    func updateDate() {
        myDate = NSDate()
    }
}
```

2. 创建一个全局上下文变量。

```swift
private var myContext = 0
```
3. 为键-路径增加一个观察者，重写`observeValueForKeyPath:ofObject:change:context:`函数，并且在`deinit`中移除观察者。

```swift
class MyObserver: NSObject {
    var objectToObserve = MyObjectToObserve()
    override init() {
        super.init()
        objectToObserve.addObserver(self, forKeyPath: "myDate", options: .New, context: &myContext)
    }    
    
    override func observeValueForKeyPath(keyPath: String?, ofObject object: AnyObject?, change: [NSObject : AnyObject]?, context: UnsafeMutablePointer<Void>) {
        if context == &myContext {
            if let newValue = change?[NSKeyValueChangeNewKey] {
                print("Date changed: \(newValue)")
            }
        } else {
            super.observeValueForKeyPath(keyPath, ofObject: object, change: change, context: context)
        }
    }
    
    deinit {
        objectToObserve.removeObserver(self, forKeyPath: "myDate", context: &myContext)
    }
}
```

<a name="Target_Action"></a>
##Target-Action模式（Target-Action）##
当有特定事件发生，需要一个对象向另一个对象发送消息时，我们通常采用 Cocoa 的 Target-Action 设计模式。Swift 和 Objective-C 中的 Target-Action 模型基本类似。在 Swift 中，你可以使用 `Selector` 类型达到 Objective-C 中 selectors 的效果。请在 [Objective-C Selectors](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html#//apple_ref/doc/uid/TP40014216-CH4-ID59) 中查看在 Swift 中使用 Target-Action 设计模式的示例。

<a name="Introspection"></a>
##类型匹配与统一规范（Introspection）##
在 Objective-C 中，你可以使用 `isKindOfClass:` 方法检查某个对象是否是指定类型，可以使用 `conformsToProtocol:` 方法检查某个对象是否遵循特定协议的规范。在 Swift 中，你可以使用 `is` 运算符完成上述的功能，或者也可以使用 `as?` 向下匹配指定类型。

你可以使用 `is` 运算符检查一个实例是否是指定的子类。如果该实例是指定的子类，那么 `is` 运算结果为 `true`，反之为 `false`。

```swift
if object is UIButton {
    // object is of type UIButton
} else {
    // object is not of type UIButton
}
```
你也可以使用 `as?` 运算符尝试向下匹配子类型，`as?` 运算符返回不定值，结合 `if-let` 语句使用。

```swift
if let button = object as? UIButton {
    // object is successfully cast to type UIButton and bound to button
} else {
    // object could not be cast to type UIButton
}
```
请在 [Type Casting](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TypeCasting.html#//apple_ref/doc/uid/TP40014097-CH22) 中查看更多信息。

检查匹配协议的语法与检查匹配类的语法是一样的，下面是使用 `as?` 检查匹配协议的示例：

```swift
if let dataSource = object as? UITableViewDataSource {
    // object conforms to UITableViewDataSource and is bound to dataSource
} else {
    // object not conform to UITableViewDataSource
}
```

注意，当做完匹配之后，`dataSource` 会转换为  `UITableViewDataSource` 类型，所以你只能访问和调用`UITableViewDataSource` 协议定义的属性和方法。当你想进行其他操作时，必须将其转换为其他的类型。<br />
可以在 [Protocols](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25) 查看更多相关信息。

<a name="API_Availability"></a>
## API 可用性

一些类和方法并不是在你的应用所有平台的所有版本都可用。为了确保你的应用功能上能够适应差异，你需要检查这些 API 的可用性。

在 Objective-C 中，我们使用`respondsToSelector:`和`instancesRespondToSelector:`方法来检查一个类或者实例方法是否可用。如果没有检查，调用方法则会抛出`NSInvalidArgumentException`“unrecognized selector sent to instance”异常。例如，`requestWhenInUseAuthorization`方法只在 iOS8.0 和 OS X 10.10 中对`CLLocationManager`实例可用。

```objective-c
if ([CLLocationManager instancesRespondToSelector:@selector(requestWhenInUseAuthorization)]) {
  // 方法可用
} else {
  // 方法不可用
}
```
在 Swift 中，尝试着调用一个目标平台版本不支持的方法将会报出编译时错误。

下面是上一个例子，采用 Swift 编写：

```swift
let locationManager = CLLocationManager()
locationManager.requestWhenInUseAuthorization()
// error: only available on iOS 8.0 or newer
```

如果应用的目标低于 ios8.0 或者 OSX10.10，`requestWhenInUseAuthorization()`方法则不可用，所以编译器会报告错误。

Swift 代码可以使用 API 可用性来作为运行时的条件判断。可用性检查可以使用在一个控制流语句的条件中，例如`if`,`guard`或者`while`语句。

拿前面的例子举例，你可以使用`if`语句来检查可用性，只有当方法在运行时可用时方可调用`requestWhenInUseAuthorization()`。

```swift
let locationManager = CLLocationManager()
if #available(iOS 8.0, OSX 10.10, *) {
    locationManager.requestWhenInUseAuthorization()
}
```

或者，你可以使用`guard`语句来检查可用性，除非当前的目标符合规定要求，否则将会退出作用域。这种方法简化了处理不同平台功能的逻辑。

```swift
let locationManager = CLLocationManager()
guard #available(iOS 8.0, OSX 10.10, *) else { return }
locationManager.requestWhenInUseAuthorization()
```

每个平台参数包括下面列出的平台名称，后面跟着相应的版本号。最后一个参数是一个星号（*），是用来处理未来潜在的平台。

平台名称：

-  iOS
-  iOSApplicationExtension
-  OSX
-  OSXApplicationExtension
-  watchOS

所有的 Cocoa API 都提供有可用性信息，所以你可以很自信的编写应用所针对的平台的代码。

你可以通过 @available 属性来标注声明从而简化你的 API 的可用性检查。@available 属性使用和 #available 同样的语法来做运行时检查，参数都以逗号隔开平台版本需求。

例如：

```swift
@available(iOS 8.0, OSX 10.10, *)
func useShinyNewFeature() {
    // ...
}
```

> 注意

> 使用 @available 属性标记的方法可以安全的使用满足特定平台需求的可用 API 而不用显式的做可用性检查。