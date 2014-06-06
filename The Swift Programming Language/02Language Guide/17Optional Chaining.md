#Optional Chaining

供选链接是一种可以请求和调用属性、方法及角标的一种过程，它的供选择性体现于请求或调用的目标当前可能为空（`nil`）。如果选择的目标有值，那么调用就会成功；相反，如果选择的目标为空（`nil`），则这种调用将返回空（`nil`）。多次请求或调用可以被链接在一起形成一个链，如果任何一个节点为空（`nil`）将导致整个链失效。

> 笔记：
`Swift`的选择链和Objective-C中的消息为空有些相像，但是`Swift`可以使用在任意类型中，并且失败与否可以被检测到。

## 供选链接可以作为强制拆包的替代

你可以通过在你想调用属性，方法或下标的供选值（`optional value`）（非空）后面放一个问号来定义一个供选链接。这一点很像在供选值后面放一个声明符号来强制拆得它的值。他们的主要的区别在于当供选值为空时供选链接即刻失败，然而强制拆包将会引发运行时错误。

为了反映供选链接可以用空（`nil`）调用，供选链接调用的结果通常是一个供选值，即使是你调用的属性方法下标等返回的是非供选值。你可以利用这个返回值来检测你的供选链接是否调用成功，有返回值即成功，返回nil则失败。

调用供选链接的返回结果与期待的返回结果具有相同的类型，但是被包装成一个供选值，当供选链接调用成功时，一个应该返回`Int`的属性将会返回`Int？`。

下面几段代码将解释供选链接和强制拆包的不同。

首先定义两个类`Person`和`Residence`。
	class Person {
    var residence: Residence?
	}
 
	class Residence {
    var numberOfRooms = 1
	}

`Residence`具有一个`Int`属性叫`numberOfRooms`其值为1。`Person`具有一个供选`residence`属性，它的类型是`Residence？`。

如果你创建一个新的Person实例，它的residence属性由于是被定义为供选的，此属性将默认初始化为空：

	let john = Person()

如果你想使用声明符！强制拆包获得这个人`residence`属性`numberOfRooms`属性值，将会引发运行时错误，因为这时没有可以供拆包的`residence`值。

	let roomCount = john.residence!.numberOfRooms
	// this triggers a runtime error”
	//将导致运行时错误
当`john.residence`不是`nil`时会通过运行，且会将`roomCount` 设置为一个`int`类型的合理值。然而如上所述，当`residence`为空时，这个代码将会导致运行时错误。

供选链接提供了一种另一种获得numberOfRooms的方法。利用供选链接，使用问号来代替原来`！`的位置：

	if let roomCount = john.residence?.numberOfRooms {
    	println("John's residence has \(roomCount) room(s).")
	} else {
  	  println("Unable to retrieve the number of rooms.")
	}
	// 打印 "Unable to retrieve the number of rooms."。

这告诉Swift来链接供选`residence`属性，如果`residence`存在则取回`numberOfRooms`的值。

因为这种尝试获得`numberOfRooms`值得操作有可能失败，供选链接会返回`Int？`类型值，或者称作“供选`Int`”。当`residence`是空的时候（上例），选择`Int`将会为空，因此可以反映出无法访问`numberOfRooms`的情况。

要注意的是，即使numberOfRooms是非供选`Int`（`Int？`）时这一点也成立。只要是通过供选链接的请求就意味着最后`numberOfRooms`总是返回一个`Int？`而不是`Int`。

你可以自己定义一个`Residence`实例给`john.residence`，这样他就不再为空了：

	john.residence = Residence()

	`john.residence` 现在有了实际存在的实例而不是nil了。如果你想使用和前面一样的供选链接来获得`numberOfRoooms`，它将返回一个包含默认值1的`Int？`：

	if let roomCount = john.residence?.numberOfRooms {
  	  println("John's residence has \(roomCount) room(s).")
	} else {
	    println("Unable to retrieve the number of rooms.")
	}
	// 打印 "John's residence has 1 room(s)"。


##为供选链接定义模型类

你可以使用供选链接来多层调用属性，方法，和下标。这让你可以利用相互联系的复杂模型来获取亚层的属性，并检查是否可以成功获取此类亚层属性中的各种属性方法下标等。

后面的代码定义了四个将在后面例子中使用的模型类，其中包括多层供选链接。这些类是由上面的`Person`和`Residence`模型通过添加一个`Room`和一个`Address`类拓展来。

