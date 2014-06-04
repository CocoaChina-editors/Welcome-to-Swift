##自动引用计数
Swift使用自动引用计数(ARC)来跟踪并管理应用使用的内存。大部分情况下，这意味着在Swift语言中，内存管理工作的很好，不需要自己去考虑内存管理的事情。当实例不再被使用时，ARC自动释放这些类的实例(class instance)所占用的内存。

然而，在某些情况下，为了自动的管理内存空间，ARC需要了解关于你的代码片段之间的关系的更多信息。本章描述了这些情况，并向大家展示如何打开ARC来管理应用的所有内存空间。

NOTE
引用计数只应用在类的实例。结构体(Structure)和枚举类型是值类型，并非引用类型，不是以引用的方式来存储和传递的。

###ARC是如何工作的
每次创建一个类的实例，ARC就会分配一个内存块，用来存储这个实例的相关信息。这个内存块保存着实例的类型，以及这个实例相关的属性的值。

当实例不再被使用时，ARC释放这个实例使用的内存，使这块内存可作它用。这保证了不再被使用时，类实例不会占用内存空间。

但是，如果ARC释放了仍在使用的实例，那么你就不能再访问这个实例的属性或者调用它的方法。如果你仍然试图访问这个实例，应用就有很大机会崩溃。

为了保证不会发生上述的情况，ARC跟踪与类的实例相关的属性、常量以及变量的数量。只要有一个有效的引用，ARC都不会释放这个实例。

为了让这变成现实，只要你将一个类的实例赋值给一个属性或者常量或者变量，这个属性、常量或者变量就是这个实例的*强引用(strong reference)*。之所以称之为“强”引用，是因为它牢牢的掌握着这个实例，并且只要这个强引用还存在，就不允许销毁实例。

###ARC在行动
下面的例子展示了ARC是如何工作的。本例定义了一个简单的类，类名是Person，并定义了一个名为name的存储常量的属性：

	class Person {
    	let name: String 
    	init(name: String) {
       		self.name = name
        	println("\(name) is being initialized")
    	} 
    
    	deinit {
        	println("\(name) is being deinitialized")
    	}
	}

类Person有一个初始化程序（initializer），设置这个实例的name属性，打印一条消息来指示初始化正在进行。类Person还有一个deinitializer方法，当销毁一个类的实例时，会打印一条消息。

接下来的代码片段定义了三个Person?类型的变量，这些变量用来创建多个引用，这些引用都引用紧跟着的代码所创建的Person对象。因为这些变量都是可选类型（Person?，而非Person），因此他们都被自动初始化为nil，并且当前并没有引用一个Person的实例。

	var reference1: Person?
	var reference2: Person?
	var reference3: Person?
	
现在我们创建一个新的Person实例，并且将它赋值给上述三个变量重的一个：

	reference1 = Person(name: "John Appleseed")
	// prints "Jonh Appleseed is being initialized"
	
注意，消息“John Appleseed is being initialized”在调用Person类的初始化程序时打印。这印证初始化确实发生了。

因为Person的实例赋值给了变量reference1，所以reference1是Person实例的强引用。又因为至少有这一个强引用，ARC就保证这个实例会保存在内存重而不会被销毁。

如果将这个Person实例赋值给另外的两个变量，那么将建立另外两个指向这个实例的强引用：

	reference2 = reference1
	reference3 = reference2
	
现在，这一个Person实例有三个强引用。

如果你通过赋值nil给两个变量来破坏其中的两个强引用（包括原始的引用），只剩下一个强引用，这个Person实例也不会被销毁：

	reference1 = nil
	reference2 = nil
	
直到第三个也是最后一个强引用被破坏，ARC才会销毁Person的实例，这时，有一点非常明确，你无法在使用Person实例：

	referenece3 = nil
	// 打印 “John Appleseed is being deinitialized”
	

###类实例间的强引用循环
在上面的例子中，ARC可以追踪Person实例的引用数量，并且在它不再被使用时销毁这个实例。

然而，我们有可能会写出这样的代码，一个类的实例永远不会有0个强应用。在两个类实例彼此保持对方的强引用，使得每个实例都使对方保持有效时会发生这种情况。我们称之为*强引用循环*。

通过用弱引用或者无主引用来取代强引用，我们可以解决强引用循环问题。在开始学习如何解决强引用循环问题之前，理解它产生的原因会很有帮助。

下面的例子展示了一个强引用循环是如何不小心产生的。例子定义了两个类，分别叫Person和Apartment，这两个类建模了一座公寓以及它的居民：

	class Person {
	    let name: String
    	init(name: String) { self.name = name }
    	var apartment: Apartment?
    	deinit { println("\(name) is being deinitialized") }
	}
 
	class Apartment {
    	let number: Int
    	init(number: Int) { self.number = number }
    	var tenant: Person?
    	deinit { println("Apartment #\(number) is being deinitialized") }
	}
	
每个Person实例拥有一个String类型的name属性以及一个被初始化为nil的apartment可选属性。apartment属性是可选的，因为一个人并不一定拥有一座公寓。

类似的，每个Apartment实例拥有一个Int类型的number属性以及一个初始化为nil的tenant可选属性。tenant属性是可选的，因为一个公寓并不一定有居民。

这两个类也都定义了初始化程序，打印消息表明这个类的实例正在被初始化。这使你能够看到Person和Apartment的实例是否像预期的那样被销毁了。

下面的代码片段定义了两个可选类型变量，john和number73，分别被赋值为特定的Apartment和Person的实例。得益于可选类型的优点，这两个变量初始值均为nil：

	var john: Person?
	var number73: Apartment?
	
现在，你可以创建特定的Person实例以及Apartment实例，并赋值给john和number73：

	jhon = Person(name: "John Appleseed")
	number73 = Apartments(number: 73)
	
下面的图表明了在创建以及赋值这两个实例后强引用的关系。john拥有一个Person实例的强引用，number73拥有一个Apartment实例的强引用:
![Resize icon](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Art/referenceCycle01_2x.png)


