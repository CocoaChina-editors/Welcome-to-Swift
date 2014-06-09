#方法
####方法是由特定类型关联起来的函数。类、结构体和枚举都能定义成实例方法。它封装了特定的任务和给定类型的实例的功能函数。类，结构体和方法也能定义类型方法，它只与类型的本身由关联。类型方法和Objective-C的类方法类似。

####实际上，Swift与C和Objective-C的主要区别就是结构体和枚举可以定义方法。在Objective-C中，只有类才能定义方法。而在Swift中，你可以有选择性的在类、结构体或枚举中定义方法，并且使得在你创建的类中定义方法时更具有灵活性。


##实例方法

####实例方法是属于一个特定的类、结构体或枚举的实例的函数，函数是实例的支柱，通过提供访问实例属性和修改实例属性的方法，或过过提供相关实例功能的目的。实例方法和函数有相同的语法，就像功能的描述。

####你写一个带有开和闭的实例方法作为它的所属类型，一个实例方法具有隐式访问所有其他的实例方法和属性类型。一个实例方法只能被它所属的类的特定实例调用。不能因为没有现有的实例和把孤立。

####这里定义了一个简单的Counter类，它可以用来计算一个操作发生的次数:


class Counter {

    var count = 0
    func increment() {
       count++
   }
   
    func incrementBy(amount: Int) {
        count += amount
    }
    func reset() {
        count = 0
    }
}

####Counter类里定义了三个实例方法：
* increment  计数器增量加1

* incrementBy(amount: Int)计数器由特定的整数作为增量

* reset 重置计数器的值为0

该Counter类也定义了一个属性变量count ,用来维持当前计数器的值的轨道。
你调用实例方法和属性时的语法具有相同点：

let counter = Counter()

// the initial counter value is 0
counter.increment()

// the counter's value is now 1

counter.incrementBy(5)

// the counter's value is now 6

counter.reset()

// the counter's value is now 0


##方法的局部和外部参数名

####函数的参数可以拥有一个局部名称（在函数体内使用）和一个外部名称（在调用函数时使用），作为外部参数名的描述。方法的参数也是如此，因为方法是与类型相关的函数。然而，局部名称和外部名称的默认行为是不同于函数和方法的。

####在Swift中方法相对与Objective-C中的方法非常相似，在Objective-C中和在Swift中的方法名的第一个参数使用介词作为参考，或可以通过前面的Counter类的例子的incrementBy方法可以看出来。使用了一个可用的介词便于调用时就像在阅读句子一样方便。Swift使用这种已制定的命名方法使得在书写时更简单，调用方法时使用默认途径比使用函数参数更简洁。

####具体来说，Swift在方法中默认的将第一个参数名作为方法的局部参数名，也默认的根据局部参数和外部参数名去给第二个和后续的参数命名。在你书写Objective-c方法时很熟悉这种典型的命名公约和调用约定，并使参数名称更符合你的调用方法的表达。

####细想下这个替代版本的Counter类，它定义了一个更复杂的incrementBy方法：

class Counter {

    var count: Int = 0
    func incrementBy(amount: Int, numberOfTimes: Int) {
        count += amount * numberOfTimes
    }
}

####这个incrementBy方法有两个参数－amount 和 numberOfTimes.默认情况下，Swift将amount视为唯一的局部名称，但将numberOfTimes同时视为局部名称和外部名称。调用方法如下：

let counter = Counter()

counter.incrementBy(5, numberOfTimes: 3)

// counter value is now 15

####你不需要为第一个参数的值而去定义一个外部参数名，因为他的目的是明确incrementBy函数的名称。而第二个参数名则是由外部参数名称进行限定，使方法被调用时的目的更为明确。

####如果你在numberOfTimes参数前写了一个散列符号（＃），这种行为则是有效的默认处理方法：

func incrementBy(amount: Int, #numberOfTimes: Int) {

    count += amount * numberOfTimes
}

####上面描述的默认行为是指，在Swift中定义方法时使用和Objective-c相同语法风格书写，被称之为自然的表达方式。

##方法的外部参数名称修饰行为

####有时为方法的第一个参数提供一个外部参数名是非常有用的。尽管这不是默认的行为。你可以添加一个你自己明确的外部名称，或者你也可以使用局部名称作为外部名称并在参数名前加一个散列符号作为参数名的前缀。

####相反的，如果你不想为一个方法的第二个参数或后续参数提供外部参数名，可通过使用下划线符号（_）作为该参数的显式外部参数名称来覆盖默认行为。

