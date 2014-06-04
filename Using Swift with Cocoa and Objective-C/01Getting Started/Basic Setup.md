##基础设置
>重要事项
>
这篇文章初步介绍了在开发中用到的API或技术。苹果公司提供这些信息来帮助您规划本文所说明的技术和接口以用于苹果的产品上。这些信息会改变，并且根据这篇文章所实现的软件应该在最新的操作系统并根据最新的文档测试。本文档的新版本，可能在未来通过技术和API的seeds版本来提供

Swift被设计用来无缝兼容Cocoa和Objective-C。在Swift中，你可以使用Objective-C的API（包括系统框架和你自定义的代码），你也可以在Objective-C中使用Swift的API。这种兼容性使Swift变成了一个简单、方便并且强大的工具集成到你的Cocoa应用开发工作流程中。

这篇指南包括了三个有关兼容性的重要方面方便你更好地利用来开发Cocoa应用：

* **互用性** 使你将Swift和Objective-C相接合，允许在Objective-C中使用Swift的Class并且当你在写Swift代码时利用熟悉的Cocoa Class、Pattern、Practice。
* **混合和匹配** 允许你创建结合了Swift和Objective-C文件的混合语言应用，他们能更彼此进行通信。
* **迁移** 由于以上两点，从已经存在的Objective-C代码迁移到Swift是非常简单的，使得用最新的Swift特性代替你的Objective-C应用部分内容成为了可能。

在你开始学习这些特性前，你需要对如何建立Swift环境来访问Cocoa系统框架有个大体了解。

###建立你的Swift环境

为了开始体验在Swi中访问Cocoa框架，使用Xcode的一个模板来创建一个基于Swift应用。

####在Xcode中创建一个Swift项目
1.选择 File > New > Project > (iOS or OS X) > Application > your *template of choice*。
2.点击Language弹出菜单并选择Swift。
![image](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/Art/newproject_2x.png)
Swift项目的结构几乎和Objective-C项目一模一样，只有一个重要的区别：Swift没有头文件。在实现和接口之间没有显示的刻画，所以一个指定Class中的所有信息都存储在单独的.swift文件中。
现在开始，你可以开始体验在AppDelegate中写Swift代码，或者你可以创建一个Swift Class通过选择File > New > File > (iOS or OS X) > Other > Swift。
###理解 Swift 导入过程
在你建立Xcode项目后，你可以在Swift里导入任意用Object-C来工作的Cocoa平台框架。
任何
