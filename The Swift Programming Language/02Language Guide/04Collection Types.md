#集合类型（Collection Types）

Swift提供了两种*集合类型*来存放多个值——数组(Array)和字典(Dictionary)。数组把相同类型的值存放在一个有序链表里。字典把相同类型的值存放在一个无序集合里，这些值可以通过唯一标识符（也就是键）来引用和查找。

在Swift里，数组和字典里所能存放的值的类型是明确的。这意味着你不能误把一个错误类型的值添加到数组或字典里，也意味着你可以明白无误地知道从数组或字典里取得的值会是什么类型的。Swift集合是类型明确的，这保证了你的代码会清楚地知道它们所能处理的值的类型，并让你能在代码开发阶段就发现任何输入错误。

> 注意：
> Swift的数组在赋值给常量、变量或者传值给函数、方法时，它的行为表现和其它类型并不一样。详情参见[可变集合](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-XID_151)和[集合类型的赋值和拷贝行为](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_109)

##数组(Array)

*数组*在一个有序链表里存储了多个类型相同的值。同一个值可以在数组的不同位置出现多次。

Swift的数组对它们能存放的值的类型是明确的。这不同于Objective-C的`NSArray`类和`NSMutableArray`类，Objective-C的数组能存储任何类型的对象，并且不提供关于这些对象自身的任何信息。在Swift里，任何一个特定的数组所能存储的值，其类型总会被确定下来，或者通过显式的类型说明，或者通过类型推断，并且该类型不必是类类型。例如，如果你创建了一个`Int`型数组，你就不能把任何非Int型的值插入这个数组。Swift数组是类型安全的，它总是知道它能存储什么值。

###数组类型的简写语法

Swift数组的类型是`Array<SomeType>`，这里的`SomeType`是某种数组能够存放的类型。你也可以把数组类型简写为`SomeType[]`。尽管这两种形式功能上是一样的，我们更偏向于使用简写形式，本书里所有数组类型都是使用简写形式。

###数组字面量

可以用一个*数组字面量*来初始化一个数组，简单地把一个或多个值放在一起就可以了。数组字面量的写法是一行用逗号隔开的值，并在行的两端用一对方括号包起来：

[`value 1`, `value 2`, `value 3`]

下面的例子创建了一个存放`String`类型值，名为`shoppingList`的数组：

```
var shoppingList: String[] = ["Eggs", "Milk"]
// shoppingList 用两个元素完成初始化
```

根据`String[]`，变量`shoppingList`被声明为“一个存放`String`值的数组”。因为这个数组指明了值类型为`String`，所以它只能存放`String`类型的值。这里的`shoppingList`数组使用了一个数组字面量来初始化，该数组字面量含有两个`String`类型的元素(`"Eggs"`和`"Milk"`)。

> 注意：
> 
> 数组`shoppingList`被声明为变量(用关键字`var`)而不是常量(用关键字`let`)，是因为在下面的例子中会往这个购物清单里添加更多的元素。

在本例中，这个数组字面量只包含了两个`String`值。这和`shoppingList`变量定义中的类型一致（一个存放`String`值的数组），所以可以把这个数组字面量直接赋值给`shoppingList`进行初始化。

得益于Swift的类型推断，如果使用数组字面量来初始化一个数组，这个数组字面量里的值具有相同的类型，你可以不必明确写出这个数组的类型。上面初始化`shoppingList`的代码可以简写为：

```
var shoppingList = ["Eggs", "Milk"]
```

因为数组字面量中的所有值具有相同的类型，Swift可以推断出`shoppingList`变量的类型就是`String[]`。

###数组的存取与修改

对数组的存取与修改可以通过数组的方法和属性来进行，或者使用数组的下标语法。

要知道数组中元素的数量，可以查看它的只读属性`count`：

```
println("The shopping list contains \(shoppingList.count) items.")
// 输出“The shopping list contains 2 items.”
```

使用Boolean型的`isEmpty`属性，可以快速检查`count`属性是否为0：

```
if shoppingList.isEmpty {
    println("The shopping list is empty.")
} else {
    println("The shopping list is not empty.")
}
// 输出“The shopping list is not empty.”
```

往数组的末尾添加一个元素，可以调用数组的`append`方法：

```
shoppingList.append("Flour")
// shoppingList现在包含3个元素了，看起来有人要摊薄饼啊
```
往数组末尾添加一个元素，也可以使用`+=`操作符：

```
shoppingList += "Baking Powder"
// shoppingList现在包含4个元素了
```

你也可以使用`+=`操作符把一个类型相同的数组连接到数组后面：

```
shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
// shoppingList现在包含7个元素了
```

从数组取得一个值可以使用*下标语法*。在数组名后面紧跟着的一对方括号中，传进去要取得的元素的索引值：