##self 属性

####每一个类型的实例都有一个称为 self 的隐式属性，它是完全等同于该实体本身的。你可以使用这个隐式的self属性饮用当前实例的实例方法。

####在上面的例子中，increment方法也可以写成这样：

func increment() {

    self.count++
}

####在实践中，你不需要很经常的在代码中书写self，当你使用了一个已知的属性或方法名的方法时，如果你没用明确写self，Swift会假设你是指当前实例的属性或方法。Counter类里的三个使用了count（而不是self.count）实例方法就证明了这个假设。

####主要的异常发生在一个实例方法的参数名和实例的属性名相同。在这种情况下，参数名优先，有必要参考属性更多的合格方式。你可以使用隐式的self属性区分参数名和属性名。

####在这里，使用了self来区分名称同为x的方法参数和一个实例属性:

struct Point {

    var x = 0.0, y = 0.0
    func isToTheRightOfX(x: Double) -> Bool {
        return self.x > x
    }
}

let somePoint = Point(x: 4.0, y: 5.0)

if somePoint.isToTheRightOfX(1.0) {

    println("This point is to the right of the line where x == 1.0")
}

// prints "This point is to the right of the line where x == 1.0"

####没有self的前缀，Swift将假设它们使用的 x 都是方法的参数x.

##修改值类型的实例方法

####结构体和枚举都是值类型。默认情况下，值类型的属性不能从它的内部实例方法修改。

####然而，如果你需要修改你的结构体或枚举的属性在一个特定的方法中，你可以在这个方法中选择加入变异行为。然后该方法可以变异（即改变）它的属性，任何的更改在方法结束时写回原来的结构里。该方法还可以分配一个新的实例到其隐含的self属性，而这个新的实例将取代现有的实例，当该方法结束时。

####你可以加入这个行为，通过将mutating关键字写在方法的func关键字前：
struct Point {

    var x = 0.0, y = 0.0
    mutating func moveByX(deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}

var somePoint = Point(x: 1.0, y: 1.0)

somePoint.moveByX(2.0, y: 3.0)

println("The point is now at (\(somePoint.x), \(somePoint.y))")

// prints "The point is now at (3.0, 4.0)"

####上面的Point结构体定义了一个变异的moveByX方法，它通过一定量移动一个Point实例。而不是返回一个新的点，这个方法实际上修改了在其上调用的点。mutating关键字添加到方法的定义上，使它能够修改它的属性。

####请注意，你不能使用一个常量的结构类型去调用变异方法，因为它的属性不能被改变。即使它们使可变的属性，如常量结构体实例的存储属性的描述：

let fixedPoint = Point(x: 3.0, y: 3.0)

fixedPoint.moveByX(2.0, y: 3.0)

// this will report an error

##由变异方法分配self

####变异方法可以分配一个全新的实例给隐式的self属性。上面所示的Point例子也可以勇下面的方式来替代：

struct Point {

    var x = 0.0, y = 0.0
    mutating func moveByX(deltaX: Double, y deltaY: Double) {
        self = Point(x: x + deltaX, y: y + deltaY)
    }
}

####moveByX方法的mutating 版本创建了一个全新的结构体，并将x和y的值设置到目标位置。调用这个版本的方法的最终返回结果和调用之前版本的结果完全一样。

####枚举的变异方法可以在同一个枚举里为隐式的self设置不同的值：

enum TriStateSwitch {

    case Off, Low, High
    mutating func next() {
        switch self {
        case Off:
            self = Low
        case Low:
            self = High
        case High:
            self = Off
        }
    }
}

var ovenLight = TriStateSwitch.Low

ovenLight.next()

// ovenLight is now equal to .High

ovenLight.next()

// ovenLight is now equal to .Off

####这个例子定义了一个具有三种状态供选择的枚举。每一次它的next方法被调用， 三种不同的电源状态（Off,Low和High）之间循环选择。

##类型方法

####如上所述，实例方法是由一个特定类型的实例调用的方法。你还可以定义由类型自身调用的方法。这种方法被称为类型方法。你可以通过在func 关键字前写上class 关键字来声明类的类型方法，而在结构体或枚举内定义类型方法则需在func关键字前书写static 关键字来声明。

>#####NOTE
######在Objective-C中，你只能为Objective-C类定义类型级方法。在Swift中，你可以为类、结构体和枚举定义类型级方法。每种类型方法由它的类型来明确范围的。

####类型方法的调用语法和实例方法的调用方法很像。但是，你只能通过类来调用类型方法，而不是通过这个类的实例来调用。这儿有一个叫做SomeClass的类为您展示了如何调用一个类型方法：

class SomeClass {

