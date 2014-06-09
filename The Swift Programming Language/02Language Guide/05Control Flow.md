# Control Flow - 控制流


Swift提供了所有c类语言的控制流结构。包括for和while循环来执行一个任务多次；if和switch语句来执行确定的条件下不同的分支的代码；break和continue关键字能将运行流程转到你代码的另一个点上。

除了C语言传统的for-condition-increment循环，Swift加入了for-in循环，能更加容易的遍历arrays, dictionaries, ranges, strings等其他序列类型。

Swift的switch语句也比C语言的要强大很多。 Swift中switch语句的case语句不会“掉入”下一个case，避免了c语言忘记写break语句产生的错误。 case可以匹配许多不同的模式，包括范围匹配，元组匹配或者抛给指定的类型。匹配值在一个case条件下可以绑定到临时常量或变量，可以在case的代码块中使用，复杂匹配条件下可以表示为每一个case的条件

 
## For Loops - For循环
for循环用来多次执行一组语句 ，Swift提供了两种形式：

* for-in执行范围，序列，集合或级数等每一项中的一组语句
* for-condition-increment执行一组语句直到确定的条件出现，通常在每一个循环结束前递增一个计数

####For-In
使用for-in来遍历集合中的项目，比如数组的范围，排列中的项或者字符串中的字符。 

下面的例子打印了表中的5个元素

 
    1. for index in 1...5 {
    2.     println("\(index) times 5 is \(index * 5)")
    3. }
    4. // 1 times 5 is 5
    5. // 2 times 5 is 10
    6. // 3 times 5 is 15
    7. // 4 times 5 is 20
    8. // 5 times 5 is 25 


例子中被迭代集合的项是一个封闭范围内从1到5的数字，就是上面标识为封闭范围操作符的 (...)。
Index的值被设为第一个数据的范围(1), 然后执行循环中的语句。在本例中，循环只包含了一句话，根据index现有的值打印5次乘法表的一个结果。当执行完语句之后，index的值被更新为范围中的第二个值,然后再次调用println函数。这个操作会一直持续，直到范围的终点。 

  在上面的例子中，index是一个常量，它的值在每次迭代的开始时自动初始化，使用前不会被声明，就是简单的将其隐性声明纳入循环的声明，不需要使用let来声明关键字。

    NOTE
    Index常量仅仅存在于循环的范围内。如果你想要在循环之后得到index的值，或者想要使用index的值作为变量，你必须在循环之前声明它。

如果不需要范围的值，可以用下划线替代变量名来忽略这些值:

     let base = 3
     let power = 10
     var answer = 1
     for _ in 1...power {
         answer *= base
     }
     println("\(base) to the power of \(power) is \(answer)")
     // prints "3 to the power of 10 is 59049"

例子中计算两数相乘（在本例中，3乘以10）。乘法初始值为1，每次乘3，乘10次，使用半封闭循环从0到9。这个计算不需要通过循环来知道每个计数器的值--仅仅只需要执行正确的循环次数。下划线操作符 _ (用于替代循环变量)将忽略掉个体值，并且在每一次循环迭代期间不给现有的变量提供访问。   


使用for-in循环来迭代出array中的每一个项：

    let names = ["Anna", "Alex", "Brian", "Jack"]
    for name in names {
        println("Hello, \(name)!")
    }
    // Hello, Anna!
    // Hello, Alex!
    // Hello, Brian!
    // Hello, Jack!

同样可以迭代字典来访问其中的键值对。当迭代字典时里面的每一个项都以（key,value）元组的形式来返回，你可以在for-in的循环体中分解 (key, value) 元组的成员，把成员作为显性命名的常量来使用。下面例子，字典的key被分解为animalName的常量，字典的值被分解名为legCount的常量：

    let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
    for (animalName, legCount) in numberOfLegs {
    println("\(animalName)s have \(legCount) legs")
    }
    // spiders have 8 legs
    // ants have 6 legs
    // cats have 4 legs
Dictionary中的项的迭代顺序可能跟它们插入时的顺序不一样。因为Dictionary中的内容本质上是无序的，所以迭代它们不能保证检索时的顺序。更多关于排列和字典的内容详见 [Collection Types](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-XID_133)章节。

