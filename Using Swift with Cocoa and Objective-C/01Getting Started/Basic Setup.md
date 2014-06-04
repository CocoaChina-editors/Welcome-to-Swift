# 基本设置

>重要事项
>
这篇文章初步介绍了在开发中用到的API或技术。苹果公司提供这些信息来帮助您规划本文所说明的技术和接口以用于苹果的产品上。这些信息会改变，并且根据这篇文章所实现的软件应该在最新的操作系统并根据最新的文档测试。本文档的新版本，可能在未来通过技术和API的seeds版本来提供

Swift被设计用来无缝兼容Cocoa和Objective-C。在Swift中，你可以使用Objective-C的API（包括系统框架和你自定义的代码），你也可以在Objective-C中使用Swift的API。这种兼容性使Swift变成了一个简单、方便并且强大的工具集成到你的Cocoa应用开发工作流程中。

这篇指南包括了三个有关兼容性的重要方面方便你更好地利用来开发Cocoa应用：

* **互用性** 使你将Swift和Objective-C相接合，允许在Objective-C中使用Swift的Class并且当你在写Swift代码时利用熟悉的Cocoa Class、Pattern、Practice。
* **混合和匹配** 允许你创建结合了Swift和Objective-C文件的混合语言应用，他们能更彼此进行通信。
* **迁移** 由于以上两点，从已经存在的Objective-C代码迁移到Swift是非常简单的，使得用最新的Swift特性代替你的Objective-C应用部分内容成为了可能。

在你开始学习这些特性前，你需要对如何建立Swift环境来访问Cocoa系统框架有个大体了解。

##建立你的Swift环境

为了开始体验在Swi中访问Cocoa框架，使用Xcode的一个模板来创建一个基于Swift应用。

###在Xcode中创建一个Swift项目

1.选择 File > New > Project > (iOS or OS X) > Application > your *template of choice*。

2.点击Language弹出菜单并选择Swift。

![image](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/Art/newproject_2x.png)

Swift项目的结构几乎和Objective-C项目一模一样，只有一个重要的区别：Swift没有头文件。在实现和接口之间没有显示的刻画，所以一个指定Class中的所有信息都存储在单独的.swift文件中。
现在开始，你可以开始体验在AppDelegate中写Swift代码，或者你可以创建一个Swift Class通过选择File > New > File > (iOS or OS X) > Other > Swift。

##理解 Swift 导入过程
在你建立Xcode项目后，你可以在Swift里导入任意用Object-C来工作的Cocoa平台框架。
任意Objective-C的框架（或C类库）将作为一个*module*，能直接导入到Swift中。这些包括了所有Objective-C系统框架-比如Foundation、UIKit和SpriteKit，就像系统支持公共C类库。举个例子，想导入Foundation，只要简单地添加import到你写的Swift文件的顶部。

>SWIFT
>
>import Foundation

这个import导入了所有Foundation的API，包括NSDate, NSURL, NSMutableData，并且他们的所有方法、属性和类别都可以在Swift中直接使用。

导入过程是非常简洁的。Objective-C框架在头文件中申明API。在Swift中，那些头文件被编译成Objective-C的module，接着被导入到Swift作为Swift的API。导入决定了Objective-C的Function, Class, Method和Type如何在Swift中出现。对于Function和Method，这个过程影响他们的参数和返回值。导入过程可以做下面这些事情：

* 重映射确定的Objective-C类型到Swift中的同等类型, 就像 id 到 AnyObject
* 重映射确定的 Objective-C 核心类型到Swift中的替代类型， 就像NSString 到 String
* 重映射确定的 Objective-C 概念到Swift中相匹配的概念，如 pointers 到 optionals

在互用性章节，你将会了解到更多关于这些映射如何在你的Swift代码进行取舍。
导入Swift的模型到Objective-C和从Objective-C导入Swift是非常相似的。Swift申明它的API，比如一个框架作为Swift modules。同时这些Swift modules会生成Objective-C的头文件。这些头文件可以映射回Objective-C的API中。一些Swift的API不映射回Objective-C因为他们取舍了语言特性发现这些在Objective-C中不可用。关于在Objective-C中使用Swift的更多特性，请参看[在同一项目中使用Swift和Objective-C](http://)。

>注意
>
>你不能直接把C++代码导入Swift。解决办法是为C++代码创建一个Objective-C或者C的封装。