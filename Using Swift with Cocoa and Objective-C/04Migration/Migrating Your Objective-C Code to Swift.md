[@xudeheng](https://github.com/xudeheng) 翻译 [Apple](http://www.apple.com) 官方文档 [Migration](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/Migration.html#//apple_ref/doc/uid/TP40014216-CH12-XID_67)

#将Objective-c代码迁移到Swift

迁移工作正好提供了一个重访现有 Objective-C 代码的机会，也可以通过Swift代码来更好的优化app软件架构，逻辑以及性能。直接的说，你将用先前学到的 mix and match以及这两个语言间的互操作性来进行增量迁移工作。Mix-and-match功能使得选择哪些特性和功能来用Swift来实现，哪些依然用Objective-C来实现变得简单。Swift和Objective-C的互用性又使得将这些功能集成到 Objective-C 变得无害。通过这些工具来开放 Swift 的扩展功能并集成到现有的 Objective-C 项目中而完全不必立刻使用 Swift 重写整个项目。

##为你的Objective-c代码做好迁移准备

在开始迁移你的代码之前，请确保你的 Objective-C 和 Swift 代码间有理想的兼容性。这意味着整理并使用 Objective-C 的现代化特性来优化你的现有项目。你的现有代码需要遵循现代编码实践来和 Swift 进行更容易的无缝交互。有个简短的适配练习列表，参看[Adopting Mordern Objective-C](https://developer.apple.com/library/prerelease/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html#//apple_ref/doc/uid/TP40014150)

##迁移过程

最有效迁移代码的方式是基于逐个文件的方式 - 一次完成一个类。由于你不能在 Objective-C 中继承 Swift 类， 最好选择一个没有子类的（译者：从类的继承角度来看，应该先从类族树的叶子节点开始，自底向上的进行迁移操作）。你就可以用单个 .swift 文件来代替对应的 .m 和 .h 文件了。你所有的实现代码和接口将直接放进单个 Swift 文件。你不用再创建头文件了；Xcode 会在你需要引用的时候自动生成头文件。(译者：当然这实在xcode内部机制完成的了，对开发者是透明的)

###准备工作

* 在 Xcode 中:File>New>File>(iOS 或者 OS X) > Other > Swift 为对应的 Objective-C .m 和 .h 文件创建一个 Swift 类。
* 导入相关系统框架
* 如果你希望在 Swift 文件中访问 Objective-C 代码的话，可以填入一个 Objective-C 桥接头。具体的操作步骤，请看 [Importing Code from Within the Same App Target](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-XID_77)。
* 为使你的 Swift 类能在 Objective-C 中访问使用，可以继承 Objective-C 类，或者标记上 @objc 属性。为类指定特殊的名称，以在 Objective-C 中使用，标记上 @objc(#name#), <#name#> 就是在 Objective-C 中引用的 Swift 类名。 更多信息，请看[Swift Type Compatibility](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html#//apple_ref/doc/uid/TP40014216-CH4-XID_36)。

###开始工作
* 你可以通过继承 Objective-C 类，适配 Objective-C 协议，或者更多的方式，来让 Swift 类集成  Objective-C 行为。更多信息，请看[Writing Swift Classes with Objective-C Behavior](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/WritingSwiftClassesWithObjective-CBehavior.html#//apple_ref/doc/uid/TP40014216-CH5-XID_54)
* 当你使用 Objective-C APIs 的时候，你需要知道 Swift 是怎样来翻译某些 Objective-C 特性的。更多信息，请看[Interacting with Objective-C APIs](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html#//apple_ref/doc/uid/TP40014216-CH4-XID_26)
* 当用 Swift 编写用到 Cocoa 框架的代码时，记住某些类型是被桥接的，意味着你可以使用某些 Swift 类型来替代 Objective-C 类型。更多信息，请看[Working with Cocoa Data Types](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/WorkingWithCocoaDataTypes.html#//apple_ref/doc/uid/TP40014216-CH6-XID_40)
* 当你在 Swift 中运用 Cocoa设计模式得时候，请看 [Adopting Cocoa Design Patterns](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/AdoptingCocoaDesignPatterns.html#//apple_ref/doc/uid/TP40014216-CH7-XID_5)获取更多的通用设计模式的转换信息。
* 对于打算将项目从 Objective-C 转换到 Swfit 的人，请看 [Propeties](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13)。
* 在必要的时候，请为 Swift 的属性或方法，通过 @objc(<#name#>) 属性来提供 Objective-C 名称，就像这样：

<pre lang=C>
	var enabled: Bool {
  		@objc(isEnabled) get {
    	/* ... */
  		}
	}
</pre>

* 分别用 func 和 class func 来表示 instance(-) 和 class(+) 方法。
* 声明简单的宏来作为常量，将复杂的宏转换为函数。

###大功告成
* 在你的 Objective-C 代码中更新 import 语句为 #import "模块名-Swift.h"，在[Importing Code from Within the Same App Target](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-XID_77)中曾有提到。
* 在Target 的成员选择框中去掉勾选框来移除原始的 Objective-C .m 文件.不要立刻删除 .m 和 .h 文件，以备解决问题用。
* 如果你给 Swift 类起了一个不同的名字，请使用 Swift 类名代替 Objective-C 名。


##问题解决
对于不同的项目，迁移的经历是不尽相同的。无论怎样，都有一些通用的步骤和工具能帮你解决代码迁移时碰到的问题：

* 记住：你不能在 Objective-C 中继承 Swift 类。因此，被你迁移的类不能有任何的 Objective-C 子类存在于你的应用中。

* 当你迁移一个类到 Swift 的时候，你必须从 target 中移除相关的 .m 文件，以避免编译时提示出现重复的符号等编译错误。

* 为了在 Objective-C 中可以访问并使用，Swift 类必须是一个 Objective-C 类的子类，或者被标记为 @objc。

* 当你在 Objective-C 中使用 Swift 代码的时候，记住 Objective-C 不能理解那些 Swift 的某些特性，请看[Using Swift from Objective-C](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html#//apple_ref/doc/uid/TP40014216-CH10-XID_84)。

* 可以通过 Commond + 点击一个 Swift 类名来查看他生成的头文件。

* Option + 点击一个符号查看更准确的信息，比如它的类型，属性以及文档注释等。