除了排列和字典，for-in循环还能迭代字符串中的Character（字符）：

    for character in "Hello" {
        println(character)
    }
    // H
    // e
    // l
    // l
    // o

####For-Condition-Increment For-条件-递增
除了for-in循环，Swift还支持传统C语言按条件递增的for循环

    for var index = 0; index < 3; ++index {
        println("index is \(index)")
    }
    // index is 0
    // index is 1
    // index is 2

这是常用的形式：

    for <initialization>; <condition>; <increment> {
        <statements>
    }
封号把循环定义隔为了三个部分，跟C语言一样。然而与C不同的是，Swift不需要用括号把 “初始化; 条件; 增量” 的代码块包起来。

循环按照下面流程执行：
     
1. 当循环第一次进入，_initialization expression_（初始化表达式）计算一次，设置好循环所需的常量或者变量。
2. 计算_condition expression_（条件表达式）。如果计算结果为false（假），循环终止，并继续执行for循环尾括号（}）后面的代码。如果结果为（true）真，则执行循环体大括号内的代码。
3. 在所有的语句执行完后，计算_increment expression_(增量表达式)。计数器可能递增或递减，也可能根据语句执行的结果将初始化变量设为新的值。计算完增量表达式返回到第2步，条件表达式再次被计算。

上面描述的循环体的形式和执行过程可以简单的等同于：

    <initialization>
    while <condition> {
        <statements>
        <increment>
    }    


常量和变量在初始化表达式中的声明（比如var index = 0）只在for循环自己内部有效。如果需要知道index最终的值，必须在循环开始前声明index：

    var index: Int
    for index = 0; index < 3; ++index {
        println("index is \(index)")
    }
    // index is 0
    // index is 1
    // index is 2
    println("The loop statements were executed \(index) times")
    // prints "The loop statements were executed 3 times"
注意，循环完成后index最终的值是3，不是2。最后一次执行增量表达式调用了++index，把index设为3，使得index<3等于false，循环结束。


##While Loops - While循环
while循环在条件变为false前执行一组语句，这类循环最好用在第一个迭代开始前并不知道迭代器的数字的时候。Swift提供了两种while循环:

* while 在每次通过循环的开头计算条件
* do-while 在每次通过循环的结尾计算条件

####while
一个while循环开始于计算单个的条件，如果条件为true，一组语句将重复直到条件变为false。

这是常见的while形式：

    while <condition> {
        <statements>
    }

比如，玩这个叫_Snakes and Ladders_的游戏（或_Chutes and Ladders_）:
![5-1](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Art/snakesAndLadders_2x.png "Title here")
 

游戏的规则是这样的：

* 板子上有25个矩形，然后目标是到达或超越25号方块。
* 每一轮，你先摇六面骰子，然后按照水平方向的虚线箭头移动到对应数字的方块上。
* 如果走完落在梯子的底部，你可以爬上梯子。
* 如果走完落在蛇的头部，你可以走到那条蛇的尾部。

这个游戏板由一个Int型数组展示出来。它的大小基于finalSquare常量，该常量用来初始化数组并在之后检查胜利条件。游戏板初始化为26个值为0的Int型数据，不是25个（分别位于0至25的索引）：

1.   let finalSquare = 25
2.   var board = Int[](count: finalSquare + 1, repeatedValue: 0)

一些方块给蛇与梯子设有具体的值。游戏板里，你能在有方块有梯子脚的地方向上移动正数，而有蛇头的地方你只能向下移动负数：

1. board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
2. board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08

方块3有梯子脚，所以你可以移动到方块11。为了表述这个动作，board[03]等于+08,等同于一个整型数值8（即3和11的差）。一元运算符加运算符（+i）与一元减运算符（-i）相平衡，如果数值小于10就用0替代，这样所板上的定义就对齐了。(风格调整不是必须的，但代码简洁很有必要)

玩家开始于方块0，就在游戏板左下角的外面。第一下投骰是把玩家带到游戏版里面：

    var square = 0
    var diceRoll = 0
    while square < finalSquare {
        // roll the dice
        if ++diceRoll == 7 { diceRoll = 1 }
        // move by the rolled amount
        square += diceRoll
        if square < board.count {
            // if we're still on the board, move up or down for a snake or a     ladder
        square += board[square]
        }
    }
    println("Game over!")

