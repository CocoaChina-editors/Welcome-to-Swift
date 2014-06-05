#采用Cocoa设计模式#
使用Cocoa现有的一些设计模式，是帮助开发者开发一款拥有合理设计思路、稳定的性能、良好的可扩展性应用的有效方法之一。这些模式都依赖于在Objective-C中定义的类。因为Swift与Objective-C的互用性，所以你依然可以在Swift代码中使用这些设计模式。在一些情况下，你甚至可以使用Swift语言的特性扩展或简化这些Cocoa设计模式，使这些设计模式更强大、更易于使用。

###委托###
在Swift和Objective-C中，委托通常由一个定义了交互方法和遵循规范的委托属性的协议表示。与Objective-C相比，当你在Swift中继承一个委托时，虽然继承模式不变，但是内部的实现已经改变了。就像在Objective-C中，在你向委托发送消息之前，不管它是不是`nil`你都会去查看，如果定义的方法是非必须实现的方法，不管委托有没有实现这个方法，你也都会去查看。而在Swift中，通过保持类型安全的特性，可以有效的消除这些繁琐、不必要的行为问题。<br />
下面列出的代码可以说明这个过程：<br />
1. 检查`myDelegate`不为`nil`。<br />
2. 检查`myDelegate`是否实现了继承的`window:willUseFullScreenContentSize:`方法。<br />
3. 如果`myDelegate`不为`nil`并且实现了`window:willUseFullScreenContentSize:`方法，那么调用该方法，将该方法的返回值分配给名为`fullScreenSize`的属性。<br />
4. 将该方法的返回值输出在控制台。<br />

```
// @inteface MyObject : NSObject
// @property (nonatomic, weak) id<NSWindowDelegate> delegate;
// @end
if let fullScreenSize = myDelegate?.window?(myWindow, willUseFullScreenContentSize: mySize) {
    println(NSStringFromSize(fullScreenSize))
}
```

> 注意：
> 在一个完全使用Swift编写的app中，在定义`delegate`属性时，它作为一个不定值的`NSWindowDelegate`对象，并将初始值设为`nil`。

###延迟初始化###
你可以在[Lazy Stored Properties](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html#//apple_ref/doc/uid/TP40014097-CH14)中了解到更多关于延迟初始化的信息。

###错误报告###
Swift中的错误报告模式沿用了Objective-C的模式，但Swift中不定值返回值的新特性给我们带来了额外的好处。举个很简单的例子，你用`Bool`值作为一个函数的返回值，用于标识该函数是否执行成功，当你需要输出错误信息时，你可以在函数中添加一个`NSErrorPointer`类型的输出参数`NSError`。这个类型类似Objective-C中的`NSError **`，并增加了内存安全性和非强制性的传参。你可以使用`&`运算符作为前缀引用一个不定值`NSError`类型作为`NSErrorPointer`对象传递错误信息。如下面的代码所示：<br />

```
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

当你实现自己的方法时，你需要配置一个`NSErrorPointer`对象，并将`NSErrorPointer`对象的`memory`属性设为你创建的`NSError`对象。首先检查调用者传递的参数，确保它是一个非`nil`的`NSError`对象。

```
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
