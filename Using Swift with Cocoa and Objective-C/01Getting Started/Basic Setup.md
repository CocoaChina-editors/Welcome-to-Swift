# 基本设置

-----------------

本页包含内容：

-   [建立你的 Swift 环境](#setting_up_your_swift_environment)
-   [理解 Swift 导入过程](#understanding_the_swift_import_process)

>重要事项：
这篇文章初步介绍了在开发中用到的 API 或技术。苹果公司提供这些信息来帮助您规划本文所说明的技术和接口以用于苹果的产品上。这些信息会改变，并且根据这篇文章所实现的软件应该在最新的操作系统并根据最新的文档测试。本文档的新版本，可能在未来通过技术和 API 的 seeds 版本来提供

Swift 被设计用来无缝兼容 Cocoa 和 Objective-C 。在 Swift 中，你可以使用 Objective-C 的 API（包括系统框架和你自定义的代码），你也可以在 Objective-C中 使用 Swift 的 API。这种兼容性使 Swift 变成了一个简单、方便并且强大的工具集成到你的 Cocoa 应用开发工作流程中。

这篇指南包括了三个有关兼容性的重要方面方便你更好地利用来开发 Cocoa 应用：

* **互用性** 使你将 Swift 和 Objective-C 相接合，允许在 Objective-C 中使用 Swift 的 Class 并且当你在写 Swift 代码时利用熟悉的 Cocoa Class、Pattern、Practice。
* **混合和匹配** 允许你创建结合了 Swift 和 Objective-C 文件的混合语言应用，他们能更彼此进行通信。
* **迁移** 由于以上两点，从已经存在的 Objective-C 代码迁移到 Swift 是非常简单的，使得用最新的 Swift 特性代替你的 Objective-C 应用部分内容成为了可能。

在你开始学习这些特性前，你需要对如何建立 Swift 环境来访问 Cocoa 系统框架有个大体了解。

<a name="setting_up_your_swift_environment"></a>
##建立你的 Swift 环境

为了开始体验在 Swift 中访问 Cocoa 框架，使用 Xcode 的一个模板来创建一个基于 Swift 应用。

<a name="understanding_the_swift_import_process"></a>
###在 Xcode 中创建一个 Swift 项目

1.选择 File > New > Project > (iOS or OS X) > Application > your *template of choice*。

2.点击 Language 弹出菜单并选择 Swift。

![image](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/Art/newproject_2x.png)

Swift 项目的结构几乎和 Objective-C 项目一模一样，只有一个重要的区别：Swift 没有头文件。在实现和接口之间没有显示的刻画，所以一个指定 Class 中的所有信息都存储在单独的 .swift 文件中。
现在开始，你可以开始体验在 AppDelegate 中写 Swift 代码，或者你可以创建一个 Swift Class 通过选择 File > New > File > (iOS or OS X) > Other > Swift。

##理解 Swift 导入过程
在你建立 Xcode 项目后，你可以在 Swift 里导入任意用 Objective-C 来工作的 Cocoa 平台框架。
任意 Objective-C 的框架（或 C 类库）将作为一个 *module*，能直接导入到 Swift 中。这些包括了所有 Objective-C 系统框架-比如 Foundation、UIKit 和 SpriteKit，就像系统支持公共 C 类库。举个例子，想导入 Foundation，只要简单地添加 import 到你写的 Swift 文件的顶部。

>SWIFT：
`import Foundation`

这个 import 导入了所有 Foundation 的 API，包括 NSDate，NSURL，NSMutableData，并且他们的所有方法、属性和类别都可以在 Swift 中直接使用。

导入过程是非常简洁的。Objective-C 框架在头文件中申明 API。在 Swift 中，那些头文件被编译成 Objective-C 的 module，接着被导入到 Swift 作为 Swift 的 API。导入决定了 Objective-C 的 Function，Class，Method 和 Type 如何在 Swift 中出现。对于 Function 和 Method，这个过程影响他们的参数和返回值。导入过程可以做下面这些事情：

* 重映射确定的 Objective-C 类型到 Swift 中的同等类型，就像 id 到 AnyObject
* 重映射确定的 Objective-C 核心类型到 Swift 中的替代类型， 就像NSString 到 String
* 重映射确定的 Objective-C 概念到 Swift 中相匹配的概念，如 pointers 到 optionals

在互用性章节，你将会了解到更多关于这些映射如何在你的 Swift 代码进行取舍。
导入 Swift 的模型到 Objective-C 和从 Objective-C 导入Swift是非常相似的。Swift 申明它的 API，比如一个框架作为 Swift modules。同时这些 Swift modules 会生成 Objective-C 的头文件。这些头文件可以映射回 Objective-C 的 API 中。一些 Swift 的 API 不映射回 Objective-C 因为他们取舍了语言特性发现这些在 Objective-C 中不可用。关于在 Objective-C 中使用 Swift 的更多特性，请参看[在同一项目中使用 Swift 和 Objective-C](http://)。

>注意：
你不能直接把 C++ 代码导入 Swift。解决办法是为 C++ 代码创建一个 Objective-C 或者 C 的封装。