> 翻译：[JaceFu](https://github.com/JaceFu)

> 校正：[LunaticM](https://github.com/LunaticM)


#采用Cocoa设计模式#
-----------------
本页包含内容：

-   [委托（Delegation）](#Delegation)
-   [延迟初始化（Lazy Initialization）](#Lazy_Initialization)
-   [错误报告（Error Reporting）](#Error_Reporting)
-   [键值观察（Key-Value Observing）](#Key-Value_Observing)
-   [Target-Action模式（Target-Action）](#Target_Action)
-   [类型匹配与统一规范（Introspection）](#Introspection)

使用 Cocoa 现有的一些设计模式，是帮助开发者开发一款拥有合理设计思路、稳定的性能、良好的可扩展性应用的有效方法之一。这些模式都依赖于在 Objective-C 中定义的类。因为 Swift 与 Objective-C 的互用性，所以你依然可以在 Swift 代码中使用这些设计模式。在一些情况下，你甚至可以使用 Swift 语言的特性扩展或简化这些 Cocoa 设计模式，使这些设计模式更强大、更易于使用。

<a name="Delegation"></a>
##委托（Delegation）##
在 Swift 和 Objective-C 中，委托通常由一个定义交互方法和遵循规范的委托属性的协议表示。与 Objective-C 相比，当你在 Swift 中继承一个委托时，虽然继承模式不变，但是内部的实现已经改变了。就像在 Objective-C 中，在你向委托发送消息之前，不管它是不是 `nil` 你都会去查看，如果定义的方法是非必须实现的方法，不管委托有没有实现这个方法，你也都会去查看。而在 Swift 中，通过保持类型安全的特性，可以有效的消除这些繁琐、不必要的行为问题。<br />
下面列出的代码可以说明这个过程：<br />
1. 检查 `myDelegate` 不为 `nil`。<br />
2. 检查 `myDelegate` 是否实现了继承的 `window:willUseFullScreenContentSize:` 方法。<br />
3. 如果` myDelegate` 不为 `nil` 并且实现了 `window:willUseFullScreenContentSize:` 方法，那么调用该方法，将该方法的返回值分配给名为 `fullScreenSize` 的属性。<br />
4. 将该方法的返回值输出在控制台。<br />

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

<a name="Lazy_Initialization"></a>
##延迟初始化（Lazy Initialization）##
你可以在 [Lazy Stored Properties](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html#//apple_ref/doc/uid/TP40014097-CH14) 中了解到更多关于延迟初始化的信息。

<a name="Error_Reporting"></a>
##错误报告（Error Reporting）##
Swift 中的错误报告模式沿用了 Objective-C 的模式，但 Swift 中不定值返回值的新特性给我们带来了额外的好处。举个很简单的例子，你用 `Bool` 值作为一个函数的返回值，用于标识该函数是否执行成功，当你需要输出错误信息时，你可以在函数中添加一个 `NSErrorPointer` 类型的输出参数 `NSError`。这个类型类似 Objective-C 中的 `NSError **`，并增加了内存安全性和非强制性的传参。你可以使用 `&` 运算符作为前缀引用一个不定值 `NSError` 类型作为 `NSErrorPointer` 对象传递错误信息。如下面的代码所示：<br />

```swift
var writeError : NSError?
let written = myString.writeToFile(path, atomically: false,
    encoding: NSUTF8StringEncoding,
    error: &writeError)
if !written {
    if let error = writeError {
        println("write failure: \(error.localizedDescription)")
    }
}
```

当你实现自己的方法时，你需要配置一个 `NSErrorPointer` 对象，并将 `NSErrorPointer` 对象的 `memory` 属性设为你创建的 `NSError` 对象。首先检查调用者传递的参数，确保它是一个非 `nil` 的 `NSError` 对象。

```swift
func contentsForType(typeName: String! error: NSErrorPointer) -> AnyObject! {
    if cannotProduceContentsForType(typeName) {
        if error {
            error.memory = NSError(domain: domain, code: code, userInfo: [:])
        }
        return nil
    }
    // ...
}
```
<a name="Key-Value_Observing"></a>
##键值观察（Key-Value Observing）##
尽请期待。

<a name="Target_Action"></a>
##Target-Action模式（Target-Action）##
当有特定事件发生，需要一个对象向另一个对象发送消息时，我们通常采用 Cocoa 的 Target-Action 设计模式。Swift 和 Objective-C 中的 Target-Action 模型基本类似。在 Swift 中，你可以使用 `Selector` 类型达到 Objective-C 中 selectors 的效果。请在 [Objective-C Selectors](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html#//apple_ref/doc/uid/TP40014216-CH4-XID_37) 中查看在 Swift 中使用 Target-Action 设计模式的示例。

<a name="Introspection"></a>
##类型匹配与统一规范（Introspection）##
在 Objective-C 中，你可以使用 `isKindOfClass:` 方法检查某个对象是否是指定类型，可以使用 `conformsToProtocol:` 方法检查某个对象是否遵循特定协议的规范。在 Swift 中，你可以使用 `is` 运算符完成上述的功能，或者也可以使用 `as?` 向下匹配指定类型。<br />
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
请在 [Type Casting](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TypeCasting.html#//apple_ref/doc/uid/TP40014097-CH22) 中查看更多信息。<br />
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
