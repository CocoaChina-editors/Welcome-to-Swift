# 模式

 _模式_ 代表了单值或组合值的结构。例如，元组`(1,2)` 的结构是用逗号分隔两个元素的列表。因为模式代表一种值的结构，而不是一个任何一个特殊的值，你可以用多种值与之匹配。举个例子，模式 `(x,y)` 可以匹配元组 `(1,2)` 以及其他任何两个元素的元组。除了将一个值与模式匹配，你还可以提取组合值的部分或全部，然后把各部分绑定到常量或变量上。

在 Swift 中，模式出现在变量和常量的声明中（在它们的左边），在 `for-in` 语句和 `switch` 语句（在他们的 case 标签）中。虽然模式可能出现在 `switch` 语句的任何一个 case 标签中，但是在其他情况下，只有通配符模式，标识符模式，以及包含这两种模式的模式才能够出现。

你可以为通配符模式，标识符模式，以及元组模式指定一个类型注释，以约束模式只匹配符合特定类型的值。

> 模式的语法
>
> pattern → identifier-pattern­type-annotation­opt­
>
> pattern → value-binding-pattern­
>
> pattern → tuple-pattern­type-annotation­opt
>­
> pattern → enum-case-pattern­
>
> pattern → type-casting-pattern­
>
> pattern → expression-pattern

## 通配符模式

_通配符模式_ 匹配并忽略任何值，包括下划线（\_）。当你不关心被匹配的值时，可以使用通配符模式。比如说，下面的代码在闭区间 `1...3` 中迭代，并忽略每次迭代时区间的当前值：

``` Swift
for _ in 1...3 {
    // 执行三次
}
```

> 通配符模式的语法
>
> wildcard-pattern → _­
 
## 标识符模式

_标识符模式_ 匹配任何值，并将匹配的值绑定到一个变量或常量上。例如，在下面的常量声明中，`someValue` 是一个匹配了 `Int` 型的值为 `42` 的标识符模式。

```Swift
let someValue = 42
```

当匹配成功时，`42` 被绑定（赋值）到常量 `someValue`。

当一个变量或常量声明左侧的模式是标识符模式时，该标识符模式是隐式的值绑定模式。

> 标识符模式的语法
>
> identifier-pattern → identifier­

## 值绑定模式

_值绑定模式_ 绑定匹配的值到变量或常量。绑定匹配值到常量名时，值绑定模式以关键字 `let` 开头；而绑定值到变量名时，以关键字 `var` 开头。

标识符模式包含于值绑定模式中绑定新命名的变量或常量到它们匹配的值上。例如，你可以分解一个元组的元素并把每个元素的值绑定到相应的标识符模式。

```Swift
let point = (3, 2)
switch point {
    // 绑定 x 和 y 到 point 中的元素 
case let (x, y):
    println("The point is at (\(x), \(y)).")
}
// 打印 "The point is at (3, 2)."
```

在上面的例子中，`let` 将元组模式 `(x, y)` 的元素分配到各个标识符模式。由于这个行为，`switch` 语句 `case let (x, y):` 和 `case (let x, let y):` 匹配的值是一样的。

> 值绑定模式的语法
>
> value-binding-pattern → var­pattern­  let­pattern­
 
## 元组模式

_元组模式_ 是包含零个或多个模式由一个逗号分隔的列表，包含在括号中。元组模式匹配相应元组类型的值。

你可以通过使用类型注释来限制一个元组模式，以匹配特定种类的元组类型。例如，在常量声明 `let (x, y): (Int, Int) = (1, 2)` 中的元组模式 `(x, y): (Int, Int)`，只匹配两个元素都是 `Int` 型的元组。如果只需要限制元组模式中的一些元素，直接向这些单个元素提供类型注释即可。例如，在 `let (x: String, y)` 中的元组模式匹配任意包含两个元素且第一个元素是 `String` 型的元组类型。

当元组模式被用作 `for-in` 语句或者变量或常量申明的模式时，它只能包含通配符模式，标识符模式或者其他包含两者的模式。例如，下面的代码是不合法的，因为 `(x, 0)` 中的元素 `0` 是一个表达式模式：