这个例子很简单的模拟了投骰子。用随机数的生成器来替代，diceRoll从0开始。每一个while循环，diceRoll通过自加运算符（++i）递增，然后检查是否过大。++diceRoll的返回值等于diceRoll自加以后的值。如果返回值等于7，骰子值则过大，重设为1。这样diceRoll的值会一直是1，2，3，4，5，6，1，2等等。

在摇骰子后，玩家根据diceRoll移动方块。有可能骰子的数会让玩家超过方块25，这样就算游戏结束。为了描述这个场景，代码在向board[square]添加值以前先检查square的值是否少于board数组的count属性，如果是则根据现有的square值将玩家上移或下移到相应梯子或蛇。

如果不执行这个检查，board[square]会可能尝试取得board数组界限外的值，导致越界。如果square现在等于26，代码将尝试检查board[26]，这个值超过了数组限制。

现有的While循环执行到最后，会检查循环条件看循环是否会再次执行。如果玩家已经移动或者方块超过25，循环条件会计算为false，游戏结束。

在这个例子中使用While循环是比较合适的，因为游戏的长度在循环的开头不明确，让循环一直执行直到特定的满足条件出现。



####Do-While
While循环的另一个形式是do-while，在考虑循环条件前先执行一次整个循环体，然后再继续重复循环直到条件为false。

下面是do-while的常见形式:

    do {
       <statements>
    } while <condition>


再来看看Snakes and Ladders的例子，用do-while而不是while来实现。finalSquare, board, square, 和diceRoll都用相同的方式初始化：

    let finalSquare = 25
    var board = Int[](count: finalSquare + 1, repeatedValue: 0)
    board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
    board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    var square = 0
    var diceRoll = 0
在这个版本的游戏中，循环中第一个操作是检查一个梯子或蛇。没有梯子能直接把玩家带到方块25，所以不可能只爬一个梯子就赢了。因此在循环里先检查梯子或蛇会更安全。

游戏的开始，玩家在“方块0”。 board[0]永远等于0，没有别的功能：

    do {
        // move up or down for a snake or ladder
        square += board[square]
        // roll the dice
        if ++diceRoll == 7 { diceRoll = 1 }
        // move by the rolled amount
        square += diceRoll
    } while square < finalSquare
    println("Game over!")

在代码检查后，开始摇骰子，玩家通过diceRoll方块向前移动，该循一直环执行到最后。

循环条件(while square < finalSquare)跟之前例子一样，但是这次会在第一次循环的结尾才计算。do-while循环的结构比while循环更适合本例。上面do-while中，在循环条件确认square仍旧在游戏板里面后，square += board[square]会直接进行计算，不必进行数组越界的检查。



##Conditional Statements - 条件语句

编程中常常根据不同的条件执行不同的代码，你可能会要代码在出错后运行额外的语句，或者当数值越界时展示一个消息。我们可以用conditional（条件）来实现。

Swift提供两种方式来添加代码的分支，常见的if和switch语句。显然，用if语句来计算只有少量分支的的条件，而Switch用于更复杂的情况，特别是在模式匹配的时候有助于选择合适的代码分支来执行。


####If
在下面最简单的例子里，if语句有一个if条件。所有的语句都只有在if条件为true的情况下才执行：

     var temperatureInFahrenheit = 30
     if temperatureInFahrenheit <= 32 {
         println("It's very cold. Consider wearing a scarf.")
     }
     // prints "It's very cold. Consider wearing a scarf."

前面的例子检查了温度是否等于32摄氏度。如果是则打印消息。否则不打印消息，直接执行if语句大括号后面的代码。

if语句可以提供一个二选一的语句，常见的：_else clause_，用于当if条件为false时。这些语句用else关键字来控制：

     temperatureInFahrenheit = 40
     if temperatureInFahrenheit <= 32 {
         println("It's very cold. Consider wearing a scarf.")
     } else {
         println("It's not that cold. Wear a t-shirt.")
     }
     // prints "It's not that cold. Wear a t-shirt."
两个大括号中的一个被执行。因为温度增加超过了40摄氏度，已经不需要建议去带围巾，所以else分支被触发。

