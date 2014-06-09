# 枚举 (Enumerations)
**枚举**为一系相关联的值定义了一个公共的组类型.同时能够让你在编程的时候在类型安全的情况下去使用这些值。
如果你对C语言很熟悉，你肯定知道在C语言中**枚举**类型就是一系列具有被指定有关联名称的的整数值.但在Swift中**枚举**类型就更加灵活了，并且你不必给**枚举**类型中的每个成员都赋值。如果把一个值(假设值为"raw")提供给所有的**枚举**类型当中的成员，那么这个值可以是一个字符串，一个字符，一个整数或者说是一个浮点数.
作为选择，**枚举**中的成员可以被特别指定为任何不同于其他成员的可以存储的类型，就像是其他语言当中的联合或者变体。你能够将一些相关联的成员定义成一个公共的集合作为**枚举**的一部分，每个部分都有不同的集合类型并且使用了恰当的类型。
	
###枚举语法
你可以用enum开始并且用大括号包含整个定义体来定义一个枚举:

```
enum SomeEnumeration {
	// 在这里定义枚举
}
```	

这里有一个例子，定义了一个包含四个方向的罗盘:

```
enum CompassPoint {
	case North
	case South
	case East
	case West
}
```	
**枚举**中定义的变量(像上例中`North`, `South`, `East`, `West`)是**枚举**的成员变量(或者说成员).关键字case是用来标明这一行将要定义一个新的成员变量
	
>注意:
>
>与C或者Objective-C不同的是，在Swift语言中**枚举**类型的成员初始的时候不会被默认赋值成整数值，在`CompassPoint`这个例子中，`North`, `South`, `East`, `West`默认不会隐式的等于0,1,2,3。取而代之的是不同的**枚举**成员将要用什么类型以及赋值什么值都是可以自己控制的，可以在定义CompassPoint这个**枚举**的时候指定.
	
多个成员还可以用一行来定义，他们之间用逗号分割:

```
enum Plant{
	case Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
}
```

每个枚举的定义都是定义了一个全新的类型,就像Swfit中的其他的类型一样，枚举的名称(像上边的`CompassPoint`, `Plant`)应该是以一个大写字母开头，让他们是单数类型而不是复数类型，从而让他们可以不言而喻:

```
var directionToHead = CompassPoint.West
```
	
当`directionToHead`在初始化过程中被赋值成`CompassPoint`中的某一个可能的值的时候，它的类型就可以被推测出来来了。一旦`directionToHead`被声明成是`CompassPoint`类型，那么你就可以简短的使用逗号表达式来给它赋值成其他的`CompassPoint`当中的值了:

```
directionToHead = .East
```
	
`directionToHead`的类型是已知的了，所以你可以忽略它的类型来给他赋值了。这样使得在使用显示类型的枚举值时代码具有很高的可读性。
	
###使用Switch语句来匹配枚举值
你可以通过switch语句来访问单独的某个枚举值:

```
directionToHead = .South
switch directionToHead {
	case .North:
	    println("Lots of planets have a north")
	case .South:
    	println("Watch out for penguins")
	case .East:
	    println("Where the sun rises")
	case .West:
	    println("Where the skies are blue")
}
// 输出"Watch out for penguins”
```
	
你可以这样阅读这段代码:

考虑`directionToHead`的值，如果它等于`.North`那么就输出"Lost of planets have a north",如果它等于`.South`,那么就输出"Watch out for penguins"。
	
...等等

就和在控制流程那一章所讲，一个switch语句被用到判断枚举值的时候，必须要包括所有的枚举成员。假设`.West`被忽略了，将会导致编译出错，因为它没有考虑到枚举的所有的枚举成员，我们需要全面性的确保枚举的所有成员不被忽略掉.

如果给考虑每个枚举的成员不合适，你可以提供一个default来覆盖其他没有明确处理的成员:

```
let somePlanet = Planet.Earth
switch somePlanet {
	case .Earth:
    	println("Mostly harmless")
	default:
   		println("Not a safe place for humans")
}
// 输出 "Mostly harmless"
```

###关联值

在上一节的示例中显示了一个枚举的成员是如何在自己的权利界定（和类型）的值。你可以设置一个常量或变量的值为`Planet.Earth` ，然后检查这个值。然而，如果在保留成员值的同时能够存储其它类型的关联值将会变得更有意义。这使您能够在保存成员值的同时存储额外的自定义信息，并且允许每次你在代码中使用这些成员值的时候改变这些关联值。在Swift中当你定义一个枚举成员的时候，你可以给他关联任何的类型，而且如果需要的话每个成员可以有不同的关联类型。枚举类型的这个特性和其他语言当中的辨别联合，标记联合或者变体很像。


举个例子，设想一个库存跟踪系统想要通过两种不同的条形码来跟踪产品。一些产品用UPC-A格式的一维条形码标识的，使用0到9的数字。每个条形码当中有一个标识“数字系统“的数字，然后是10个“标识符"数字，最后边一个用来做“检查”的数字，以确保这个条形码被正确的扫描识别:
		
		照片
		
另一些产品是用QR编码格式的二维码标识的，这种条形码可以使用任何ISO 8859-1的字符而且最大可以编码一个2953字符长度的字符串:
	
		照片
		
如果能够用一个有三个整型的元组来存储UPC-A格式的条形码，然后用一个可以存储任意长度的字符串来存储QR格式的条形码，那么对于一个库存跟踪系统来说，将会是再便捷不过的了。

在Swift语言中，一个可以定义两个格式的产品条形码的枚举看起来是这样的:

```
enum Barcode {
   	case UPCA(Int, Int, Int)
    case QRCode(String)
}
```
你可以这样阅读这段代码:

