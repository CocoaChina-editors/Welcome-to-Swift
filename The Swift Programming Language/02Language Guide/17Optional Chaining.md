//暂时先贴出来，待本人校对。

Optional Chaining

可选链接是一种可以请求和调用属性，方法及角标的一种过程，它的可选择性体现于请求或调用的目标当前可能为空（nil）。如果选择的目标有值，那么调用就会成功；相反，如果选择的目标为空（nil），则这种调用将返回空（nil）。多次请求或调用可以被链接在一起形成一个链，如果任何一个节点为空（nil）将导致整个链失效。

笔记：
Swift的选择链和OC中的消息为空有些相像，但是Swift可以使用在任意类型中，并且失败与否可以被检测到。

可选链接可以作为强制拆包的替代

你可以通过在你想调用属性方法或下标的可选值（optional value）（非空）后面放一个问号来定义一个可选链接。这一点很像在可选值后面放一个声明符号来强制拆得它的值。他们的主要的区别在于当可选值为空时可选链接即刻失败，然而强制拆包将会引发运行时错误。

为了反映可选链接可以用空（nil）调用，可选链接调用的结果通常是一个可选值，即使是你调用的属性方法下标等返回的是非可选值。你可以利用这个返回值来检测你的可选链接是否调用成功，有返回值即成功，返回nil则失败。

调用可选链接的返回结果与期待的返回结果具有相同的类型，但是被包装成一个可选值，当可选链接调用成功时，一个应该返回Int的属性将会返回Int？。

下面几段代码将解释可选链接和强制拆包的不同。

首先定义两个类Person和Residence。
class Person {
    var residence: Residence?
}
 
class Residence {
    var numberOfRooms = 1
}

Residence具有一个Int属性叫numberOfRooms其值为1。Person具有一个可选residence属性，它的类型是Residence？。

如果你创建一个新的Person实例，它的residence属性由于是被定义为可选的，此属性将默认初始化为空：

let john = Person();

如果你想使用声明符！强制拆包获得这个人residence属性numberOfRooms属性值，将会引发运行时错误，因为这时没有可以供拆包的residence值。

“let roomCount = john.residence!.numberOfRooms
// this triggers a runtime error”

当john.residence不是nil时会通过，且会将roomCount 设置为一个int类型的合理值。然而如上所述，当residence为空时，这个代码将会导致运行时错误。

可选链接提供了一种另一种获得numberOfRooms的方法。利用可选链接，使用问号来代替原来！的位置：

“if let roomCount = john.residence?.numberOfRooms {
    println("John's residence has \(roomCount) room(s).")
} else {
    println("Unable to retrieve the number of rooms.")
}
// prints "Unable to retrieve the number of rooms.”

这告诉Swift来链接可选residence属性，如果residence存在则取回numberOfRooms的值。

因为这种尝试获得numberOfRooms值得操作有可能失败，可选链接会返回Int？类型值，或者称作“可选Int”。当residence是空的时候（上例），选择Int将会为空，因此可以反映出无法访问numberOfRooms。

要注意的是，即使numberOfRooms是非可选Int（Int？）时这一点也成立。只要是通过可选链接的请求就意味着最后numberOfRooms总是返回一个Int？而不是Int。

你可以自己定义一个Residence实例给john.residence，这样他就不再为空了：

john.residence = Residence();

john.residence 现在有了实际存在的实例而不是空。如果你想使用和前面一样的可选链接来获得numberOfRoooms，它将返回一个包含默认值1的Int？：

if let roomCount = john.residence?.numberOfRooms {
    println("John's residence has \(roomCount) room(s).")
} else {
    println("Unable to retrieve the number of rooms.")
}
// prints "John's residence has 1 room(s).


为可选链接定义模型类

你可以使用可选链接来多层调用属性，方法，和下标。这让你可以利用相互联系的复杂模型来获取亚层的属性，并检查是否可以成功获取此类亚层属性中的各种属性方法下标等。