你可以使用多个if语句，像这样增加条件：

     temperatureInFahrenheit = 90
     if temperatureInFahrenheit <= 32 {
         println("It's very cold. Consider wearing a scarf.")
     } else if temperatureInFahrenheit >= 86 {
         println("It's really warm. Don't forget to wear sunscreen.")
     } else {
         println("It's not that cold. Wear a t-shirt.")
     }
     // prints "It's really warm. Don't forget to wear sunscreen."
   这里增加的if语句用于应对极端炎热的情况。最后的else条件保留，打印既不冷也不热的情况。
   
   最后的else条件是可选的，因此如果不需要写完整可以去掉。
   
        temperatureInFahrenheit = 72
     if temperatureInFahrenheit <= 32 {
         println("It's very cold. Consider wearing a scarf.")
     } else if temperatureInFahrenheit >= 86 {
         println("It's really warm. Don't forget to wear sunscreen.")
     }

在这个例子中，温度既不冷也不热才能触发if或者else条件来打印信息。


####Switch
Switch语句用一个值来匹配相对应的几个匹配模式。然后执行相对应的代码块，基于一开始匹配成功的模式。switch语句提供了应对多种选择情况的处理来替代if语句。

最简单的形式，switch比较一个值对应的一个或者多个相同形式的值：

     switch some value to consider {
     case value 1:
         respond to value 1
     case value 2,
     value 3:
         respond to value 2 or 3
     default:
         otherwise, do something else
     }

每个switch语句由多个可能的case（情况）构成，都用标记的case关键字开头。除了比较对应的值，Swift还为每种case应对更复杂的匹配模式提供了几种方法，后面章节再说。

每一个switch语句都必须_exhaustive_（详细），并且每一个所考虑类选可能的值都必须匹配switch中的一个case。如果不能对应到switch中的所有case的值，可以定义个默认的选取器来解决。选取器用default关键字来表示，必须出现在最后。

下面的例子用switch语句匹配了一个小写字符，someCharacter：

     let someCharacter: Character = "e"
     switch someCharacter {
     case "a", "e", "i", "o", "u":
         println("\(someCharacter) is a vowel")
     case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m",
     "n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
         println("\(someCharacter) is a consonant")
     default:
         println("\(someCharacter) is not a vowel or a consonant")
     }
     // prints "e is a vowel"

Switch语句第一个case匹配了5个小写的元音字母。相似地，第二个case匹配所有的辅音小写字母。

case中并不需要写上所有其他的英语字母，所以switch提供了default来匹配其他所有不是元音也不是辅音的情况。这样的条件保证了switch没有遗漏。



####No Implicit Fallthrough - 没有隐性掉入
相比C和objective-c中的switch语句，Swift中（如果忘了写break）的switch不会默认的掉落到每个case的下面进入另一个case。相反，switch语句当第一个遇到的case完成时就完成了它整个的执行，不需要写break。因此比起C语言，swift的switch更加安全和简单，避免了执行多个case的错误。

     NOTE
     如果你需要，仍旧可在case执行完以前跳出来，详见：[Break in a Switch Statement]见下面章节




每个case的主干包括只少一个可执行的语句。下面这样写是无效的，因为第一个case是空的：

    let anotherCharacter: Character = "a"
    switch anotherCharacter {
    case "a":
    case "A":
        println("The letter A")
    default:
        println("Not the letter A")
    }
    // this will report a compile-time error


不像C语言中的Switch，这里的switch语句不能匹配"a"和"A"。比如，case "a"会在编译时报错：没有找到任何可执行语句。尽可能的避免了意外从一个case掉入另一个，这样使代码更安全。

如果有多个匹配对象的，可以用逗号隔开，像下面这样写成多行：

    switch some value to consider {
    case value 1,
    value 2:
        statements
    }



    NOTE
    为特别的switch case选择掉落行为，可以使用fallthrough关键字，在下文中详解


####Range Matching - 范围匹配

switch中case的值可以检查他们内在的范围。这个例子使用数字范围可以提供任意大小数字的自然语言计数。
  
    let count = 3_000_000_000_000
    let countedThings = "stars in the Milky Way"
    var naturalCount: String
    switch count {
    case 0:
        naturalCount = "no"
    case 1...3:
        naturalCount = "a few"
    case 4...9:
        naturalCount = "several"
    case 10...99:
        naturalCount = "tens of"
    case 100...999:
        naturalCount = "hundreds of"
    case 1000...999_999:
        naturalCount = "thousands of"
    default:
        naturalCount = "millions and millions of"
    }
    println("There are \(naturalCount) \(countedThings).")
    // prints "There are millions and millions of stars in the Milky Way."

