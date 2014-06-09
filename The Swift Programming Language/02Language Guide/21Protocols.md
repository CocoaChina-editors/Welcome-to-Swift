# 协议 #

协议定义了一个方法的蓝图，属性和其他适合特定任务或功能的要求。协议实际上并不提供一个这些要求的实现，它只是描述了一个实现会是什么样子。协议可以通过一个类，结构或枚举提供这些要求的具体实现。满足要求的任何类型的协议都是符合协议。


协议可以要求符合类型有特定的实例属性，实例方法，类型丰富，操作符和下标。

## 协议的语法 ##
定义协议与定义类，结构，枚举非常相似<br/>

    protocol SomeProtocol {
       // protocol definition goes here
    }
定制类型状态，他们采用一种特定的协议，通过将协议名方在类型名之后，用冒号分割，当作定义的一部分。可以列出多个协议，由逗号分隔：

    struct SomeStructure: FirstProtocol, AnotherProtocol {
       // protocol definition goes here
    }
如果一个类有父类，在任何协议之前列出父类名，后跟一个逗号：

    class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
       // protocol definition goes here
    }

## 属性要求 ##
一个协议可以要求任何符合类型提供一个实例属性或类型属性与一个特定的名称和类型。协议不指定是否该属性应该是一个存储属性或者计算属性--它只指定所需的属性名称和类型。协议还制定是否每个属性必须可获取或可获取和可设置。

如果一个协议需要一个属性是可获取和可设置的，那么一个常量存储属性或一个只读的计算属性是无法满足此属性要求的。如果协议之需要一个属性是可获取的，那么任何类型的属性都能满足此要求，并且如果这对你代码有用，它同样是有效的，也是可设置的。

属性要求总是声明为变量属性，用var关键字做前缀。可获取和可设置属性是通过在他们类型声明后编写*{ get set }*方法，并且可获取属性是通过编写*{ get }*方法。

    protocol SomeProtocol {
       var mustBeSettable: Int { get set }
       var doesNotNeedToBeSettable: Int { get }
    }
当你在协议中定义他们时，总是要在类型属性要求前加*class*关键字作为前缀。即使它被结构或枚举实现时，类型属性要求使用*static*关键词做前缀也是一样。

    protocol FullyNamed {
       var fullName: String { get }
    }
*FullyNamed*协议定义了任何类型的事情都有一个完整限定名称。它不指定它必须是什么样的事情--它只指定此事必须给自己提供一个完整的名称。它指定这一要求，规定任何*FullyNamed*类型必须有一个可获得实例属性*fullName*，而且是*String*类型

这里有一个结构简单的例子,采用符合*FullyNamed*协议的

    struct Person: FullyNamed {
       var fullName: String
    }
    let john = Person(fullName: "John Appleseed")
    // john.fullName is "John Appleseed"

此例定义了一个叫*Person*的结构，其代表一个特定名字的人。它规定
采用*FullyNamed*协议当作其定义部分的第一行。

每个*Person*实例都有一个单独的存储属性*fullName*，且类型为*String*。这符合*FullyNamed*协议的要求，这意味着*Person*协议有正确符合协议。（如果在编译时不满足协议要求，Swift会报出一个错误）

这里有一个更复杂的类,它也采用并符合*FullyNamed*协议:

    class Starship: FullyNamed {
       var prefix: String?
       var name: String
       init(name: String, prefix: String? = nil) {
           self.name = name
           self.prefix = prefix
       }
       var fullName: String {
           return (prefix ? prefix! + " " : "") + name
       }
    }
    var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
    // ncc1701.fullName is "USS Enterprise"

此类实现了*fullName*属性要求作为*Starship*的只读计算属性。每个*Starship*类实例存储一个强制性的*name*和一个可选的*prefix*。如果*fullName*存在就是用*prefix*，并且提前准备以*name*开头去创建*Starship*的全名。

## 方法要求 ##
协议可以要求指定实例方法和类型方法被一致的类型实现。这些方法被写为协议定义的一部分，跟普通实例和类型方法完全一样，但是没有大括号或方法体。可变参数是允许的，普通方法也遵循同样的规则。

> **注意** <br/>
> 协议为普通方法使用相同的语法，但不允许给方法参数指定默认值。 <br/>

同类型属性需求，当他们被定义到协议中时，总是需要在方法需求前加*class*关键字前缀。即使它被结构或枚举实现时，类型属性要求使用*static*关键词做前缀也是一样。

    protocol SomeProtocol {
       class func someTypeMethod()
    }
下面的例子定义了一个单个实例方法要求的协议:

    protocol RandomNumberGenerator {
       func random() -> Double
    }
