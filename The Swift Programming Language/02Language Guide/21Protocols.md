# 协议 #

协议定义了一个方法的蓝图，属性和其他适合特定任务或功能的要求。协议实际上并不提供一个这些要求的实现，它只是描述了一个实现会是什么样子。协议可以通过一个类，结构或枚举提供这些要求的具体实现。满足要求的任何类型的协议都是符合协议。


协议可以要求符合类型有特定的实例属性，实例方法，类型丰富，操作符和下标。

## 协议的语法 ##
定义协议与定义类，结构，枚举非常相似<br/>
```js

    protocol SomeProtocol {
       // protocol definition goes here
    }
```
定制类型状态，他们采用一种特定的协议，通过将协议名方在类型名之后，用冒号分割，当作定义的一部分。可以列出多个协议，由逗号分隔：
```js

    struct SomeStructure: FirstProtocol, AnotherProtocol {
       // protocol definition goes here
    }
```
如果一个类有父类，在任何协议之前列出父类名，后跟一个逗号：
```js

    class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
       // protocol definition goes here
    }
```

## 属性要求 ##
一个协议可以要求任何符合类型提供一个实例属性或类型属性与一个特定的名称和类型。协议不指定是否该属性应该是一个存储属性或者计算属性--它只指定所需的属性名称和类型。协议还制定是否每个属性必须可获取或可获取和可设置。

如果一个协议需要一个属性是可获取和可设置的，那么一个常量存储属性或一个只读的计算属性是无法满足此属性要求的。如果协议之需要一个属性是可获取的，那么任何类型的属性都能满足此要求，并且如果这对你代码有用，它同样是有效的，也是可设置的。

属性要求总是声明为变量属性，用var关键字做前缀。可获取和可设置属性是通过在他们类型声明后编写*{ get set }*方法，并且可获取属性是通过编写*{ get }*方法。
```js

    protocol SomeProtocol {
       var mustBeSettable: Int { get set }
       var doesNotNeedToBeSettable: Int { get }
    }
```
当你在协议中定义他们时，总是要在类型属性要求前加*class*关键字作为前缀。即使它被结构或枚举实现时，类型属性要求使用*static*关键词做前缀也是一样。
```js

    protocol FullyNamed {
       var fullName: String { get }
    }
```
*FullyNamed*协议定义了任何类型的事情都有一个完整限定名称。它不指定它必须是什么样的事情--它只指定此事必须给自己提供一个完整的名称。它指定这一要求，规定任何*FullyNamed*类型必须有一个可获得实例属性*fullName*，而且是*String*类型

这里有一个结构简单的例子,采用符合*FullyNamed*协议的
```js

    struct Person: FullyNamed {
       var fullName: String
    }
    let john = Person(fullName: "John Appleseed")
    // john.fullName is "John Appleseed"
```

此例定义了一个叫*Person*的结构，其代表一个特定名字的人。它规定
采用*FullyNamed*协议当作其定义部分的第一行。

每个*Person*实例都有一个单独的存储属性*fullName*，且类型为*String*。这符合*FullyNamed*协议的要求，这意味着*Person*协议有正确符合协议。（如果在编译时不满足协议要求，Swift会报出一个错误）

这里有一个更复杂的类,它也采用并符合*FullyNamed*协议:
```js

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
```

此类实现了*fullName*属性要求作为*Starship*的只读计算属性。每个*Starship*类实例存储一个强制性的*name*和一个可选的*prefix*。如果*fullName*存在就是用*prefix*，并且提前准备以*name*开头去创建*Starship*的全名。

## 方法要求 ##
协议可以要求指定实例方法和类型方法被一致的类型实现。这些方法被写为协议定义的一部分，跟普通实例和类型方法完全一样，但是没有大括号或方法体。可变参数是允许的，普通方法也遵循同样的规则。

> **注意** <br/>
> 协议为普通方法使用相同的语法，但不允许给方法参数指定默认值。 <br/>

同类型属性需求，当他们被定义到协议中时，总是需要在方法需求前加*class*关键字前缀。即使它被结构或枚举实现时，类型属性要求使用*static*关键词做前缀也是一样。
```js

    protocol SomeProtocol {
       class func someTypeMethod()
    }
```
下面的例子定义了一个单个实例方法要求的协议:
```js

    protocol RandomNumberGenerator {
       func random() -> Double
    }
```
*RandomNumberGenerator*协议要求任何符合类型都有一个叫做*random*的实例方法，在任何被调用时候都返回一个Double值。（尽管它没有指定其作为协议的一部分，认为此值是在0.0到1.0之间的数字。）