####Tuples 元组

你可以使用元组在相同的switch语句中测试多个值。每一个元组中的元素都可以试着和范围中不同的值进行匹配。另外，用下划线（_）标示符来匹配任意可能的值。

下面例子中使用一个点坐标（x,y），用元组型(Int, Int)来表示，可以在下面的图中分类出来：



    let somePoint = (1, 1)
    switch somePoint {
    case (0, 0):
        println("(0, 0) is at the origin")
    case (_, 0):
        println("(\(somePoint.0), 0) is on the x-axis")
    case (0, _):
        println("(0, \(somePoint.1)) is on the y-axis")
    case (-2...2, -2...2):
        println("(\(somePoint.0), \(somePoint.1)) is inside the box")
    default:
        println("(\(somePoint.0), \(somePoint.1)) is outside of the box")
    }
    // prints "(1, 1) is inside the box"




![5-2](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Art/coordinateGraphSimple_2x.png)

Switch语句决定了点是否在原点（0，0）上，在红色的x轴上，在橙色的y轴上，在蓝色4X4的矩形为中心的原点内，或者在矩形外。

与C语言不同，Swift允许多个switch的case考虑相同的值。实际上点(0,0)能匹配例子中所有的四个case。然而，如果多个匹配出现，第一个匹配成功的case将被使用。点（0，0）能首先匹配case（0，0），所以其他所有的case将被忽略。


####Value Bindings 值绑定
一个switch的case能绑定用于匹配临时常量或变量值，在case的分支代码里使用。这就是_value binding_（值绑定），因为这些值在case的代码体中是临时常量或变量的“边界”。

下面的例子有一个点（x,y），用元组型(Int,Int)来表示，在图种展示出来如下:

    let anotherPoint = (2, 0)
    switch anotherPoint {
    case (let x, 0):
        println("on the x-axis with an x value of \(x)")
    case (0, let y):
        println("on the y-axis with a y value of \(y)")
    case let (x, y):
        println("somewhere else at (\(x), \(y))")
    }
    // prints "on the x-axis with an x value of 2



![5-3](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Art/coordinateGraphMedium_2x.png)

switch语句来确定点是否在红色x轴上，在橙色y轴，或者其他地方。

三个switch的case都声明了占位常量x和y，暂时从anotherPoint占用一个或两个元组值。第一个case里，case (let x, 0)，匹配给任意一个y值为0，并分派点的x值给临时常量x的点。相似地，第二个case，case (0, let y)，匹配给一个x值为0，分派点的y值给临时常量y的点。

一旦临时常量被声明，他们将在case的代码块中使用。这里他们将作为简写在println函数中打印出来。

注意switch语句没有default的case。最后一个case，case let (x, y)，声明了包含两个占位常量可以匹配任何值的元组。作为结果，它匹配任何可能的值，不需要default语句switch就足够完美了。

上面的例子中，声明x和y用了关键词let，因为这里不需要在case的代码段里改变它们的值。然而，他们可以用变量来代替，使用var关键词。如果用了变量，则会创建临时变量并初始化为合适的值。任何对变量的改变都只会影响case代码段中的部分。

####Where
switch的case能使用where子句来进一步判断条件。
下面的例子将点(x,y)在下图种分类：

    let yetAnotherPoint = (1, -1)
    switch yetAnotherPoint {
    case let (x, y) where x == y:
        println("(\(x), \(y)) is on the line x == y")
    case let (x, y) where x == -y:
        println("(\(x), \(y)) is on the line x == -y")
    case let (x, y):
        println("(\(x), \(y)) is just some arbitrary point")
    }
    // prints "(1, -1) is on the line x == -y"

![5-4](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Art/coordinateGraphComplex_2x.png)

switch语句判断了点是否在绿色斜线上且x == y，或在紫色斜线上且x == -y，或都不是。

三个switch的case声明了占位常量x和y，临时占用point中元组值。这些常量作为where子句的一部分，用来创建动态的筛选。只有当where子句的条件结果为true，Switch的case则会匹配现有point的值。

就像在前一个例子中，最后的case匹配所有可能的值，所以不需要default。