*RandomNumberGenerator*协议要求任何符合类型都有一个叫做*random*的实例方法，在任何被调用时候都返回一个Double值。（尽管它没有指定其作为协议的一部分，认为此值是在0.0到1.0之间的数字。）

*RandomNumberGenerator*协议不做关于每个随机数是如何生成的假设--它仅要求生成器提供一个标准的方式去生成一个随机数。

这里有一个符合*RandomNumberGenerator*协议的实现类。此类实现了一种伪随机数生成器算法称为线性同余生成器。

	class LinearCongruentialGenerator: RandomNumberGenerator {
	    var lastRandom = 42.0
	    let m = 139968.0
	    let a = 3877.0
	    let c = 29573.0
	    func random() -> Double {
	        lastRandom = ((lastRandom * a + c) % m)
	        return lastRandom / m
	    }
	}
	let generator = LinearCongruentialGenerator()
	println("Here's a random number: \(generator.random())")
	// prints "Here's a random number: 0.37464991998171"
	println("And another one: \(generator.random())")
	// prints "And another one: 0.729023776863283"
	Mutating Method Requirements

## Mutating方法要求 ##
有时需要一个方法来修改它属于(或*mutate*)的实例。对值类型实例方法（即结构和枚举），你将*mutating*关键字放在方法*func*关键字之前表明该方法允许修改所属实例和/或实例的任何属性。这个过程描述在[实例方法内修改值类型](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Methods.html#//apple_ref/doc/uid/TP40014097-CH15-XID_305)。

如果你定义一个协议实例方法要求旨在改变采用此协议的任何类型实例,标记有*mutating*关键字的方法为协议定义的一部分。这使得结构和枚举采用协议并满足此方法要求。


> **注意** <br/>
> 如果你标记一个协议实例方法需求作为*mutating*， 当为一个类写此方法的实现时，你不需要写*mutating*关键字。*mutating*关键字只用于结构和枚举。<br/>

下例定义了一个叫做*Togglable*的协议，它定义了一个简单的实例方法要求叫做*toggle*。正如其名，*toggle*方法旨在切换或转换任何符合类型的状态，通常通过修改类型的属性。

*toggle*方法使用*mutating*关键字标记作为*Togglable*协议定义的一部分，当它被调用时表明该方法有望改变符合实例的状态。

    protocol Togglable {
       mutating func toggle()
    }

当你实现*Togglable*协议结构或枚举，实现的结构或枚举可以通过提供同样标记为*mutating*，名为*toggle*的方法实现来符合协议。

下例定义一个叫做*OnOffSwitch*的枚举。此枚举在两个状态间切换，用枚举cases *On*和*Off*表示.枚举的*toggle*实现也标记为*mutating*，以匹配*Togglable*协议的要求。

    enum OnOffSwitch: Togglable {
	    case Off, On
	    mutating func toggle() {
	        switch self {
	        case Off:
	            self = On
	        case On:
	            self = Off
	        }
	    }
	}
	var lightSwitch = OnOffSwitch.Off
	lightSwitch.toggle()
	// lightSwitch is now equal to .On
	Protocols as Types

## 协议类型 ##
协议本身实际上没有实现任何功能。尽管如此，你创建的任何协议将成为一个成熟的类型在你的代码中使用。

因为它是一个类型，你可以在许多其他类型被允许的地方使用一个协议。包括：<br>

- 作为函数，方法或初始化中的一个参数类型或返回类型。
- 作为常量，变量或属性的类型 
- 作为一个数组项，字典或其他容器的类型
  

> **注意** <br/>
> 因为Swift中的类型名（如*Int*、String和Double）以大写字母开头，所以协议类型的名称也以大写字母开头（如*FullyNamed*和*RandomNumberGenerator*）<br/>

这是协议作为类型的一个例子:

	class Dice {
	    let sides: Int
	    let generator: RandomNumberGenerator
	    init(sides: Int, generator: RandomNumberGenerator) {
	        self.sides = sides
	        self.generator = generator
	    }
	    func roll() -> Int {
	        return Int(generator.random() * Double(sides)) + 1
	    }
	}

此例定义了一个新类*Dice*，代表了一个在棋盘游戏中使用的多面骰子。*Dice*实例有一个叫*sides*的整型属性，其代表了有多少面，一个叫*generator*的属性，提供了在创建骰子滚动值的一个随机数字生成器。

*generator*属性是*RandomNumberGenerator*属性。然后，可以设置为采用*RandomNumberGenerator*协议的任何类型的一个实例。除了必须采用*RandomNumberGenerator*协议外，不需要指定别的该实例的属性。

*Dice*也有初始化去设置它的初识状态。这个初始化参数成为*generator*，其类型也是*RandomNumberGenerator*。当初始化一个新的*Dice*实例，你可以传递一个任何符合输入该参数的值。

*Dice*提供了一个叫做*roll*的实例方法，其返回一个整数值，范围在1和*Dice*的属性*sides*之间。此方法调用生成器的*random*方法创建一个在0.0到1.0之间的随机数字，并且使用这个随机数去创建一个在正确范围内的骰子滚动值。因为*generator*是引用*RandomNumberGenerator*，其保证有一个*random*方法调用。

这里有一个*Dice*类可以用于创建六面骰子且使用*LinearCongruentialGenerator*实例作为随机数生成器：

	var d6 = Dice(sides: 6, generator: LinearCongruentialGenerator())
	for _ in 1...5 {
	    println("Random dice roll is \(d6.roll())")
	}
	// Random dice roll is 3
	// Random dice roll is 5
	// Random dice roll is 4
	// Random dice roll is 5
	// Random dice roll is 4


## 委托 ##
委托是一种设计模式，它使得一个类或结构可以自身的一些职责交给（或委托）另外类型的实例。该设计模式通过定义一个协议封装委托职责实现的，这样符合类型（称为委托）保证了提供已经授权的功能。委托可以用于相应一个特定的动作，或从外部源检索数据，而不需要了解来源的底层类型。

下面定义了两个协议用于基于骰子的棋盘游戏：

	protocol DiceGame {
	    var dice: Dice { get }
	    func play()
	}
	protocol DiceGameDelegate {
	    func gameDidStart(game: DiceGame)
	    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
	    func gameDidEnd(game: DiceGame)
	}

*DiceGame*协议是一个可以被任何涉及骰子游戏采用的协议。*DiceGameDelegate*协议可以被任何跟踪*DiceGame*进度的类型采用。

这里有一个在最初介绍[控制流](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-XID_153)时的*Snakes*和*Ladders*游戏的版本。这个版本适合使用*Dice*实例是因为滚动骰子，采用*DiceGame*协议，并对*DiceGameDelegate*通知其进展。

	class SnakesAndLadders: DiceGame {
	    let finalSquare = 25
	    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
	    var square = 0
	    var board: Int[]
	    init() {
	        board = Int[](count: finalSquare + 1, repeatedValue: 0)
	        board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
	        board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
	    }
	    var delegate: DiceGameDelegate?
	    func play() {
	        square = 0
	        delegate?.gameDidStart(self)
	        gameLoop: while square != finalSquare {
	            let diceRoll = dice.roll()
	            delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
	            switch square + diceRoll {
	            case finalSquare:
	                break gameLoop
	            case let newSquare where newSquare > finalSquare:
	                continue gameLoop
	            default:
	                square += diceRoll
	                square += board[square]
	            }
	        }
	        delegate?.gameDidEnd(self)
	    }
	}


*Snakes*和*Ladders*游戏的描述见[控制流](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-XID_153)章节的[跳出](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-XID_174)部分。

这个游戏版本被封装为一个叫做*SnakesAndLadders*的类，其采用*DiceGame*协议。它提供了一个获取*dice*属性和一个符合协议的*play*方法。（*Dice*属性声明为一个常量属性，因为它不需要在初始化后改变，并且协议只需要是能得到的。）

*Snakes*和*Ladders*游戏板设置在类的*init()*方法内进行初始化。所有游戏逻辑进入到协议的*play*方法，它使用协议必须的*dice*属性提供骰子滚动值。

注意，*delegate*属性被定义为的一个可选*DiceGameDelegate*，因为玩游戏委托不是必须的。因为它是一个可选的类型，*delegate*属性自动设置一个初始值*nil*。此后，游戏实例可以选择设置属性为一个合适的委托。

*DiceGameDelegate*提供三个方法来跟踪一个游戏的进展。这三种方法已经被纳入上面*play*方法的游戏逻辑中，并且在游戏开始时被调用，至到一个新游戏开始或游戏结束。

因为*delegate*属性是一个可选的*DiceGameDelegate*，*play*方法是用选项链每当在委托中调用一个方法。如果*delegate*属性是*nil*，这些委托优雅的调用失败并不报错。如果*delegate*属性是*non-nil*，委托方法被调用并且通过*SnakesAndLadders*实例的参数传递。

下一个示例展示了一个称为*DiceGameTracker*的类，其采用了*DiceGameDelegate*协议。

	class DiceGameTracker: DiceGameDelegate {
	    var numberOfTurns = 0
	    func gameDidStart(game: DiceGame) {
	        numberOfTurns = 0
	        if game is SnakesAndLadders {
	            println("Started a new game of Snakes and Ladders")
	        }
	        println("The game is using a \(game.dice.sides)-sided dice")
	    }
	    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
	        ++numberOfTurns
	        println("Rolled a \(diceRoll)")
	    }
	    func gameDidEnd(game: DiceGame) {
	        println("The game lasted for \(numberOfTurns) turns")
	    }
	}
