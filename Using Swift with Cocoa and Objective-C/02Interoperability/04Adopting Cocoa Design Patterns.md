#采用Cocoa设计模式#
使用Cocoa现有的一些设计模式，是帮助开发者开发一款拥有合理设计思路、稳定的性能、良好的可扩展性应用的有效方法之一。这些模式都依赖于在Objective-C中定义的类。因为Swift与Objective-C的互用性，所以你依然可以在Swift代码中使用这些设计模式。在一些情况下，你甚至可以使用Swift语言的特性扩展或简化这些Cocoa设计模式，使这些设计模式更强大、更易于使用。
###委托###
在Swift和Objective-C中，委托通常由一个定义了交互方法和遵循规范的委托属性的协议表示。与Objective-C相比，当你在Swift中继承一个委托时，虽然继承模式不变，但是内部的实现已经改变了。就像在Objective-C中，在你向委托发送消息之前，不管它是不是`nil`你都会去查看，如果定义的方法是非必须实现的方法，不管委托有没有实现这个方法，你也都会去查看。而在Swift中，通过保持类型安全的特性，可以有效的消除这些繁琐、不必要的行为问题。
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