##Control Transfer Statements - 控制转移语句

控制转移语句能改变已经执行代码的顺序，能使代码跳转到别的部分。Swift有四个句子：

* continue
* break
* fallthrough
* return

control, break和fallthrough在下文中详解。reture语句在[Functions](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-XID_204)中描述。

####Continue
Continue语句告诉循环体终止现在的操作，然后开始迭代下一个循环。好像在说“我这次迭代做完啦”，总之不会离开循环体。

    NOTE
    在for-condition-increment循环中，调用了continue后累加器依旧会计算。循环会继续像平时一样工作，只有循环体中的代码会被跳过。

下面例子中从一个小写字符串中移除了所有的元音和空格，创建了个字谜短语：

    let puzzleInput = "great minds think alike"
    var puzzleOutput = ""
    for character in puzzleInput {
        switch character {
        case "a", "e", "i", "o", "u", " ":
            continue
        default:
            puzzleOutput += character
        }
    }
    println(puzzleOutput)
    // prints "grtmndsthnklk"
上面代码里，只要匹配到元音字母或空格，就会触发continue关键字。使本次迭代立即终止，然后直接跳入下次迭代的开头。这个方式使得switch代码块能匹配(和忽略)元音字母与空格，比用代码块把每一个要打印字符都匹配一次的好。


####Break
Break语句能立即终止整个控制流。可以根据你想要的在switch或循环语句里的任何地方终止整个执行。

#####Break in a Loop Statement - 循环中的Break
当在循环体中使用break，循环会立即停止，并将控制流带到循环体括号(})后方的第一行代码里。循环体里其他的代码不会被执行，也不会开始下一次迭代。




#####Break in a Switch Statement - Switch中的break
在switch里使用break，switch语句会立即终止，并将控制流带到switch语句括号(})后方的第一行代码里。

这种特性可以用于switch里匹配（或忽略）一个或多个case。因为Swift的switch是穷举的，并且不允许有空case的，有时候要慎重的匹配和为了使意图明显而忽略了case。当switch匹配了break语句，case中的break能直接终止整个switch。

    NOTE
    一个switch的case只能包含一个作为编译错误的注释。注释不是语句，不会导致switch的case被忽略。只能使用break语句来跳过case。

下面的例子中switch有一个字符值，并且判断四种语言之一中是否有数字符号。一个简单的switch中包含了多个值：

    let numberSymbol: Character = "三"  // Simplified Chinese for the number 3
    var possibleIntegerValue: Int?
    switch numberSymbol {
    case "1", "١", "一", "๑":
        possibleIntegerValue = 1
    case "2", "٢", "二", "๒":
        possibleIntegerValue = 2
    case "3", "٣", "三", "๓":
        possibleIntegerValue = 3
    case "4", "٤", "四", "๔":
        possibleIntegerValue = 4
    default:
        break
    }
    if let integerValue = possibleIntegerValue {
        println("The integer value of \(numberSymbol) is \(integerValue).")
    } else {
        println("An integer value could not be found for \(numberSymbol).")
    }
        // prints "The integer value of 三 is 3."

例子中通过检查numberSymbol判断是否是拉丁语，阿拉伯语，中文或者泰文符号来得到1至4的数字。如果匹配成功，其中的一个case里会赋给可选型Int变量possibleIntegerValue一个合适的整型值。

在switch语句执行完成后，例子里用了可选型绑定来确定值是否被发现。possibleIntegerValue变量有一个隐性的初始值nil，具有可选型的优点。只有在前四个case中，当possibleIntegerValue被赋了实际的值可选绑定才会成功。

在上面例子中列出所有可能的字符值不太实际，所以default能提供一个没任何字符被选中情况下的容器。这的default不用执行任何操作，只写个简单的break。一旦default被匹配，break语句立即终止switch，并继续执行 if let语句。

####Fallthrough 
Swift中的Switch不会掉下到case的下方并进入下一个case。因此，整个switch语句毁在第一个匹配的case完成后结束。相反，C语言要求你在每个case的末尾插入一个break来防止掉入。相比于C语言，Swift的switch禁止默认掉入让更加简洁和可控，这样避免了执行多个case的错误。