后面的代码定义了四个将在后面例子中使用的模型类，其中包括多层可选链接。这些类是由上面的Person和Residence模型通过添加一个Room和一个Address类拓展来。

Person类定义与之前相同。

class Person {
    var residence: Residence?
}

Residence类比之前复杂些。这次，它定义了一个变量 rooms，它被初始化为一个Room[]类型的空数组：

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

因为这个版本的Residence存储了一个Room实例的数组，它的numberOfRooms属性不是一个固定的存储值而是通过计算而来的。numberOfRooms属性是由返回rooms数组的count属性值得到的。

为了能快速访问rooms数组，Residence定义了一个只读的下标，插入数组的元素角标值就可以访问到数组中的对应元素。如果该元素存在，subscript则返回数组中的此元素。

Residence中也提供了一个printNumberOfRooms的方法，即简单的打印房间个数。

最后， Residence定义了一个可选属性叫做address（address？）。Address类的属性定义在后面。
用于rooms数组的Room类是一个只有一个name属性和一个设定room名的初始化器的很简单的类。

class Room {
    let name: String
    init(name: String) { self.name = name }
}


这个模型中的最终类叫做Address类。这个类有三个可选属性他们额类型是String？。前面两个可选属性buildingName和 buildingNumber作为地址的一部分，是定义某个特定的建筑的两种不同方式。第三个属性，street，用于命名地址的街道名：

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

Address类还提供了一个buildingIdentifier的方法，它的返回值类型为String？。这个方法检查buildingName和buildingNumber的属性，如果buildingName有值则将其返回，或者如果buildingNumber有值则将其返回，再或返回空如果没有一个属性有值。

通过可选链接调用属性

正如上面“ Optional Chaining as an Alternative to Forced Unwrapping”中所述，你可以利用可选链接的可选值获取属性，并且检查属性是否获取成功。然而你不能使用可选链接设置属性值。

使用上述定义的类来创建一个人实例，并再次尝试后去它的numberOfRooms属性：

let john = Person()
if let roomCount = john.residence?.numberOfRooms {
    println("John's residence has \(roomCount) room(s).")
} else {
    println("Unable to retrieve the number of rooms.")
}
// prints "Unable to retrieve the number of rooms.

由于john.residence是空，所以这个可选链接和之前一样编译失败了，但是没有运行时错误。

通过可选链接调用方法

你可以使用可选链接的来调用可选值的方法并检查方法调用是否成功。即使这个方法没有返回值，你依然可以使用可选链接来达成这一目的。

Residence的printNumberOfRooms方法会打印numberOfRooms的当前值。方法如下：

func printNumberOfRooms(){
	println(“The number of rooms is \(numberOfRooms)”)
}

这个方法没有返回值。但是，没有返回值类型的函数和方法有一个隐式的返回值类型Void（参见Function Without Return Values）。

如果你利用可选链接调用此方法，这个方法的返回值类型将是Void？，而不是Void，因为当通过可选链接调用方法时返回值总是可选类型（optional type）。这可以让你使用if语句来检查是否能成功调用printNumberOfRooms方法，即使是这个方法本是没有定义返回值；如果方法通过可选链接调用成功，printNumberOfRooms的隐式返回值将会是Void，如果没有成功，将返回nil：

if john.residence?.printNumberOfRooms() {
    println("It was possible to print the number of rooms.")
} else {
    println("It was not possible to print the number of rooms.")
}
// prints "It was not possible to print the number of rooms.

使用可选链接调用角标

你可以使用可选链接来尝试从角标获取值并检查角标的调用是否成功，然而你不能通过可选链接来设置角标。

笔记：
当你使用可选链接来获取角标的时候，你应该将问号放在角标括号的前面而不是后面。可选链接的问号一般直接跟着可选表达语句的后面。