`Person`类定义与之前相同。

	class Person {
  	  var residence: Residence?
	}

`Residence`类比之前复杂些。这次，它定义了一个变量 `rooms`，它被初始化为一个`Room[]`类型的空数组：

	class Residence {
	    var rooms = Room[]()
	    var numberOfRooms: Int {
	    return rooms.count
	    }
	    subscript(i: Int) -> Room {
	        return rooms[i]
	    }
	    func printNumberOfRooms() {
	        println("The number of rooms is \(numberOfRooms)")
	    }
	    var address: Address?
	}

因为这个版本的`Residence`存储了一个`Room`实例的数组，它的`numberOfRooms`属性不是一个固定的存储值而是通过计算而来的。`numberOfRooms`属性是由返回`rooms`数组的`count`属性值得到的。

为了能快速访问`rooms`数组，`Residence`定义了一个只读的下标，插入数组的元素角标值就可以访问到数组中的对应元素。如果该元素存在，`subscript`则返回数组中的此元素。

`Residence`中也提供了一个`printNumberOfRooms`的方法，即简单的打印房间个数。

最后，`Residence`定义了一个供选属性叫`address`（`address？`）。`Address`类的属性将在后面定义。
用于`rooms`数组的`Room`类是一个很简单的类，它只有一个`name`属性和一个设定`room`名的初始化器。

	class Room {
	    let name: String
	    init(name: String) { self.name = name }
	}


这个模型中的最终类叫做`Address`类。这个类有三个供选属性他们额类型是`String`？。前面两个供选属性`buildingName`和 `buildingNumber`作为地址的一部分，是定义某个特定的建筑的两种不同方式。第三个属性`street`，用于命名地址的街道名：

	class Address {
	    var buildingName: String?
	    var buildingNumber: String?
	    var street: String?
	    func buildingIdentifier() -> String? {
	        if buildingName {
	            return buildingName
	        } else if buildingNumber {
	            return buildingNumber
	        } else {
	            return nil
	        }
	    }
	}

`Address`类还提供了一个`buildingIdentifier`的方法，它的返回值类型为`String？`。这个方法检查`buildingName`和`buildingNumber`的属性，如果`buildingName`有值则将其返回，或者如果`buildingNumber`有值则将其返回，再或返回空如果没有一个属性有值。

##通过供选链接调用属性

正如上面“ Optional Chaining as an Alternative to Forced Unwrapping”中所述，你可以利用供选链接的供选值获取属性，并且检查属性是否获取成功。然而你不能使用供选链接设置属性值。

使用上述定义的类来创建一个人实例，并再次尝试后去它的`numberOfRooms`属性：

	let john = Person()
	if let roomCount = john.residence?.numberOfRooms {
	    println("John's residence has \(roomCount) room(s).")
	} else {
	    println("Unable to retrieve the number of rooms.")
	}
	// 打印 "Unable to retrieve the number of rooms。

由于`john.residence`是空，所以这个供选链接和之前一样编译失败了，但是没有运行时错误。

##通过供选链接调用方法

你可以使用供选链接的来调用供选值的方法并检查方法调用是否成功。即使这个方法没有返回值，你依然可以使用供选链接来达成这一目的。

	Residence的printNumberOfRooms方法会打印numberOfRooms的当前值。方法如下：
	
	func printNumberOfRooms(){
		println(“The number of rooms is \(numberOfRooms)”)
	}

这个方法没有返回值。但是，没有返回值类型的函数和方法有一个隐式的返回值类型`Void`（参见Function Without Return Values）。

如果你利用供选链接调用此方法，这个方法的返回值类型将是`Void？`，而不是`Void`，因为当通过供选链接调用方法时返回值总是供选类型（optional type）。这可以让你使用`if`语句来检查是否能成功调用`printNumberOfRooms`方法，即使是这个方法本是没有定义返回值；如果方法通过供选链接调用成功，`printNumberOfRooms`的隐式返回值将会是`Void`，如果没有成功，将返回`nil`：

	if john.residence?.printNumberOfRooms() {
	    println("It was possible to print the number of rooms.")
	} else {
	    println("It was not possible to print the number of rooms.")
	}
	// 打印 "It was not possible to print the number of rooms."。

##使用供选链接调用角标

你可以使用供选链接来尝试从角标获取值并检查角标的调用是否成功，然而你不能通过供选链接来设置角标。

> 笔记：
当你使用供选链接来获取角标的时候，你应该将问号放在角标括号的前面而不是后面。供选链接的问号一般直接跟着供选表达语句的后面。