```Swift
let points = [(0, 0), (1, 0), (1, 1), (2, 0), (2, 1)]
// 以下代码不合法
for (x, 0) in points {
    /* ... */
}
```

围绕只包含单个元素的元组模式的括号是不起作用的。模式匹配单个元素类型的值。例如，下面的式子是等价的：

```Swift
let a = 2        // a: Int = 2
let (a) = 2      // a: Int = 2
let (a): Int = 2 // a: Int = 2
```

> 元组模式的语法
>
> tuple-pattern → (tuple-pattern-element-list opt)
>
> tuple-pattern-element-list → tuple-pattern-element | tuple-pattern-element, tuple-pattern-element-list
>
> tuple-pattern-element → pattern

## 枚举实例模式

_枚举实例模式_ 匹配现有的枚举类型的实例。枚举实例模式只出现在 `switch` 语句的 case 标签中。

如果你准备匹配的枚举实例有任意关联的值，则相应的枚举实例模式必须指定一个包含每个关联值元素的元组模式。使用 `switch` 语句来匹配包含关联值枚举实例的例子，请参阅 [Associated Values](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html#//apple_ref/doc/uid/TP40014097-CH12-XID_189)。

> 枚举实例模式的语法
>
> enum-case-pattern → type-identifier opt . enum-case-name tuple-pattern opt

## 类型转换模式

类型转换模式有两种，`is` 模式和 `as` 模式。两种模式都只出现在 `switch` 语句中的 case 标签中。`is` 模式和 `as` 模式有下面的形式：

```Swift
is type
pattern as type
```

如果一个值的类型在运行时和 `is` 模式右侧的指定类型或者指定类型的子类是相同的，那么 `is` 模式会匹配该值。`is` 模式的行为就像 `is` 操作符一样，他们都进行类型转换，但是舍弃了返回类型。

如果一个值的类型在运行时（runtime）和 `as` 模式右边的指定类型或者指定类型的子类是相同的，那么 `as` 模式会匹配该值。如果匹配成功，匹配值的类型将被转换成 `as` 模式左边指定的模式。

使用 `switch` 语句匹配使用 `is` 模式和 `as` 模式的值的例子，请参阅 [Type Casting for Any and AnyObject](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TypeCasting.html#//apple_ref/doc/uid/TP40014097-CH22-XID_448)。

> 类型转换模式的语法
>
> type-casting-pattern → is-pattern­  as-pattern­
>
> is-pattern → is­type­
>
> as-pattern → pattern­as­type­
 
## 表达式模式

_表达式模式_ 代表了一个表达式的值。表达式模式只出现在 `switch` 语句中的 case 标签中。

由表达式模式所代表的表达式通过使用 Swift 标准库中的 `~=` 操作符与输入表达式的值进行比较。如果 `~=` 操作符返回 `true`，则匹配成功。默认情况下，`~=` 操作符使用 `==` 操作符来比较两个相同类型的值。它也可以将一个整数值与一个 `Range` 对象中的整数范围匹配，如下面的例子所示：

```Swift
let point = (1, 2)
switch point {
case (0, 0):
    println("(0, 0) is at the origin.")
case (-2...2, -2...2):
    println("(\(point.0), \(point.1)) is near the origin.")
default:
    println("The point is at (\(point.0), \(point.1)).")
}
// 输出 "(1, 2) is near the origin."
```

你可以重载 `~=` 操作符来提供自定义表达式的匹配行为。例如，你可以重写上面的例子以比较 `point` 表达式和点的字符串表示形式。

```Swift
// 重载 `~=` 操作符以匹配值为整形的字符串
func ~=(pattern: String, value: Int) -> Bool {
    return pattern == "\(value)"
}
switch point {
case ("0", "0"):
    println("(0, 0) is at the origin.")
case ("-2...2", "-2...2"):
    println("(\(point.0), \(point.1)) is near the origin.")
default:
    println("The point is at (\(point.0), \(point.1)).")
}

// 输出 "(1, 2) is near the origin."
```

> 表达式模式的语法
>
> expression-pattern → expression­