*DiceGameTracker*实现了*DiceGameDelegate*所要求的所有三个方法。它使用这些方法来跟踪游戏所走步骤的数字。当游戏开始时它重置*numberOfTurns*属性为0；每一个新步骤开始就增加；当游戏结束打印出游戏的总圈数。

如上所示的*gameDidStart*实现使用游戏参数打印一些关于*game*即将扮演的介绍性信息。*game*参数有一个*DiceGame*类型，不是*SnakesAndLadders*，所以*gameDidStart*只能访问和使用实现了*DiceGame*协议一部分的方法和属性。然后，该方法仍然能够使用类型转换去查询底层实例的类型。在此例中，它检查*game*幕后实例是否是*SnakesAndLadders*，如果是就打印一个适当的消息。

*gameDidStart*同样可以访问*dice*属性和传入的*game*参数。因为*game*是符合*DiceGame*协议的，它确保拥有一个*dice*属性，所以*gameDidStart*方法能够访问并打印骰子的*sides*属性，不管什么样的game正在进行。


下面是*DiceGameTracker*执行步骤：

	let tracker = DiceGameTracker()
	let game = SnakesAndLadders()
	game.delegate = tracker
	game.play()
	// Started a new game of Snakes and Ladders
	// The game is using a 6-sided dice
	// Rolled a 3
	// Rolled a 5
	// Rolled a 4
	// Rolled a 5
	// The game lasted for 4 turns