```
var firstItem = shoppingList[0]
// firstItem 等于 "Eggs"
```

要注意数组第一个元素的索引值为0，而不是1。Swift的数组总是从0开始索引的。

你可以使用下标语法来改变给定索引的已存在的值：

```
shoppingList[0] = "Six eggs"
// 这个清单的第一项现在是“Six eggs”了，而不是"Eggs"
```

你可以使用下标语法一次性改变指定范围的值，即使将要被替换掉的元素的数量和将要替换成的元素的数量不一样。下面的例子将`"Chocolate Spread"`，`"Cheese"`和 `"Butter"`替换为`"Bananas"`和`"Apples"`

```
shoppingList[4...6] = ["Bananas", "Apples"]
// shoppingList现在包含6个元素
```

> 注意：
> 不能使用下标语法添加新元素到数组末尾。如果试图使用超出数组范围的下标来取用或存放一个元素，会产生运行时错误。在使用一个索引值之前，应该把它跟数组的`count`属性进行比较，以检测它是否有效。除非`count`是`0`（意味着这是个空数组），数组的最大有效索引总是`count - 1`，因为数组的索引是从0开始的。

插入一个元素到特定位置，可以调用数组的`insert(atIndex:)`方法：

```
shoppingList.insert("Maple Syrup", atIndex: 0)
// shoppingList现在包含7个元素
// 清单的第一个元素现在是"Maple Syrup"
```

本次调用insert函数，通过指明的下标`0`，向购物清单的开头添加了一个值为"Maple Syrup"的新元素。

类似地，你可以使用`removeAtIndex`方法从数组删除一个元素。该方法删掉指定索引上的元素，并返回这个被删掉的元素（如果你不需要返回值，可以忽略它）：

```
let mapleSyrup = shoppingList.removeAtIndex(0)
// 索引为0的元素已从数组中删掉了
// shoppingList现在包含6个元素，不包含"Maple Syrup"
// 常量mapleSyrup现在等于被删掉的字符串"Maple Syrup"
```

当一个元素被删除时，数组中的不会留下任何空白无元素的地方。所以在索引`0`处的元素又变为`"Six eggs"`了：

```
firstItem = shoppingList[0]
// firstItem现在等于"Six eggs"
```

如果想删除数组的最后一个元素，可以使用`removeLast`方法，而不必使用`removeAtIndex`方法，这样就可以避免还得调用数组的`count`属性。类似`removeAtIndex`方法，`removeLast`方法也返回被删除的元素：

```
let apples = shoppingList.removeLast()
// 数组的最后一个元素被删除了
// shoppingList现在包含5个元素，不包含"cheese"
// 常量apples现在等于被删掉的字符串"Apples"
```

###数组的迭代访问

你可以通过`for-in`循环来迭代访问整个数组的值。

```
for item in shoppingList {
    println(item)
}
// Six eggs
// Milk
// Flour
// Baking Powder
// Bananas
```

如果要获得每个元素的索引及其对应的值，可以使用全局的`enumerate`方法来迭代使用这个数组。`enumerate`方法对每个元素都会返回一个由索引值及对应元素值组成的元组。你可以把元组中的成员转为变量或常量来使用：

```
for (index, value) in enumerate(shoppingList) {
    println("Item \(index + 1): \(value)")
}
// Item 1: Six eggs
// Item 2: Milk
// Item 3: Flour
// Item 4: Baking Powder
// Item 5: Bananas
```

要获得关于`for-in`循环的更多资料，请查看[For Loops](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-XID_154)。

###数组创建与初始化

可以通过初始化语法创建一个特定类型的空数组，数组中不包含任何初始值：

```
var someInts = Int[]()
println("someInts is of type Int[] with \(someInts.count) items.")
// 输出 "someInts is of type Int[] with 0 items.
```

要注意变量`someInts`会被推断为`Int[]`类型，因为它接收的是`Int[]`型初始化函数的返回值。

并且，如果上下文中已经提供了类型信息，例如方法的参数、已经定义类型的常量或变量，你就可以使用一个空的数组字面量来创建一个空的数组。空的数组字面量被写为`[]`，就是一对不包括任何内容的方括号。

```
someInts.append(3)
// someInts现在包含了一个整型值
someInts = []
// someInts现在成为了一个空数组，但其类型仍然是Int[]
```
Swift的`数组`还提供了一个生成若干个重复元素组成的数组的初始化函数。把要添加到新数组里的元素数量（`count`）和一个合适的默认值(`repeatedValue`)传参给该初始化函数即可：

```
var threeDoubles = Double[](count: 3, repeatedValue: 0.0)
// threeDoubles类型为Double[]，值为[0.0, 0.0, 0.0]
```