下面这个例子试图使用在Residence类中定义的角标来获取john.residence数组中第一个房间的名字。因为john.residence现在是nil，角标的调用失败了。
if let firstRoomName = john.residence?[0].name {
    println("The first room name is \(firstRoomName).")
} else {
    println("Unable to retrieve the first room name.")
}
// prints "Unable to retrieve the first room name.

在角标调用中可选链接的问号直接跟在john.residence的后面，在角标括号的前面，因为john.residence是可选链接试图获得的可选值。

如果你创建一个Residence实例给john.residence，且在他的rooms数组中有一个或多个Room实例，那么你可以使用可选链接通过Residence角标来获取在rooms数组中的实例了：

let johnsHouse = Residence()
johnsHouse.rooms += Room(name: "Living Room")
johnsHouse.rooms += Room(name: "Kitchen")
john.residence = johnsHouse
 
if let firstRoomName = john.residence?[0].name {
    println("The first room name is \(firstRoomName).")
} else {
    println("Unable to retrieve the first room name.")
}
// prints "The first room name is Living Room.

连接多层链接

你可以将多层可选链接连接在一起，可以掘取模型内更下层的属性方法和角标。然而多层可选链接不能再添加比已经返回的可选值更多的层。
也就是说：

如果你试图获得类型不是可选类型，由于可选链接它将变成可选类型。
如果你试图获得的类型已经是可选类型，由于可选链接它也不会提高可选性。

因此：

如果你试图通过可选链接获得Int值，不论使用了多少层链接返回的总是Int？。
相似的，如果你试图通过可选链接获得Int？值，不论使用了多少层链接返回的总是Int？。

下面的例子试图获取john的residence属性里的address的street属性。这里使用了两层可选链接来联系residence和address属性，他们两者都是可选类型：

if let johnsStreet = john.residence?.address?.street {
    println("John's street name is \(johnsStreet).")
} else {
    println("Unable to retrieve the address.")
}
// prints "Unable to retrieve the address.”

john.residence的值现在包含一个Residence实例，然而john.residence.address现在时nil，因此john.residence?.address?.street调用失败。

从上面的例子发现，你试图获得street属性值。这个属性的类型是String？。因此尽管在可选类型属性前使用了两层可选链接，john.residence?.address?.street的返回值类型也是String？。

如果你为Address设定一个实例来作为john.residence.address的值，并为address的street属性设定一个实际值，你可以通过多层可选链接来得到这个属性值。

let johnsAddress = Address()
johnsAddress.buildingName = "The Larches"
johnsAddress.street = "Laurel Street"
john.residence!.address = johnsAddress
 
if let johnsStreet = john.residence?.address?.street {
    println("John's street name is \(johnsStreet).")
} else {
    println("Unable to retrieve the address.")
}
// prints "John's street name is Laurel Street.

值得注意的是，“!”符的在定义address实例时的使用（john.residence.address）。john.residence属性是一个可选类型，因此你需要在它获取address属性之前使用！拆包以获得它的实际值。

链接可选返回值的方法

前面的例子解释了如何通过可选链接来获得可选类型属性值。你也可以通过调用返回可选类型值的方法并按需链接次方法的返回值。

下面的例子通过可选链接调用了Address类中的buildingIdentifier 方法。这个方法的返回值类型是String?。如上所述，这个方法在可选链接调用后最终的返回值类型依然是String？：

if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
    println("John's building identifier is \(buildingIdentifier).")
}
// prints "John's building identifier is The Larches.

如果你还想继续进一步对方法返回值执行可选链接，将可选链接问号符放在方法括号的后面：

if let upper = john.residence?.address?.buildingIdentifier()?.uppercaseString {
    println("John's uppercase building identifier is \(upper).")
}
// prints "John's uppercase building identifier is THE LARCHES.

笔记：

在上面的例子中，你将可选链接问号符放在括号后面是因为你想要链接的可选值是buildingIdentifier方法的返回值，不是buildingIdentifier方法本身。