## 使用扩展补充协议一致性 ##

你可以扩展现有类型去采用和符合新协议，即时你没有访问现有类型的源代码。扩展可以添加新属性，方法和下标到现有类型，因此能够添加任何协议可能需要的要求。更多关于扩展，见[扩展](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html#//apple_ref/doc/uid/TP40014097-CH24-XID_191)。

> **注意** <br/>
> 当这种一致性被添加到一个扩展的实例类型时，现有类型的实例自动采用并符合协议<br/>

例如，这个称为*TextRepresentable*的协议，可以实现任何类型，一种是用于文本表示。这可能是本身的描述，或是一个文本当前状态的版本。


	protocol TextRepresentable {
	    func asText() -> String
	}

*Dice*从前面可以被扩展去采用并符合*TextRepresentable*。

	extension Dice: TextRepresentable {
	    func asText() -> String {
	        return "A \(sides)-sided dice"
	    }
	}

这个扩展采用新的协议以完全相同的方式好像*Dice*提供了最初的实现。协议名在类型名后，用冒号分割，所有协议需求的实现在花括号内提供。

现在任何*Dice*实例都可以被视为*TextRepresentable*:

	let d12 = Dice(sides: 12, generator: LinearCongruentialGenerator())
	println(d12.asText())
	// prints "A 12-sided dice"

同样，*SnakesAndLadders*游戏类可以被扩展去采用并符合*TextRepresentable*协议：

	extension SnakesAndLadders: TextRepresentable {
	    func asText() -> String {
	        return "A game of Snakes and Ladders with \(finalSquare) squares"
	    }
	}
	println(game.asText())
	// prints "A game of Snakes and Ladders with 25 squares"


###采用扩展声明协议###

如果一个类型已经符合协议的所有要求，但尚未指定其采用的协议，你就可以使用一个空扩展使它采用协议。

	struct Hamster {
	    var name: String
	    func asText() -> String {
	        return "A hamster named \(name)"
	    }
	}
	extension Hamster: TextRepresentable {}

*Hamster*实例现在可以被用到任何需要*TextRepresentable*类型的地方：

	let simonTheHamster = Hamster(name: "Simon")
	let somethingTextRepresentable: TextRepresentable = simonTheHamster
	println(somethingTextRepresentable.asText())
	// prints "A hamster named Simon"

> **注意** <br/>
> 类型不自动采用协议只是通过满足其要求。他们必须显示的声明他们要采用的协议。<br/>


## 协议类型的集合 ##

协议可以被用于类型并存储在集合中，比如数组或字典，就像在
[协议类型](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-XID_352)提到的。下例创建一个*TextRepresentable*数组：

	let things: TextRepresentable[] = [game, d12, simonTheHamster]

现在可以迭代数组中的项，并打印每个条目的文本表示：

	for thing in things {
	    println(thing.asText())
	}
	// A game of Snakes and Ladders with 25 squares
	// A 12-sided dice
	// A hamster named Simon

注意*TextRepresentable*类型的*thing*常量。它不是*Dice*类型或*DiceGame*或*Hamster*，即时实际实例幕后是其中的一个类型。尽管如此，因为它是*TextRepresentable*类型，众所周知，*TextRepresentable*有一个*asText*方法，循环中每次调用*thing.asText*是安全的。