下面这个例子试图使用在`Residence`类中定义的角标来获取`john.residence`数组中第一个房间的名字。因为`john.residence`现在是`nil`，角标的调用失败了。
	if let firstRoomName = john.residence?[0].name {
	    println("The first room name is \(firstRoomName).")
	} else {
	    println("Unable to retrieve the first room name.")
	}
	// 打印 "Unable to retrieve the first room name."。

在角标调用中供选链接的问号直接跟在`john.residence`的后面，在角标括号的前面，因为`john.residence`是供选链接试图获得的供选值。

如果你创建一个`Residence`实例给`john.residence`，且在他的`rooms`数组中有一个或多个`Room`实例，那么你可以使用供选链接通过`Residence`角标来获取在`rooms`数组中的实例了：

	let johnsHouse = Residence()
	johnsHouse.rooms += Room(name: "Living Room")
	johnsHouse.rooms += Room(name: "Kitchen")
	john.residence = johnsHouse
	 
	if let firstRoomName = john.residence?[0].name {
	    println("The first room name is \(firstRoomName).")
	} else {
	    println("Unable to retrieve the first room name.")
	}
	// 打印 "The first room name is Living Room."。

##连接多层链接

你可以将多层供选链接连接在一起，可以掘取模型内更下层的属性方法和角标。然而多层供选链接不能再添加比已经返回的供选值更多的层。
也就是说：

如果你试图获得类型不是供选类型，由于供选链接它将变成供选类型。
如果你试图获得的类型已经是供选类型，由于供选链接它也不会提高供选性。

因此：

如果你试图通过供选链接获得`Int`值，不论使用了多少层链接返回的总是`Int？`。
相似的，如果你试图通过供选链接获得`Int？`值，不论使用了多少层链接返回的总是`Int？`。

下面的例子试图获取`john`的`residence`属性里的`address`的`street`属性。这里使用了两层供选链接来联系`residence`和`address`属性，他们两者都是供选类型：

	if let johnsStreet = john.residence?.address?.street {
	    println("John's street name is \(johnsStreet).")
	} else {
	    println("Unable to retrieve the address.")
	}
	// 打印 "Unable to retrieve the address.”。

`john.residence`的值现在包含一个`Residence`实例，然而`john.residence.address`现在时`nil`，因此`john.residence?.address?.street`调用失败。

从上面的例子发现，你试图获得`street`属性值。这个属性的类型是`String？`。因此尽管在供选类型属性前使用了两层供选链接，`john.residence?.address?.street`的返回值类型也是`String？`。

如果你为`Address`设定一个实例来作为`john.residence.address`的值，并为`address的street`属性设定一个实际值，你可以通过多层供选链接来得到这个属性值。

	let johnsAddress = Address()
	johnsAddress.buildingName = "The Larches"
	johnsAddress.street = "Laurel Street"
	john.residence!.address = johnsAddress
	 
	if let johnsStreet = john.residence?.address?.street {
	    println("John's street name is \(johnsStreet).")
	} else {
	    println("Unable to retrieve the address.")
	}
	// 打印 "John's street name is Laurel Street."。

值得注意的是，“`!`”符的在定义`address`实例时的使用（`john.residence.address`）。`john.residence`属性是一个供选类型，因此你需要在它获取`address`属性之前使用`！`拆包以获得它的实际值。

##链接供选返回值的方法

前面的例子解释了如何通过供选链接来获得供选类型属性值。你也可以通过调用返回供选类型值的方法并按需链接次方法的返回值。

下面的例子通过供选链接调用了`Address`类中的`buildingIdentifier` 方法。这个方法的返回值类型是`String?`。如上所述，这个方法在供选链接调用后最终的返回值类型依然是`String？`：

	if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
	    println("John's building identifier is \(buildingIdentifier).")
	}
	// 打印 "John's building identifier is The Larches."。

如果你还想进一步对方法返回值执行供选链接，将供选链接问号符放在方法括号的后面：

	if let upper = john.residence?.address?.buildingIdentifier()?.uppercaseString {
	    println("John's uppercase building identifier is \(upper).")
	}
	// 打印 "John's uppercase building identifier is THE LARCHES."。

> 笔记：

在上面的例子中，你将供选链接问号符放在括号后面是因为你想要链接的供选值是`buildingIdentifier`方法的返回值，不是`buildingIdentifier`方法本身。