得益于Swift的类型推断，在使用这个初始化函数的时候，不必精确说明该数组的类型，因为这能从默认值中推断出来：

```
var anotherThreeDoubles = Array(count: 3, repeatedValue: 2.5)
// anotherThreeDoubles的类型被推断为Double[]，值为[2.5, 2.5, 2.5]
```

##字典（Dictionary）

*字典*是一种存储多个类型相同的值的容器。每个值都和一个唯一的*键*相对应，这个键在字典里就是其对应值的唯一标识。跟数组不同，字典里的元素并没有特定的顺序。在“字典”中使用键来查询其对应值的方式，跟在“现实世界的字典”中使用单词查询单词定义差不多。

Swift的字典对它们能存放的键和值的类型是明确的。这不同于Objective-C的`NSDictionary`类和`NSMutableDictionary`类，Objective-C的字典能存储任何类型的对象作为键或值，并且不提供关于这些对象自身的任何信息。在Swift里，任何一个特定的字典键和值，其类型总会被确定下来，或者通过显式的类型说明，或者通过类型推断。

Swift的字典类型是`Dictionary<KeyType, ValueType>`，其中`KeyType`是字典中键的类型，`ValueType`是字典中值的类型。

对键类型`KeyType`的唯一限制是，它必须是*可哈希的*，也就是说，它必须能够提供一个方式让自己被唯一表示出来。Swift的所有基础类型（例如`String`、`Int`、`Double`和`Bool`）默认都是可哈希的，这些类型都能够用作字典中的键。枚举成员中没有绑定值的值（参见[枚举](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html#//apple_ref/doc/uid/TP40014097-CH12-XID_185)）默认也是可哈希的。

###字典字面量

可以用一个*字典字面量*来初始化一个数组，其语法格式类似于之前看到的数组字面量。字典字面量是写一个具有单个或多个键值对字典的简便方法。

*键值对*是一个键和一个值的组合。在字典字面量中，每对键值对中的键和值使用冒号分开，键值对之间用逗号分开，用一对方括号将这些键值对包起来：

[`key 1`: `value 1`,&nbsp; `key 2`: `value 2`,&nbsp; `key 3`: `value 3`]

下面的例子创建了一个存储国际机场名字的字典，在这个字典中，键是三字母的国际航空运输协会码，值是机场的名字：

```
var airports: Dictionary<String, String> = ["TYO": "Tokyo", "DUB": "Dublin"]
```
`airports`被定义为一个`Dictionary<String, String>`类型的字典，也就是“一个键的类型为String，值的类型也为String的字典”。

> 注意：
> 
> 字典`airports`被声明为变量(用关键字`var`)而不是常量(用关键字`let`)，是因为在下面的例子中会往这个字典里添加更多的机场。

这里的字典`airports`使用了一个字典字面量来初始化，该字典字面量含有两个键值对，第一对的键为`"TYO"`、值为`"Tokyo"`，第二对的键为`"DUB"`、值为`"Dublin"`。

在本例中，此数组字面量只包含了两个`String: String`键值对。这和`airports`变量定义中的类型一致（键的类型为String，值的类型也为String），所以可以把这个字典字面量直接赋值给`airports`进行初始化。

就像数组一样，如果使用具有相应类型的键和值的字典字面量来对字典进行初始化，你可以不必明确写出这个字典的类型。上面初始化`airports`的代码可以简写为：

```
var airports = ["TYO": "Tokyo", "DUB": "Dublin"]
```
因为这个字典字面量中的所有键具有相同的类型，所有值也具有相同的类型，Swift可以推断出`airports`变量的类型就是`Dictionary<String, String>`。

###字典的存取与修改

对字典的存取与修改可以通过字典的方法和属性来进行，或者使用字典的下标语法。就像数组一样，可以通过只读属性`count`获得`Dictionary`中元素的数量：

```
println("The dictionary of airports contains \(airports.count) items.")
// 输出"The dictionary of airports contains 2 items."
```

可以使用下标语法向字典中添加新的元素。以一个合适类型的新键作为下标索引，并且赋给它一个合适类型的值：

```
airports["LHR"] = "London"
// 字典airports现在包含3个元素
```

也可以使用下标语法来改动某个键对应的值：

```
airports["LHR"] = "London Heathrow"
// "LHR"的值被改为"London Heathrow
```

在对特定键设置或更新值时，也可以使用`updateValue(forKey:)`函数来替代下标。就像上面例子中的下标语法，`updateValue(forKey:)`函数在键不存在的时候就设置一个新值，在键存在的时候就更新该值。和下标语法不一样的是，`updateValue(forKey:)`函数在更新一个值之后，会返回原来的*老*值。这让你能够检测是否发生了值的更新。

`updateValue(forKey:)`函数返回一个值的类型的可选值。例如一个值类型为`String`的字典，该函数返回值的类型为`String?`。如果更新前该键的值存在，函数返回值就是该键更新前的值，如果不存在，函数返回值就是`nil`：

```
if let oldValue = airports.updateValue("Dublin International", forKey: "DUB") {
    println("The old value for DUB was \(oldValue).")
}
// 输出 "The old value for DUB was Dublin.
```

你也可以使用下标语法从字典中对一个特定的键取值。因为可能会用到一个没有对应值存在的键，所以字典的下标语法返回的也是该字典值类型的可选值。如果字典中有该键对应的值，下标语法就返回该值，如果不存在，下标语法就返回`nil`：

```
if let airportName = airports["DUB"] {
    println("The name of the airport is \(airportName).")
} else {
    println("That airport is not in the airports dictionary.")
}
// 输出 "The name of the airport is Dublin International."
```

也可以使用下标语法把一个键对应的值赋为`nil`来删除该键值对：

```
airports["APL"] = "Apple International"
// "Apple International" 不是APL的机场，所以要删除它
airports["APL"] = nil
// APL键值对已经从字典中删除了
```

从字典中删除键值对也可以使用`removeValueForKey`函数。如果该键值对存在，该函数就返回本删掉的值，如果不存在，就返回`nil`：

```
if let removedValue = airports.removeValueForKey("DUB") {
    println("The removed airport's name is \(removedValue).")
} else {
    println("The airports dictionary does not contain a value for DUB.")
}
// 输出 "The removed airport's name is Dublin International."
```

###字典的迭代访问

你可以通过for-in循环来迭代访问整个字典的键值对。它会对字典中的每个元素都会返回一个` (key, value) `元组，你可以把元组中的成员转为变量或常量来使用：

```
for (airportCode, airportName) in airports {
    println("\(airportCode): \(airportName)")
}
// TYO: Tokyo
// LHR: London Heathrow
```

要获得关于`for-in`循环的更多资料，请查看[For Loops](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-XID_154)。

可以通过字典的`keys`属性和`values`属性，获得一个可以迭代访问的集合：

```
for airportCode in airports.keys {
    println("Airport code: \(airportCode)")
}
// Airport code: TYO
// Airport code: LHR

for airportName in airports.values {
    println("Airport name: \(airportName)")
}
// Airport name: Tokyo
// Airport name: London Heathrow
```

如果你需要将字典的所有键或者所有值放到一个`Array`对象里，可以直接使用字典的`keys`属性或者`values`属性：

```
let airportCodes = Array(airports.keys)
// airportCodes为["TYO", "LHR"]

let airportNames = Array(airports.values)
// airportNames为["Tokyo", "London Heathrow"]
```

> 注意：
> Swift的`Dictionary`类型不是一个有序集合，迭代访问字典的keys、values、键值对时候的顺序并无具体规定。

###生成一个空字典

像数组一样，你可以使用初始化语法创建一个特定类型的空`Dictionary`：

```
var namesOfIntegers = Dictionary<Int, String>()
// namesOfIntegers是一个Dictionary<Int, String>类型的空字典
```

这个例子创建了一个存储数字名称的空字典，键的类型是`Int`，值的类型是`String`。

如果上下文中已经提供了类型信息，可以使用空字典字面量`[:]`来创建一个空字典：

```
namesOfIntegers[16] = "sixteen"
// namesOfIntegers现在含有1个键值对
namesOfIntegers = [:]
// namesOfIntegers又成为一个类型为Int，String的空字典
```

> 注意：
> 实际上，Swift的数组和字典类型是通过*泛型集合*实现的。要获得关于泛型类型和集合的更多资料，请查看[Generics](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Generics.html#//apple_ref/doc/uid/TP40014097-CH26-XID_234)。

##集合的可变性

数组和字典都把多个值存放在一个集合里。如果你创建了一个数组或者字典，并且将之指定为变量，那么该集合就是*可变的*，这意味着在集合被创建后，可以通过增加或删除元素来改变集合的容量大小。相反地，如果你指定一个数组或字典为常量，那么该数组或字典就是*不可变的*，其容量大小不能被改变。

对字典来说，不可变还意味着你不能改变字典里某个键的值。一个不可变的字典一旦被设置值后，它里面的内容就不能再改变。

但是，数组的不可变性跟字典略有不同。尽管你不能进行任何可能会改变数组大小的操作，但是你可以给数组中的某个索引设置一个新的值。这使得Swift的数组在大小固定的情况下能够达到最佳的性能。

Swift数组类型的可变性行为还会影响数组对象是如何被复制与修改的，详细信息可参见[集合类型的复制与拷贝行为](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_109)。

> 注意：
> 在任何不需要改变集合大小的地方，最好将集合定义为不可变的。这么做能让Swift的编译器优化该集合的性能。