定义了一个叫做Barcode的枚举类型，它可以有一个UPCA成员，这个成员关联了一个包含三个整型数值的元组，同时这个枚举类型还有一个QRCode成员，关联了一个字符串。

这个定义不会生成任何的整型或者字符串值，他只是定义了当一个不可变变量或者变量等于Barcode.UPCA或者`Barcode.QRCode`的时候它被关联的值的类型

这样一来可以用任意其中一个类型来生成一个新的条形码了:

```
var productBarcode = Barcode.UPCA(8, 85909_51226, 3)
```
这个例子生成了一个新的变量叫做`productBarcode`,这个变量被关联了一个`Barcode.UPCA`类型的元组，这个元组的值为`(8, 8590951226, 3)`,被提供做“标识”的值用下划线分割了-85909_51226,这样做是为了更好的被以条形码读出来.

同样的产品还可以被赋值为另一个条形码类型:

```
productBarcode = .QRCode("ABCDEFGHIJKLMNOP")
```
在这一点上，原来的`Barcode.UPCA`以及关联到的整型值被一个新类型`Barcode.QRCode`以及与其关联的字符串给替换了。`Barcode`类型的不可变变量以及可变变量可以存储`.UPCA`或者`.QRCode`类型(同时还有与其相关联的值)，但是每次都只能存储这两个类型当中的一个。

同样，这两个不同的类型可以用Switch语句来做检查。同时，在switch语句中他们相关联的值也可以被获取到。你可以把关联的值当做不可变变量(用`let`来开头)，或者可变变量(用`var`开头)以在switch的控制体当中使用。

```
switch productBarcode {
	case .UPCA(let numberSystem, let identifier, let check):
    	println("UPC-A with value of \(numberSystem), \(identifier), \(check).")
	case .QRCode(let productCode):
	    println("QR code with value of \(productCode).")
}
```

如果一个枚举成员关联的所有值都被当做不可变变量或者可变变量来使用，那么你可以在成员名称之前只放一个`let`或者`var`来达到目的，简要示例:

```
switch productBarcode {
case let .UPCA(numberSystem, identifier, check):
   	println("UPC-A with value of \(numberSystem), \(identifier), \(check).")
case let .QRCode(productCode):
    println("QR code with value of \(productCode).")
}
// 输出 "QR code with value of ABCDEFGHIJKLMNOP.”
```
###原始值
在上一节当中条形码的例子展示了一个枚举类型的成员怎么声明他们可以存储不同类型的关联值。不同于关联值，枚举类型的成员还可以预设置默认值(我们叫他原始值)，这些值的类型是相同的。

这里有一个枚举成员存储一个ASCII值的例子：

```
enum ASCIIControlCharacter: Character {
   	case Tab = "\t"
	case LineFeed = "\n"
	case CarriageReturn = "\r"
}
```
这里定义了一个原始值为字符类型的枚举类型`ASCIIControlCharacter`，而且包含了一些我们常用的控制字符。关于字符类型，你可以在字符串和字符那个章节找到更多的描述。

请注意，原始值与关联值不同。原始值应该是在你定义枚举的代码中被设置为预填充值的，就像上述三个ASCII码。对于一个特定的枚举成员的原始值始终是相同的。关联值是当你创建一个基于枚举的成员的新的常量或变量的时候设置的，并且每次都可以是不同的。

原始值可以是字符串，字符或者其他任何的整型或者浮点型等数字类型。每个原始值在他属的枚举类型定义的时候都应该是不同的。如果原始值是整数类型，那么当其他枚举成员没有设置原始值的时候，他们的原始值是这个整型原始值自增长设置的。

下边这个枚举类型是对之前的`Plant`枚举类型的改良，新的枚举类型有一个整型的原始值来标识他们在距离太阳的顺序:

```
enum Planet: Int {
   	case Mercury = 1, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
}	
```
	
自增长的意思就是`Planet.Venus`的原始值会被设置成2，以此类推。
	
可以通过枚举成员的toRaw()方法来获取他的原始值:

```
let earthsOrder = Planet.Earth.toRaw()
//eathsOrder is 3
```
	
使用枚举成员的fromRaw()方法来尝试通过一个原始值来寻找他所对应的枚举成员。下面这个例子介绍了怎么通过Uranus的原始值7找到Uranus的：

```
let possiblePlanet = Planet.fromRaw(7)
// possiblePlanet is of type Planet? and equals Planet.Uranus”
```

然而不是所有的整型值都可以找到一个对应的`Planet`，正是如此，`fromRaw()`将会返回一个非强制类型的枚举成员。上边展示的例子当中`possiblePlanet`是一个`Planet?`类型，或者是一个非强制`Planet`类型。

如果你尝试通过原始值9来寻找他对应的`Planet`，那么`fromRaw()`返回给你的非强制`Planet`类型将会是`nil`:

```
let positionToFind = 9
if let somePlanet = Planet.fromRaw(positionToFind) {
   	switch somePlanet {
	    case .Earth:
       		println("Mostly harmless")
	    default:
       		println("Not a safe place for humans")
    }
} else {
   	println("There isn't a planet at position \(positionToFind)")
}
// 输出 "There isn't a planet at position 9”
```

这个例子尝试使用非强制类型绑定来试着用原始值9来获取一个`Planet`。表达式`if let somePlanet = Planet.fromRaw(9)`用来检索一个`Planet`，如果能够检索到`Planet`将会将这个非强制`Planet`赋值给`somePlanet`。在这个示例中，通过原始值9不可能检索到对应的`Planet`，所以`else`包含的语句将会被执行。
	