如果你确实需要C式的掉入特性，你可以使用fallthrough关键词。下面的例子用fallthrough来创建一段描述数字的文本：

    let integerToDescribe = 5
    var description = "The number \(integerToDescribe) is"
    switch integerToDescribe {
    case 2, 3, 5, 7, 11, 13, 17, 19:
        description += " a prime number, and also"
        fallthrough
    default:
        description += " an integer."
    }
    println(description)
    // prints "The number 5 is a prime number, and also an integer."


例子中声明了一个名为description的String型变量并分派一个初始值。然后函数用switch匹配integerToDescribe的值。如果integerToDescribe的值符合素数列表中的一项，最后的description会增加一段字符，注意数字都是素数。然后用fallthrough关键字让代码“掉到”default里。default的代码中再额外的给字符串添加些描述，最后switch结束。

如果integerToDescribe不跟素数表中任何一项匹配，那根本就不会匹配switch的第一个case。这里面没有其他的case，因此integerToDescribe直接进入default容器。

在switch执行完成后，数字的描述用println函数打印出来。在本例中，5是正确的答案。

    NOTE
    fallthrough关键字不检查case里的条件，会直接掉入下一个case。fallthrough简单的让代码执行到下一个case（或default）的代码块中，和标准C语言的特性一样。

  
####Labeled Statements - 标签语句
你可以嵌套循环或在switch语句中嵌套其他的循环，Swift语句种的switch可以创建复杂的控制流结构。 然而，循环和switch语句都可以过早地使用break。因此，有时明确的使用break来终止代码很有用。类似的，如果你有多个嵌套的循环，continue会更有用。

为了做到这一点，你可以用_statement label_来标记循环或switch，与break或continue语句一起使用这个标签来终止或继续标记语句的执行。

标签语句作为引导关键词在标签的同一行，后面跟着冒号“：”。 这里有一个用了该符号的while循环例子，所有的循环和switch都是相同的。


    <label name>: while <condition> {
        <statements>
    }

下面的例子里有一个用了标签的while循环，使用了break和continue语句，_Snakes and Ladders_游戏可以在上文中看到：

* 为了胜利，你必须恰好到达25号方块

如果骰子带你超过了25号方块，你必须重新丢骰子直到正好抵达25号方块。

游戏板和上文中的一样

![5-6](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Art/snakesAndLadders_2x.png)

变量 finalSquare, board, square, diceRoll的初始化都跟前文一样：
 
    let finalSquare = 25
    var board = Int[](count: finalSquare + 1, repeatedValue: 0)
    board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
    board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    var square = 0
    var diceRoll = 0


这个版本用while循环和switch语句来实现游戏逻辑。While循环的标签为gameLoop，为_Snakes and Ladders Game_标记出游戏主体。

While循环的条件是while square != finalSquare，意思是你必须落在25号方块上：

    gameLoop: while square != finalSquare {
        if ++diceRoll == 7 { diceRoll = 1 }
        switch square + diceRoll {
        case finalSquare:
            // diceRoll will move us to the final square, so the game is over
            break gameLoop
        case let newSquare where newSquare > finalSquare:
            // diceRoll will move us beyond the final square, so roll again
            continue gameLoop
        default:
            // this is a valid move, so find out its effect
            square += diceRoll
            square += board[square]
        }
    }
    println("Game over!")



骰子在每个循环的最开始摇动。然后玩家立即移动，switch语句用来判断移动的结果，算出是否允许移动：

* 如果骰子带玩家抵达最后的方块，游戏结束。break gameLoop语句将控制流带到游戏结束的地方，即while循环外的第一行。
* 如果骰子超过了最后的方块，则移动无效，玩家需要再次投骰。break gameLoop会终止本次迭代，开始下次迭代。
* 在其他的情况下，骰子有效。玩家向前移动方块并根据游戏逻辑检查蛇或梯子。循环结束，控制流返回到while条件判断处，决定是否需要再次迭代。




      NOTE
      如果上面的break语句不使用gameLoop标签，将会中断switch语句，而不是while语句。使用gameLoop标签可以更加明确的让控制流终止。

      同样要注意，当调用continue gameLoop来跳到下一个迭代循环中不是必须使用gameLoop标签。因为这里只有一个循环体，不会受到其他循环体的影响。然而，用了gameLoop标签也没什么不好。这样做保持与break旁边的标签统一，有助于让游戏逻辑能更加清晰阅读和理解。