*RandomNumberGenerator*协议不做关于每个随机数是如何生成的假设--它仅要求生成器提供一个标准的方式去生成一个随机数。

这里有一个符合*RandomNumberGenerator*协议的实现类。此类实现了一种伪随机数生成器算法称为线性同余生成器。
```js

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
```

## Mutating方法要求 ##
有时需要一个方法来修改它属于(或*mutate*)的实例。对值类型实例方法（即结构和枚举），你将*mutating*关键字放在方法*func*关键字之前表明该方法允许修改所属实例和/或实例的任何属性。这个过程描述在[实例方法内修改值类型](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Methods.html#//apple_ref/doc/uid/TP40014097-CH15-XID_305)。

如果你定义一个协议实例方法要求旨在改变采用此协议的任何类型实例,标记有*mutating*关键字的方法为协议定义的一部分。这使得结构和枚举采用协议并满足此方法要求。


> **注意** <br/>
> 如果你标记一个协议实例方法需求作为*mutating*， 当为一个类写此方法的实现时，你不需要写*mutating*关键字。*mutating*关键字只用于结构和枚举。<br/>

下例定义了一个叫做*Togglable*的协议，它定义了一个简单的实例方法要求叫做*toggle*。正如其名，*toggle*方法旨在切换或转换任何符合类型的状态，通常通过修改类型的属性。

*toggle*方法使用*mutating*关键字标记作为*Togglable*协议定义的一部分，当它被调用时表明该方法有望改变符合实例的状态。
```js

    protocol Togglable {
       mutating func toggle()
    }
```
当你实现*Togglable*协议结构或枚举，实现的结构或枚举可以通过提供同样标记为*mutating*，名为*toggle*的方法实现来符合协议。

下例定义一个叫做*OnOffSwitch*的枚举。此枚举在两个状态间切换，用枚举cases *On*和*Off*表示.枚举的*toggle*实现也标记为*mutating*，以匹配*Togglable*协议的要求。
```js

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
```

## 协议类型 ##
协议本身实际上没有实现任何功能。尽管如此，你创建的任何协议将成为一个成熟的类型在你的代码中使用。

因为它是一个类型，你可以在许多其他类型被允许的地方使用一个协议。包括：<br>

- 作为函数，方法或初始化中的一个参数类型或返回类型。
- 作为常量，变量或属性的类型 
- 作为一个数组项，字典或其他容器的类型
  

> **注意** <br/>
> 因为Swift中的类型名（如*Int*、String和Double）以大写字母开头，所以协议类型的名称也以大写字母开头（如*FullyNamed*和*RandomNumberGenerator*）<br/>

这是协议作为类型的一个例子:
```js

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
```

此例定义了一个新类*Dice*，代表了一个在棋盘游戏中使用的多面骰子。*Dice*实例有一个叫*sides*的整型属性，其代表了有多少面，一个叫*generator*的属性，提供了在创建骰子滚动值的一个随机数字生成器。

*generator*属性是*RandomNumberGenerator*属性。然后，可以设置为采用*RandomNumberGenerator*协议的任何类型的一个实例。除了必须采用*RandomNumberGenerator*协议外，不需要指定别的该实例的属性。

*Dice*也有初始化去设置它的初识状态。这个初始化参数成为*generator*，其类型也是*RandomNumberGenerator*。当初始化一个新的*Dice*实例，你可以传递一个任何符合输入该参数的值。

*Dice*提供了一个叫做*roll*的实例方法，其返回一个整数值，范围在1和*Dice*的属性*sides*之间。此方法调用生成器的*random*方法创建一个在0.0到1.0之间的随机数字，并且使用这个随机数去创建一个在正确范围内的骰子滚动值。因为*generator*是引用*RandomNumberGenerator*，其保证有一个*random*方法调用。

这里有一个*Dice*类可以用于创建六面骰子且使用*LinearCongruentialGenerator*实例作为随机数生成器：
```js

	var d6 = Dice(sides: 6, generator: LinearCongruentialGenerator())
	for _ in 1...5 {
	    println("Random dice roll is \(d6.roll())")
	}
	// Random dice roll is 3
	// Random dice roll is 5
	// Random dice roll is 4
	// Random dice roll is 5
	// Random dice roll is 4
```


## 委托 ##
委托是一种设计模式，它使得一个类或结构可以自身的一些职责交给（或委托）另外类型的实例。该设计模式通过定义一个协议封装委托职责实现的，这样符合类型（称为委托）保证了提供已经授权的功能。委托可以用于相应一个特定的动作，或从外部源检索数据，而不需要了解来源的底层类型。

下面定义了两个协议用于基于骰子的棋盘游戏：
```js

	protocol DiceGame {
	    var dice: Dice { get }
	    func play()
	}
	protocol DiceGameDelegate {
	    func gameDidStart(game: DiceGame)
	    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
	    func gameDidEnd(game: DiceGame)
	}
```

*DiceGame*协议是一个可以被任何涉及骰子游戏采用的协议。*DiceGameDelegate*协议可以被任何跟踪*DiceGame*进度的类型采用。

这里有一个在最初介绍[控制流](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-XID_153)时的*Snakes*和*Ladders*游戏的版本。这个版本适合使用*Dice*实例是因为滚动骰子，采用*DiceGame*协议，并对*DiceGameDelegate*通知其进展。
```js

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
```

*Snakes*和*Ladders*游戏的描述见[控制流](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-XID_153)章节的[跳出](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-XID_174)部分。

这个游戏版本被封装为一个叫做*SnakesAndLadders*的类，其采用*DiceGame*协议。它提供了一个获取*dice*属性和一个符合协议的*play*方法。（*Dice*属性声明为一个常量属性，因为它不需要在初始化后改变，并且协议只需要是能得到的。）

*Snakes*和*Ladders*游戏板设置在类的*init()*方法内进行初始化。所有游戏逻辑进入到协议的*play*方法，它使用协议必须的*dice*属性提供骰子滚动值。

注意，*delegate*属性被定义为的一个可选*DiceGameDelegate*，因为玩游戏委托不是必须的。因为它是一个可选的类型，*delegate*属性自动设置一个初始值*nil*。此后，游戏实例可以选择设置属性为一个合适的委托。

*DiceGameDelegate*提供三个方法来跟踪一个游戏的进展。这三种方法已经被纳入上面*play*方法的游戏逻辑中，并且在游戏开始时被调用，至到一个新游戏开始或游戏结束。

因为*delegate*属性是一个可选的*DiceGameDelegate*，*play*方法是用Optional链每当在委托中调用一个方法。如果*delegate*属性是*nil*，这些委托优雅的调用失败并不报错。如果*delegate*属性是*non-nil*，委托方法被调用并且通过*SnakesAndLadders*实例的参数传递。

下一个示例展示了一个称为*DiceGameTracker*的类，其采用了*DiceGameDelegate*协议。
```js

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
```
*DiceGameTracker*实现了*DiceGameDelegate*所要求的所有三个方法。它使用这些方法来跟踪游戏所走步骤的数字。当游戏开始时它重置*numberOfTurns*属性为0；每一个新步骤开始就增加；当游戏结束打印出游戏的总圈数。

如上所示的*gameDidStart*实现使用游戏参数打印一些关于*game*即将扮演的介绍性信息。*game*参数有一个*DiceGame*类型，不是*SnakesAndLadders*，所以*gameDidStart*只能访问和使用实现了*DiceGame*协议一部分的方法和属性。然后，该方法仍然能够使用类型转换去查询底层实例的类型。在此例中，它检查*game*幕后实例是否是*SnakesAndLadders*，如果是就打印一个适当的消息。

*gameDidStart*同样可以访问*dice*属性和传入的*game*参数。因为*game*是符合*DiceGame*协议的，它确保拥有一个*dice*属性，所以*gameDidStart*方法能够访问并打印骰子的*sides*属性，不管什么样的game正在进行。


下面是*DiceGameTracker*执行步骤：
```js

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
```

## 使用扩展补充协议一致性 ##

你可以扩展现有类型去采用和符合新协议，即时你没有访问现有类型的源代码。扩展可以添加新属性，方法和下标到现有类型，因此能够添加任何协议可能需要的要求。更多关于扩展，见[扩展](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html#//apple_ref/doc/uid/TP40014097-CH24-XID_191)。

> **注意** <br/>
> 当这种一致性被添加到一个扩展的实例类型时，现有类型的实例自动采用并符合协议<br/>

例如，这个称为*TextRepresentable*的协议，可以实现任何类型，一种是用于文本表示。这可能是本身的描述，或是一个文本当前状态的版本。
```js

	protocol TextRepresentable {
	    func asText() -> String
	}

```
*Dice*从前面可以被扩展去采用并符合*TextRepresentable*。
```js

	extension Dice: TextRepresentable {
	    func asText() -> String {
	        return "A \(sides)-sided dice"
	    }
	}
```

这个扩展采用新的协议以完全相同的方式好像*Dice*提供了最初的实现。协议名在类型名后，用冒号分割，所有协议需求的实现在花括号内提供。

现在任何*Dice*实例都可以被视为*TextRepresentable*:
```js

	let d12 = Dice(sides: 12, generator: LinearCongruentialGenerator())
	println(d12.asText())
	// prints "A 12-sided dice"
```

同样，*SnakesAndLadders*游戏类可以被扩展去采用并符合*TextRepresentable*协议：
```js

	extension SnakesAndLadders: TextRepresentable {
	    func asText() -> String {
	        return "A game of Snakes and Ladders with \(finalSquare) squares"
	    }
	}
	println(game.asText())
	// prints "A game of Snakes and Ladders with 25 squares"
```


###采用扩展声明协议###

如果一个类型已经符合协议的所有要求，但尚未指定其采用的协议，你就可以使用一个空扩展使它采用协议。
```js

	struct Hamster {
	    var name: String
	    func asText() -> String {
	        return "A hamster named \(name)"
	    }
	}
	extension Hamster: TextRepresentable {}
```

*Hamster*实例现在可以被用到任何需要*TextRepresentable*类型的地方：
```js

	let simonTheHamster = Hamster(name: "Simon")
	let somethingTextRepresentable: TextRepresentable = simonTheHamster
	println(somethingTextRepresentable.asText())
	// prints "A hamster named Simon"
```

> **注意** <br/>
> 类型不自动采用协议只是通过满足其要求。他们必须显示的声明他们要采用的协议。<br/>


## 协议类型的集合 ##

协议可以被用于类型并存储在集合中，比如数组或字典，就像在
[协议类型](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-XID_352)提到的。下例创建一个*TextRepresentable*数组：

	let things: TextRepresentable[] = [game, d12, simonTheHamster]

现在可以迭代数组中的项，并打印每个条目的文本表示：
```js

	for thing in things {
	    println(thing.asText())
	}
	// A game of Snakes and Ladders with 25 squares
	// A 12-sided dice
	// A hamster named Simon
```

注意*TextRepresentable*类型的*thing*常量。它不是*Dice*或*DiceGame*或*Hamster*类型，即时实际实例幕后是其中的一个类型。尽管如此，因为它是*TextRepresentable*类型，众所周知，*TextRepresentable*有一个*asText*方法，循环中每次调用*thing.asText*是安全的。

## 协议继承 ##

一个协议可以继承一个或多个其他协议，可以添加进一步的需求在其继承的需求之上。协议继承的语法类似于类继承的语法，但是选项需列出多个继承协议，并用逗号分隔。
```js

	protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
	    // protocol definition goes here
	}
```

下例是一个继承自*TextRepresentable*协议的例子：
```js

	protocol PrettyTextRepresentable: TextRepresentable {
	    func asPrettyText() -> String
	}
```

这里例子定义了一个新协议*PrettyTextRepresentable*，它继承自*TextRepresentable*。任何采用*PrettyTextRepresentable*必须满足由*TextRepresentable*强制执行的所有要求，加上额外由*PrettyTextRepresentable*强制执行的需求。在此例中*PrettyTextRepresentable*添加一个单一的需求提供一个叫做*asPrettyText*的实力方法并返回一个*String*。

*SnakesAndLadders*类可以被扩展去采用并符合*PrettyTextRepresentable*协议：
```js
	
	extension SnakesAndLadders: PrettyTextRepresentable {
	    func asPrettyText() -> String {
	        var output = asText() + ":\n"
	        for index in 1...finalSquare {
	            switch board[index] {
	            case let ladder where ladder > 0:
	                output += "▲ "
	            case let snake where snake < 0:
	                output += "▼ "
	            default:
	                output += "○ "
	            }
	        }
	        return output
	    }
	}
```
这个扩展声明它采用
*PrettyTextRepresentable*协议并提供了*SnakesAndLadders*类型协议*asPrettyText*方法的实现。任何*PrettyTextRepresentable*类型必须是*TextRepresentable*类型，所以*asPrettyText*实现首先调用*TextRepresentable*协议的*asText*方法开始输出字符串。字符串后面跟一个冒号和一个换行符，使用这个作为这个漂亮的文本表示的开始，然后遍历这个数组的方块，每个方块跟一个表情符号表示。

- 如果方块的值大于0，它是*ladder*的基础，由 ▲ 表示。
- 如果方块的值小于0，它是*snake*的头，由 ▼ 表示。
- 如果方块的值等于0，它是未使用的方块，由 ○ 表示。 <br/>

此方法实现现在可以被用于打印任何一个*SnakesAndLadders*实例的漂亮的文本描述：
```js

	println(game.asPrettyText())
	// A game of Snakes and Ladders with 25 squares:
	// ○ ○ ▲ ○ ○ ▲ ○ ○ ▲ ▲ ○ ○ ○ ▼ ○ ○ ○ ○ ▼ ○ ○ ▼ ○ ▼ ○
```

## 协议组合 ##
协议组合对于要求一个类型立即符合多种协议是有用的。你可以将多个协议统一到一个协议组合的单一需求中。协议组合的形式为
*protocol<SomeProtocol, AnotherProtocol>*。你可以在一对尖括号
(*<>*)内尽量列出多个协议，并用逗号分隔。

下例是一个将两个分别叫做*Named*和*Aged*协议统一到一个单一的协议组合需求中并被当作一个函数参数的例子：
```js

	protocol Named {
	    var name: String { get }
	}
	protocol Aged {
	    var age: Int { get }
	}
	struct Person: Named, Aged {
	    var name: String
	    var age: Int
	}
	func wishHappyBirthday(celebrator: protocol<Named, Aged>) {
	    println("Happy birthday \(celebrator.name) - you're \(celebrator.age)!")
	}
	let birthdayPerson = Person(name: "Malcolm", age: 21)
	wishHappyBirthday(birthdayPerson)
	// prints "Happy birthday Malcolm - you're 21!"
```

此例定义了一个叫做*Named*的协议，只有一个可获得，类型为*String*的*name*属性。同样定义了一个叫做*Aged*的协议，只有一个可获得，类型为*Int*的*age*属性。这两个协议都采用一个叫*Person*的结构。

此例还定义了一个叫做*wishHappyBirthday*的功能，改功能接受一个叫做*celebrator*的参数。此参数的类型是*protocol<Named, Aged>*，意味着“任何符合*Named*和*Aged*这两个协议的类型”。对于具体传递的参数类型是什么没关系，只要它符合这两个必须的协议。

然后创建了一个叫做*birthdayPerson*的*Person*实例，并传递此新实例到*wishHappyBirthday*功能。因为*Person*符合这两个协议，这是一个有效的调用，*wishHappyBirthday*功能能够打印其生日问候。


> **注意** <br/>
> 协议组合不定义一个新的，永久的协议类型。相反，他们定义了一个临时的本地协议，统一了组合中所有协议的需求。<br/>

##检查协议一致性##

你可以使用[类型转换](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TypeCasting.html#//apple_ref/doc/uid/TP40014097-CH22-XID_443)中描述的*is*和*as*操作去检查协议一致性，并转换为一个指定的协议。检查并转换一个协议和检查并转换一个类型有完全相同的语法：

- 如果一个实例符合协议并且不返回*false*，那么*is*操作返回*true*。
- *as*版本的向下转换操作返回此协议类型的*
- *值，如果此实例不符合协议，那么此值是*nil*。
- 如果*as*版本向下转换操作强制向下转换为协议类型不成功，则触发运行错误。 <br/>

下例定义了一个叫做*HasArea*的协议，只有一个可获得的，类型为*Double*的*area*属性。
```js

	@objc protocol HasArea {
	    var area: Double { get }
	}
```

> **注意** <br/>
> 你可以检查协议一致性，只有当你的协议是由*@objc*属性标记的，像上面*HasArea*协议展示的。该属性表明协议应该暴露为在[通过Cocoa和Objective-C使用Swift](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/index.html#//apple_ref/doc/uid/TP40014216)描述的Objective-C代码。即使你不与Objective-C交互，如果你希望能够检查协议一致性，你仍然需要使用*@objc*标记你的协议。<br/>
> 还要注意使用*@objc*标记的协议只能通过类调用，不是结构或枚举。如果你使用*@objc*标记你的协议是为了检查协议一致性，你将只能应用此协议为*class*类型。<br/>

这里有两个类，*Circle*和*Country*，两者都符合*HasArea*协议：
```js

	class Circle: HasArea {
	    let pi = 3.1415927
	    var radius: Double
	    var area: Double { return pi * radius * radius }
	    init(radius: Double) { self.radius = radius }
	}
	class Country: HasArea {
	    var area: Double
	    init(area: Double) { self.area = area }
	}
```

*Circle*类实现了*area*属性要求，并基于一个存储属性*radius*将此属性作为一个计算属性。*Country*类实现了*area*需求直接作为一个存储属性。两个类正确的符合了*HasArea*协议。

这里有一个不符合*HasArea*协议的*Animal*类：
```js

	class Animal {
	    var legs: Int
	    init(legs: Int) { self.legs = legs }
	}
```
*Circle*，*Country*和*Animal*类没有一个共同的基类。尽管如此，他们都是类，所有三种类型的实力都可以用来初始化一个存储*AnyObject*类型值的数组。
```js

	let objects: AnyObject[] = [
	    Circle(radius: 2.0),
	    Country(area: 243_610),
	    Animal(legs: 4)
	]
```

*objects*数组初始为一个迭代数组，包含一个半径为2的*Circle*实例；*Country*实例初始化为英国面积的平方公里；*Animal*实例初始化为有四条腿。

*objects*数组现在可以被迭代，并且数组中每个*object*都会被检查是否符合*HasArea*协议：
```js

	for object in objects {
	    if let objectWithArea = object as? HasArea {
	        println("Area is \(objectWithArea.area)")
	    } else {
	        println("Something that doesn't have an area")
	    }
	}
	// Area is 12.5663708
	// Area is 243610.0
	// Something that doesn't have an area
```

每当数组中的一个对象符合*HasArea*协议，*as*操作拆开*optional*绑定到一个叫*objectWithArea*的常量,其*optional*值会返回。*objectWithArea*常量是*HasArea*类型，所以*area*属性可以被以类型安全的方式访问并打印。

注意，底层对象在转换过程中没有改变。他们仍然是*Circle*，*Country*和*Animal*。然而，在存储在*objectWithArea*常量时，他们只知道是*HasArea*类型，所以他们的*area*属性可以被访问。


##Optional协议要求##

你可以为协议定义*optional*要求。这些要求不需要被符合协议的类型实现。*Optional*要求前缀使用*@optional*关键字作为协议定义的一部分。

一个*optional*协议要求可以被*optional*链调用，考虑到要求没有被符合协议的类型所实现这种可能性。关于*optional*链的信息，请参照[Optional链](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/OptionalChaining.html#//apple_ref/doc/uid/TP40014097-CH21-XID_312)。

你通过在调用的要求名字后写一个问号来检查一个*optional*要求的实现，例如*someOptionalMethod?(someArgument)*。*Optional*属性要求，以及*optional*方法要求返回一个值，当他们被访问或调用时，将始终返回适当的的类型的一个*optional*值，反映了一个*optional*要求可能没有被实现的事实。

> **注意** <br/>
> *Optional*协议要求只有在你的协议被*@objc*属性标记时指定。即使你不与Objective-C交互，如果你希望指定*optional*要求，你仍然需要使用*@objc*标记你的协议。<br/>
> 还要注意使用*@objc*标记的协议只能通过类调用，不是结构或枚举。如果你使用*@objc*标记你的协议是*optional*要求，你将只能应用此协议为*class*类型。<br/>

下例定义了一个integer-counting类，叫做*Counter*，它使用一个外部数据源来提供增量。这个数据源是*CounterDataSource*协议定义的，它有两个*optional*要求：
```js

	@objc protocol CounterDataSource {
	    @optional func incrementForCount(count: Int) -> Int
	    @optional var fixedIncrement: Int { get }
	}
```

*CounterDataSource*协议定义了一个叫做*incrementForCount*的*optional*方法要求和一个叫做*fixedIncrement*的*optional*属性。这些需求定义了两个不同方式让数据源为*Counter*实例提供一个适当的增量。



> **注意** <br/>
> 严格的说，你可以编写一个自定义类，只要符合*CounterDataSource*，不用实现协议的要求。毕竟这两者都是*optional*。尽管技术上是允许的，但这不会成为一个很好的数据源。<br/>

下例定义的*Counter*类，有一个*optional*的，类型为*CounterDataSource?*的*dataSource*属性：
```js

	@objc class Counter {
	    var count = 0
	    var dataSource: CounterDataSource?
	    func increment() {
	        if let amount = dataSource?.incrementForCount?(count) {
	            count += amount
	        } else if let amount = dataSource?.fixedIncrement? {
	            count += amount
	        }
	    }
	}
```

*Counter*类将当前值存储在变量*count*中。*Counter*同样定义了一个方法*increment*，当每次此方法被调用时*count*变量增加。

*increment*方法首先尝试通过在其数据源中寻找*incrementForCount*的方法实现来增加一个数。*increment*方法使用*optional*链来调用*incrementForCount*，并将当前*count*值当作方法的单一参数传递。

注意在这里的两个不同级别的*optional*链。首先，*dataSource*有可能为*nil*，所以*dataSource*的名字后有一个问号标记表明*incrementForCount*只有*dataSource*为*non-nil*时调用。其次，即使数据源存在，也不能保证它实现了*incrementForCount*，因为它是一个*optional*要求。这就是为什么*incrementForCount*的名字后面也加了一个问号。

因为*incrementForCount*调用可能失败的原因有两种，调用返回一个*optional*的*int*值。这是真的即使在*CounterDataSource*数据源中*incrementForCount*被定义为返回*non-optional*的*int*值。

调用*incrementForCount*后，*optional*的*int*返回打开成一个*amount*常量，使用*optional*绑定。如果*optional*的*int*包含一个值--换言之，如果委托和方法都存在，并且方法返回一个值--拆开的*amount*被添加到存储属性*count*，增量就完成了。

如果不可能从*incrementForCount*方法获取值--要么是因为*dataSource*是*nil*，要么是因为数据源没有实现*incrementForCount*--然后*increment*方法尝试从数据源*fixedIncrement*的属性获取一个值来代替。*fixedIncrement*属性也是一个*optional*要求，所以它的名字同样通过在其后加上问号来使用*optional*链，表明试图访问属性的值会失败。和之前一样，返回值是一个*optional*的*int*值，即使*CounterDataSource*协议定义*fixedIncrement*为*non-optional*的*int*属性。

这里有一个简单的*CounterDataSource*实现数据源每3次查询就返回一个常量值。它通过实现*optional*的*fixedIncrement*属性要求来达到。
```js

	class ThreeSource: CounterDataSource {
	    let fixedIncrement = 3
	}
```

你可以使用*ThreeSource*的实例作为一个新的*Counter*实例的数据源：
```js

	var counter = Counter()
	counter.dataSource = ThreeSource()
	for _ in 1...4 {
	    counter.increment()
	    println(counter.count)
	}
	// 3
	// 6
	// 9
	// 12
```

上面的代码创建了一个*Counter*实例，设置它的数据源为一个新的*ThreeSource*实例，并调用*counter*的*increment*方法四次。正如所料，每次*increment*被调用*Counter*的*count*属性就增加3。

这里有一个稍微复杂点的数据源叫*TowardsZeroSource*，它使得*Counter*实例的当前*count*值能从0开始向上或向下：
```js

	class TowardsZeroSource: CounterDataSource {
	    func incrementForCount(count: Int) -> Int {
	        if count == 0 {
	            return 0
	        } else if count < 0 {
	            return 1
	        } else {
	            return -1
	        }
	    }
	}
```

*TowardsZeroSource*从*CounterDataSource*协议实现了*optional*的*incrementForCout*方法并使用*count*参数值来算出把哪个方向计算在内。如果*count*已经为0，方法返回0表明没有进一步的计算发生。

你可以将*Counter*实例和*TowardsZeroSource*实例一起使用来从-4计算到0。一旦*counter*达到0，不在发生其他计算：
```js

	counter.count = -4
	counter.dataSource = TowardsZeroSource()
	for _ in 1...5 {
	    counter.increment()
	    println(counter.count)
	}
	// -3
	// -2
	// -1
	// 0
	// 0
```