    class func someTypeMethod() {
        // type method implementation goes here
    }
}

SomeClass.someTypeMethod()

####在类型方法体内，隐式的self属性引用类型本身，而不是该类型的一个实例。对于 结构体和枚举，这意味着你可以使用self来消除静态你操作静态属性、静态方法参数和实例属性和实例方法参数的歧义。

####更为普遍的是，你在一个类型方法体内使用任何不合格的方法和属性名称它都将引用其它类型级的方法和属性。一个类型方法可以通过其它的方法名来调用另一个类型方法，而不需要为类型名加前缀。同样，结构体和枚举的类型方法也能通过使用静态属性名访问静态属性，而不需要类型名做前缀。

####下面的例子定义了一个名为LevelTracker结构体，它通过游戏的不同级别或阶段跟踪玩家的进步。这是一个单人游戏，但可以在一个设备上为多个玩家存储信息。当游戏第一次玩时所有的级别都被上锁（除了第一级）。每当一个玩家完成一个级别，该级别将对设备上的所有玩家解锁。该LevelTracker结构体采用静态属性和方法来跟踪游戏的那些级别被解锁。它还跟踪当前级别的个别玩家水平。

struct LevelTracker {

    static var highestUnlockedLevel = 1
    static func unlockLevel(level: Int) {
        if level > highestUnlockedLevel { highestUnlockedLevel = level }
    }
    static func levelIsUnlocked(level: Int) -> Bool {
        return level <= highestUnlockedLevel
    }
    var currentLevel = 1
    mutating func advanceToLevel(level: Int) -> Bool {
        if LevelTracker.levelIsUnlocked(level) {
            currentLevel = level
            return true
        } else {
            return false
        }
    }
}

####该LevelTracker结构体跟中任何玩家所解锁的最高的级别。这个值被存储在一个名为highestUnlockedLevel的静态属性里。

####LevelTracker还定义了两个类型函数为highestUnlockedLevels工作。首先时一个叫unlockLevel的函数，负责当一个新的级别被解锁时去更新highestUnlockedLevel的值。第二个是一个便利的levelIsUnlocked类型函数,当一个特定的级别被解锁，它返回true。（注意，这些类型方法可以访问highestUnlockedLevel静态属性，你不需要把它写成LevelTracker.highestUnlockedLevel）

####除了静态属性和类型方法，LevelTracker游戏通过一个currentLevel实例属性来追踪每个单个玩家当前正在玩的游戏级别。

####为了帮助管理currentLevel属性，LevelTracker定义了一个名为advanceToLevel的实例方法。在更新currentLevel之前，该方法会检查新的游戏级别是否已经解锁。advanceToLevel方法通过返回一个bool值来指示是否能够设置currentLevel。

####LevelTracker结构体被一个Player类所使用，如下所示，追踪和更新单个玩家的进度：

class Player {

    var tracker = LevelTracker()
    let playerName: String
    func completedLevel(level: Int) {
        LevelTracker.unlockLevel(level + 1)
        tracker.advanceToLevel(level + 1)
    }
    init(name: String) {
        playerName = name
    }
}

####Player类创建了一个LevelTracker的新实例用来跟踪玩家的进度。它也提供了一个名为completedLevel方法，每当玩家完成一个特定的级别时被调用。这个方法会为所有玩家解锁一个新的等级和更新玩家的进度，并将它们带入到一个新的级别里。（advanceToLevel返回的bool值将被忽略，因为在上一行LevelTracker.unlockLevel被调用时，该等级的被解锁已经是已知的了）

####你可以通过Player类创建一个新的玩家实例，看看当玩家完成一个级别会发生什么：

var player = Player(name: "Argyrios")

player.completedLevel(1)

println("highest unlocked level is now \(LevelTracker.highestUnlockedLevel)")

// prints "highest unlocked level is now 2"

####如果你创建了第二个玩家，若你想进入到一个任何一个玩家都没能解锁的游戏等级中，试图设置玩家的当前等级失败：

player = Player(name: "Beto")

if player.tracker.advanceToLevel(6) {

    println("player is now on level 6")
} else {

    println("level 6 has not yet been unlocked")
}

// prints "level 6 has not yet been unlocked"
