##利用现有Objective-C类及框架资源，编写Swift类


交互性使得开发者可以定义Swift类与Object类进行协作。编写Swift的class类时，不仅能够继承Objective-C代码编写的父类，适配Objective-C定义的协议接口，并且还能够利用其它Objective-C的优势功能。这意味着，开发者能够利用在Objective-C中已有的，熟悉的，可靠的类，方法和框架，并结合swift提供的现代化和更有效的语言特性，对以上Object-c可用资源进行扩展和增强。


###继承Objective-C的类


在Swift中，开发者能定义一个子类，使其继承自Object-c代码编写的父类。创建该子类的方式，是在Swift类的名字后面，加上一个冒号（:），冒号后面跟随Object-c的类名。

    SWIFT
    
    import UIKit

    class MySwiftViewController: UIViewController {
        // define the class
    }



开发者能够从Object-c的父类中继承所有的功能。如果开发者要覆盖父类中的方法，需要使用 __override__ 关键字。


###适配协议

在Swift中，开发者能够适配Object-c中定义好的协议。和Swift协议一样，任何需要适配的Object-c协议都是跟在父类后面，用逗号隔开。

    SWIFT
    
	class MySwiftViewController: UIViewController, UITableViewDelegate, 	UITableViewDataSource {
    	// define the class
	}


Object-c协议与Swift协议使用上是一致的。如果开发者在Swift中想引用 __UITableViewDelegate__  协议，可以直接使用__UITableViewDelegate__ （跟在Object－c中引用__id\<UITableViewDelegate>__ 是等价的）。

###编写构造器和析构器

Swift的编译器确保初始化时，在对象里面，构造器不会遗留任何未初始化的属性，这样做能够增加代码的安全性和可预测性。另外，与Object-c语言不同，Swift不提供单独的内存分配方法供开发者调用。当你使用原生的Swift初始化方法，并和Object-c一起协作时，Swift将Object-c的初始化方法转换为Swift的初始化方法。关于如何实现开发者自定义构造器的更多信息，请查看[构造器](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Initialization.html#//apple_ref/doc/uid/TP40014097-CH18)。

当开发者希望在类被释放前，执行额外的清理工作时，开发者需要实现一个析构器来代替  __dealloc__ 方法。在实例被释放前，Swift会自动调用析构器来执行额外的清理工作。Swift调用完子类的析构器后，会自动调用起父类的析构器。当开发者的代码是和Object-c协作或者和继承自Object-c的Swift代码协作时，Swift也会为开发者自动调用Object-C父类里面的 __dealloc__ 方法，关于如何实现开发者自定义析构器的更多信息，请查看[析构器](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Deinitialization.html#//apple_ref/doc/uid/TP40014097-CH19)。

###图形开发环境集成

Swift编译器包含一些属性，使得开发者的Swift对象支持图形化开发功能。和Objecgt-x里一样，你能在Swift里面使用OutLets，actions和实时渲染（live rendering）。

####与Outlets和Action协作

Outlets和Action能够连接开发者的代码和图形开发环境里面的控件对象。在Swift里面使用Outlets和Action，需要在属性和方法声明前插入 __@IBOutlet__ 或者 __@IBAction__ 关键字。当需要使用 __@IBOutlet__ 属性来声明Outlets集合时，可以使用数组类型来解决这个问题。

当开发者在Swift里面声明了一个Outlet时，Swift编译器，将该类型自动的转为弱（weak）、隐式（implicitly）、未打包（unwrapped）的optional（Object-c里面对应指针类型）数据类型，并为它分配一个初始化的空值 __nil__ 。实际上，编译器使用__@IBOutlet weak var name: Type! = nil__ 来代替 __@IBOutlet var name: Type__ 。编译器将该类型转换成了弱（weak）、隐式（implicitly）、未打包（unwrapped）的optional类型，因此开发者就不需要在构造器中为该类型分配一个初始值了。当开发者从故事板（storyboard）或者 __xib__ 文件里面初始化对象class后，定义好的Outlet和这些对象连接在一起了，所以，这些Outlet是隐式的，未包装的。由于创建的outlets一般都是弱关系的，因此默认outlets是弱类型。

举例说明，下面的Swift代码声明了一个拥有Outlet，Outlets集合以及Action的类：

	SWIFT
	
	class MyViewController: UIViewController {
	    
	    @IBOutlet var button: UIButton
    	
    	@IBOutlet var textFields: UITextField[]
    	
    	@IBAction func buttonTapped(AnyObject) {
        	println("button tapped!")
    	}
	}


在 __buttonTapped:__方法中，消息发送者的信息没有使用，因此该方法的参数名字可以被省略。

####实时渲染（live rendering）
图形化开发环境中，开发者能够利用 __@IBDesignable__ 和 __@IBInspectable__ 来创建生动的，可交互的自定义视图（view）。开发者继承 __UIView__ 或者 __NSView__ 来自定义一个视图（view）时，在类的声明前添加 __@IBDesignable__ 属性。当你在图形开发环境里，添加了自定义的视图后（在监视器面板的自定义视图类中进行设置），图形开发环境将在画布上渲染你自定义的视图。


>注意：只能针对框架里对象进行实时渲染

开发者能将 __@IBInspectable__ 添加到用户自定义的视图类的属性前。这样，当开发者将自定义的视图添加到图形开发环境中后，开发者也能在监视器面板中编辑这些属性。

	SWIFT
	
	@IBDesignable
	
	class MyCustomView: UIView {
    	@IBInspectable var textColor: UIColor
	   	@IBInspectable var iconHeight: CGFloat
	    /* ... */
	}



###指明属性特性

在Object-c中，属性通常都有一组特性（Attributes）说明来指明该属性的一些附加信息。在Swift中，开发者可以通过不同的方法来指明属性的这些特性。

####强类型和弱类型
Swift里属性默认都是强类型的。使用 __weak__ 关键字修饰一个属性，能指明其对象存储时是一个弱引用。该关键字仅能修饰optional对象类型。更多的信息，请查阅[特性](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13)。

####读／写和只读
在Swift中，没有 __readwrite__ 和 __readonly__ 特性。当存储一个属性时，使用 __let__ 修饰其为只读；使用 __var__ 修饰其为可读／写。当声明一个计算后的属性时，为其提供一个getter方法，使其成为只读的；提供getter方法和setter方法，使其成为可读／写的。更多信息，请查阅[属性](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13)。

####拷贝
在Swift中，Object-c的 __copy__ 特性被转换为 __@NSCopying__ 属性。这一类的属性必须遵守 __NSCopying__ 协议。更多信息，请查阅[特性](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13)。 

###实现CoreDataManaged对象子类
Core Data提供了基本存储和实现 __NSManagedObject__ 子类的一组属性。在Core Data模型中，与管理对象子类相关的特性或者关系的每个属性定义之前，将 __@NSmanaged__ 特性加入。与Object-c里面的 __@dynamic__ 特性类似， __@NSManaged__ 特性告知Swift的编译器，这个属性的存储和实现将在运行时完成。但是，与 __@dynamic__ 不同的是，  __@NSManaged__ 特性仅供Core Data支持。









