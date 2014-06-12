> 翻译：[halinuya](https://github.com/halinuya)

> 校对：[song-buaa](https://github.com/song-buaa) [ChildhoodAndy](https://github.com/dabing1022)


# 使用Objective-C特性编写Swift类
--------------------------------

互用性（互操作性）使开发者可以定义融合了 Objective-C 语言特性的Swift类。编写 Swift 类时，不仅可以继承 Objective-C 语言编写的父类，采用 Objective-C 的协议，还可以利用 Objective-C 的一些其它功能。这意味着，开发者可以基于 Objective-C 中已有的熟悉、可靠的类、方法和框架来创建 Swift 类，并结合 Swift 提供的现代化和更有效的语言特点对其进行优化。


## 继承Objective-C的类


在 Swift 中，开发者可以定义一个子类，该子类继承自使用 Objective-C 编写的类。创建该子类的方法是，在 Swift 的类名后面加上一个冒号（:），冒号后面跟上 Objective-C 的类名。

    SWIFT
    
    import UIKit

    class MySwiftViewController: UIViewController {
        // 定义类
    }



开发者能够从 Objective-C 的父类中继承所有的功能。如果开发者要覆盖父类中的方法，不要忘记使用`override`关键字。


## 采用协议

在 Swift 中，开发者可以采用 Objective-C 中定义好的协议。和 Swift 协议一样，所有 Objective-C 协议都写在一个用逗号隔开的列表中，跟在所在类的父类名后面（如果它有父类的话）。

    SWIFT
    
	class MySwiftViewController: UIViewController, UITableViewDelegate, 	UITableViewDataSource {
    	// 定义类
	}


Objective-C 协议与 Swift 协议使用上是一致的。如果开发者想在 Swift 代码中引用 `UITableViewDelegate`协议，可以直接使用`UITableViewDelegate`（跟在 Objective-C 中引用`id<UITableViewDelegate\>`是等价的）。

## 编写构造器和析构器

Swift 的编译器确保在初始化时，构造器不允许类里有任何未初始化的属性，这样做能够增加代码的安全性和可预测性。另外，与 Objective-C 语言不同，Swift 不提供单独的内存分配方法供开发者调用。当你使用原生的 Swift 初始化方法时（即使是和 Objective-C 类协作），Swift 会将 Objective-C 的初始化方法转换为 Swift 的初始化方法。关于如何实现开发者自定义构造器的更多信息，请查看[构造器](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Initialization.html#//apple_ref/doc/uid/TP40014097-CH18)。

当开发者希望在类被释放前，执行额外的清理工作时，需要执行一个析构过程来代替`dealloc`方法。在实例被释放前，Swift 会自动调用析构器来执行析构过程。Swift 调用完子类的析构器后，会自动调用父类的析构器。当开发者使用 Objective-C 类或者是继承自 Objective-C 类的 Swift 类时，Swift 也会自动为开发者调用这个类的父类里的`dealloc`方法。关于如何实现开发者自定义析构器的更多信息，请查看[析构器](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Deinitialization.html#//apple_ref/doc/uid/TP40014097-CH19)。

## 集成Interface Builder

Swift 编译器包含一些属性，使得开发者的 Swift 类集成了 Interface Builder 里的一些特色功能。和 Objective-C 里一样，你能在 Swift 里面使用 OutLets，actions 和实时渲染（live rendering）。

### 使用Outlets和Action

使用 Outlets 和 Action 可以连接源代码和 Interface Builder 的 UI 对象。在Swift里面使用 Outlets 和 Action，需要在属性和方法声明前插入`@IBOutlet`或者`@IBAction`关键字。声明一个 Outlet集合同样是用`@IBOutlet`属性，即为类型指定一个数组。

当开发者在 Swift 里面声明了一个 Outlet 时，Swift 编译器会自动将该类型转换为弱（weak）、隐式（implicitly）、未包装（unwrapped）的 optional（Object-c里面对应指针类型）数据类型，并为它分配一个初始化的空值`nil`。实际上，编译器使用`@IBOutlet weak var name: Type! = nil`来代替 `@IBOutlet var name: Type`。编译器将该类型转换成了弱（weak）、隐式（implicitly）、未包装（unwrapped）的 optional 类型，因此开发者就不需要在构造器中为该类型分配一个初始值了。当开发者从故事板（storyboard）或者`xib`文件里面初始化对象 class 后，定义好的 Outlet 和这些对象连接在一起了，所以，这些 Outlet 是隐式的，未包装的。由于创建的 outlets 一般都是弱关系，因此默认 outlets 是弱类型。

例如，下面的 Swift 代码声明了一个拥有 Outlet、Outlets 集合和 Action 的类：

	SWIFT
	
	class MyViewController: UIViewController {
	    
	    @IBOutlet var button: UIButton
    	
    	@IBOutlet var textFields: UITextField[]
    	
    	@IBAction func buttonTapped(AnyObject) {
        	println("button tapped!")
    	}
	}


在`buttonTapped`方法中，消息发送者的信息没有被使用，因此可以省略该方法的参数名。

### 实时渲染（live rendering）
开发者可以在Interface Builder中用`@IBDesignable`和`@IBInspectable`来创建生动、可交互的自定义视图（view）。开发者继承`UIView`或者`NSView`来自定义一个视图（view）时，可以在类声明前添加`@IBDesignable`属性。当你在 Interface Builder 里添加了自定义的视图后（在监视器面板的自定义视图类中进行设置），Interface Builder 将在画布上渲染你自定义的视图。


>注意：只能针对框架里对象进行实时渲染

你也可以将`@IBInspectable`属性添加到和用户定义的运行时属性兼容的类型属性里。这样，当开发者将自定义的视图添加到 Interface Builder 里后，就可以在监视器面板中编辑这些属性。

	SWIFT
	
	@IBDesignable
	
	class MyCustomView: UIView {
    	@IBInspectable var textColor: UIColor
	   	@IBInspectable var iconHeight: CGFloat
	    /* ... */
	}



## 指明属性特性

在 Objective-C 中，属性通常都有一组特性（Attributes）说明来指明该属性的一些附加信息。在 Swift 中，开发者可以通过不同的方法来指明属性的这些特性。

### 强类型和弱类型

Swift 里属性默认都是强类型的。使用`weak`关键字修饰一个属性，能指明其对象存储时是一个弱引用。该关键字仅能修饰 optional 对象类型。更多的信息，请查阅[特性](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13)。

### 读／写和只读

在 Swift 中，没有`readwrite`和`readonly`特性。当声明一个存储型属性时，使用`let`修饰其为只读；使用`var`修饰其为可读／写。当声明一个计算型属性时，为其提供一个 getter 方法，使其成为只读的；提供 getter 方法和 setter 方法，使其成为可读／写的。更多信息，请查阅[属性](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13)。

### 拷贝

在 Swift 中，Objective-C 的`copy`特性被转换为`@NSCopying`属性。这一类的属性必须遵守 `NSCopying`协议。更多信息，请查阅[特性](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13)。 

## 实现Core Data Managed Object子类

Core Data 提供了基本存储和实现`NSManagedObject`子类的一组属性。在 Core Data 模型中，与管理对象子类相关的特性或者关系的每个属性定义之前，将`@NSmanaged`特性加入。与 Objective-C 里面的 `@dynamic`特性类似，`@NSManaged`特性告知 Swift 编译器，这个属性的存储和实现将在运行时完成。但是，与`@dynamic`不同的是，`@NSManaged`特性仅在 Core Data 支持中可用。









