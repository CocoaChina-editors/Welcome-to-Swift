#Swift表达方式


在Swift中，一共有四种表达方式：前缀表达，二分表达，简单表达，和后缀表达。Evaluating an expression returns a value, causes a side effect, or both.

前缀表达和二分表达可以让我们在更小的语句中操作运算符。简单表达从概念上讲是获取值的最简单方式。后缀表达和前缀表达和二分表达相似，都可以让你建立更为复杂的表达方式；例如函数调用和成员获取等。我们将在本章节中详细解释每种表达方式。


	GRAMMAR OF AN EXPRESSION
	
	expression → prefix-expressionbinary-expressionsopt
	expression-list → expression  expression,expression-	list

## 前缀表达

前缀表达在表达式的前面加入了一个前缀操作符。前缀操作符后面紧跟表达式，这个表达式可以理解为前缀操作符的参数。

Swift的标准库提供了如下前缀操作符：

* `++ Increment`
* `-- Decrement`
* `! Logical NOT`
* `~ Bitwise NOT`
* `+ Unary plus`
* `- Unary minus`

这些操作符的使用方法请参考Basic Operators 和 Advanced Operators 相关章节。

除了上面的标准库操作符，当一个变量作为参数在函数调用中传递时，`&` 可以放在变量名前面，这种参数称为In-Out Parameters。

	GRAMMAR OF A PREFIX EXPRESSION
	
	prefix-expression → prefix-operatoroptpostfix-	expression
	prefix-expression → in-out-expression
	in-out-expression → &identifier

##二分表达

二分表达是一种将参数放于操作符两边的表达方式，它的形式如下：

`left-hand argument` `operator` `right-hand argument`

Swift 标准库提供了如下二分操作符：

* 幂（无左右相关，优先级 160）
 
	* `<<` 左移
	* `>>` 右移
	
* 乘除法（左相关，优先级150）

	* `*` 乘 
	* `/` 除
	* `%` 模
	* `&*` 乘（忽略溢出）
	* `&/` 除（忽略溢出）
	* `&%` 模（忽略溢出）
	* `&` 位运算和
	
* 加减法（左相关，优先级140）

	* `+` 加
	* `-` 减
	* `&+` 溢出加
	* `&-` 溢出减
	* `|` 或
	* `^` 亦或
	
* 区间（无左右相关，优先级135）

	* `..` 半开区间
	* `...` 闭区间

* 转换（无左右相关，优先级132）

	* `is` 类型检查
	* `as` 类型转换
	
* 比较符（无左右相关性，优先级130）

	* `<` 小于 
	* `<=` 小于等于
	* `>` 大于
	* `>=` 大于等于
	* `==` 等于
	* `!=` 不等于
	* `===` 相同
	* `!==` 不相同
	* `~=` 模式匹配
	
* 连接符（左相关，优先级120）

	* `&&` 逻辑与
	
* 连接符（左相关，优先级120）

	* `||` 逻辑或
	
* 三元运算符（右相关，优先级100）
	* `？` 三元运算条件符
	
* 赋值运算符（右相关，优先级90）

	* `=` 赋值 
	* `*=` 乘并赋值
	* `/=` 除并赋值
	* `%=` 取模并赋值
	* `+=` 自加并赋值
	* `-=` 自减并赋值
	* `<<=` 左移并赋值
	* `>>=` 右移并赋值
	* `&=` 位运算和并赋值
	* `^=` 位运算亦或并赋值
	* `|=` 位运算或并赋值
	* `&&=` 逻辑和并赋值
	* `||` 逻辑或并赋值
	
	这些操作符的使用方法，请参照 [基本运算符和高级运算符](wait for link)。
	
	
	```
	注意 
	 
  	在解析的时候，二分运算符组成的表达式将被拆解为一个列表的形式。这个列表将被转化为一个序列来计算。例如，`2 + 3 * 5` 最开始被理解为一个有五个元素的列表：`2`，`+`，`3`，`*`， `5`。然后才转换为序列的形式(2+(3*5))。
	```

```
二分表达式的语法
binary-expression → binary-operatorprefix-expression
binary-expression → assignment-operatorprefix-expression
‌binary-expression → conditional-operatorprefix-expression
‌binary-expression → type-casting-operator
‌binary-expressions → binary-expressionbinary-expressionso
```

##赋值运算符

赋值运算符为表达式赋予新值。其表达方式如下：

`表达式` = `值`

通过这个表达式，右侧的值将赋予左侧的表达式。如果左侧是一个元素组合（tuple），右侧的值必须与其具有相同额元素个数（允许多层组合）。赋值的操作和每一部分的元素一一对应。例如

	(a, _, (b, c)) = ("test", 9.45, (12, 3))
	// a 是 "test", b 是 12, c 是 3,  9.45 被自动忽略了。

赋值操作符没有返回值。

##三元运算符

```条件表达式``` ？``` 如果为真则执行本表达式```：```如果为假则执行本表达式```

如果条件表达式为真，则执行第一个表达式并返回它的值，否则将执行第二个表达式并返回它的值。没有使用的表达式不会被执行。

更多的例子参照[三木运算符](for linkage)。

##类型转换运算符

一共有两种类型转换运算符：```as``` 和 ```is```，它们的形式如下：

```表达式``` ```as``` ```类型```

```表达式``` ```as?``` ```类型```

```表达式``` ```is``` ```类型```


```as```运算符将一个表达式转换为特定的类型。它的使用方式有以下几种：

* 如果这种向特定类型的转换成功，那么这个表达式将被返回为此特定类型的一个实例。典型的例子是向上转型。
* 如果这种像特定类型的转换失败，那么将引发编译期错误。
* 如果在编译期转换成功与否不能确定，这个转换表达式将变为可选值类型。在运行期，如果转换成功，表达式的值将会打包成一个可选值并返回，否则，此值将为空，典型的例子为向下类型转换。
	
	```
	class SomeSuperType {}
	class SomeType: SomeSuperType {}
	class SomeChildType: SomeType {}
	let s = SomeType()
 
	let x = s as SomeSuperType  
	// 成功，则返回值	SomeSuperType
	let y = s as Int           
	// 失败，编译期错误
	let z = s as SomeChildType  
	//  可能在运行期失败错误，返回值类型为SomeChildType?
	```
	
对编译器来说，使用 ```as```来确定类型 和 类型声明 具有相同的作用。下面的例子可以说明：

	let y1 = x as SomeType  // 使用as表明类型
	let y2: SomeType = x    // 使用类型声明
	
```is```操作符将字运行时检查表达式是否为特定类型。如果是则返回真（true），否则返回假（false）。

在编译期检查的结果必须不确定，下面的例子是不合法的：

	“hello" is String
	"hello" is Int
更多关于类型转换的解释和例子请参考 [类型转换](for linkage)。

##简单表达式

简单表达是最基础的一类表达形式。他们可以单独使用或与其他符号一起使用来组成前缀表达，二分表达，以及后缀表达。

	GRAMMAR OF A PRIMARY EXPRESSION

	primary-expression → identifiergeneric-argument-clauseopt
	primary-expression → literal-expression
	primary-expression → self-expression
	primary-expression → superclass-expression
	primary-expression → closure-expression
	primary-expression → parenthesized-expression
	primary-expression → implicit-member-expression
	primary-expression → wildcard-expression
	
##表达形式

文字表达由普通的文字（例如字符串或数字），数组或字典名，或者下面的特别文字

Literal      | Type 		  | Value
:-----------:| :-----------: | :-----------:
__FILE__     |     String    | The name of the file in which it appears
__LINE__     |    Int   	  | The line number on which it appears. 
__COLUMN__	 |	    Int		  | The column number in which it begins.
__FUNCTION__ |		String	  | The name of the declaration in which it appears.

在一个函数内部，__FUNCTION__就是这个函数的名字；在方法内部，它就是方法名；在属性的get方法和set方法中它就是这个属性的名字；在特殊成员例如```init```和```subscript```中，它是关键字的名字；在文件的顶层，它是当前模型名。

数组的文字声明是一个包含有序值的集合。其结构如下：

[```value1```, ```value2```, ```...``` ]

数组内最后一个表达式可以跟句号。一个空数组声明可以用一个空的中括号代表（[]）。数组值的类型为```T[]```，```T```代表数组内部表达式的类型。如果其中的元素类型不统一，T代表他们最小超类。

字典的文字声明是一个有键值对的无序集合。它有如下表达：

[```key```:```value1```, ```key```:```value2```, ```...``` ]

字典内最后一个表达式可以跟句号。一个空字典的声明是中括号内加一个冒号（[:]）以此和空数组的表达形式区分。

字典的类型为```Dictionary<keytype, ValueType>```，```keytype```是键的类型，```ValueType```是其对应值的类型。如果字典有多种类型，```keytype```和```ValueType```使他们代表值类型的最小超类。


	GRAMMAR OF A LITERAL EXPRESSION

	literal-expression → literal
	literal-expression → array-literal  dictionary-literal
	literal-expression → __FILE__  __LINE__  __COLUMN__ __FUNCTION__
	array-literal → [array-literal-itemsopt]
	array-literal-items → array-literal-item,opt  array-literal-item,array-literal-items
	array-literal-item → expression
	dictionary-literal → [dictionary-literal-items]  [:]
	dictionary-literal-items → dictionary-literal-item,opt  dictionary-literal-item,dictionary-literal-items
	dictionary-literal-item → expression:expression
	
##Self的使用

```self```是对当前类型或当前类的实例的一种显式引用。其使用方法如下：


self
self.```member name```
self[```subscript index```]
self(```initializer arguments```)
self.init(```initializer arguments```)

在构造器，角标，或实例方法中，```self```是指当前实例的引用。在静态方法或类方法中，```self```指当前类的引用。

```self```用于当同一作用域存在同名变量的时候来明确指代以访问成员变量，例如临时函数变量与成员变量同名的情况：

	class SomeClass {
    	var greeting: String
    	init(greeting: String) {
        	self.greeting = greeting
    	}
	}
	
在一个mutating类型值得方法中，你可以为一个新的实例赋值为self，例如：

	struct Point {
    	var x = 0.0, y = 0.0
    	mutating func moveByX(deltaX: Double, y deltaY: Double) 			{
        	self = Point(x: x + deltaX, y: y + deltaY)
    	}
	}
	
	

GRAMMAR OF A SELF EXPRESSION

	self-expression → self
	self-expression → self.identifier
	self-expression → self[expression]
	self-expression → self.init

##超类的使用

超类的表达式可以让一个类与它的超类产生关联。如下例：

super.```member name```   
super[```subscript index```]   
super.init(```initializer arguments```)

第一种形式可以获取超类的成员。第二种形式可以获取超类的角标方法实现。第三种形式可以用来访问超类的初始化器。

子类使用超类来实现他们自己的成员，角标和初始化器。

	GRAMMAR OF A SUPERCLASS EXPRESSION

	superclass-expression → superclass-method-expression  superclass-subscript-expression  superclass-initializer-expression
	superclass-method-expression → super.identifier
	superclass-subscript-expression → super[expression]
	superclass-initializer-expression → super.init

##局部方法块的使用

局部方法块创建一个闭合区间，在其他语言中，也被称为lambda或匿名函数。像方法声明一样，局部方法块也包含供其执行的语句并从此闭合区间取值。它的形式如下：

{ (```parameters```) -> ```return type``` in

```statements```  
}
	
这里的参数和形式与函数的声明的形式相同。

它具有一些更简洁的写法：

* 局部方法块可以不写它的参数类型，返回值类型。如果省略参数名和其类型，同时省略语句前的```in```关键字；如果无法推断其类型，则会产生编译时错误。
* 如果局部方法块省略了它的参数名，它的参数将会隐式的以$加其位置编号命名：$0,$1,$2等等。
* 只有一个表达式的局部方法块默认返回表达式的值。当周围有表达式操作引用类型时，这个表达式的内容也会参与其中。下面的几个局部方法块是等价的:

```
myFunction {
    (x: Int, y: Int) -> Int in
    return x + y
}
 
myFunction {
    (x, y) in
    return x + y
}
 
myFunction { return $0 + $1 }
 
myFunction { $0 + $1 }
```

关于局部方法块在函数中传参的问题可参考 [函数调用的使用](for link)。

使用抓取列表，局部方法块可以显式的得到特定值。抓取列表内容由中括号括起，由逗号分隔，后面是参数列表。当你使用抓取列表时，即使你省略参数名，参数类型和返回值类型，你也必须写```in```关键字。

抓取列表的每个入口可以标明```weak```或```unowned```来抓取值的弱引用或无主引用。

	myFunction { print(self.title) }                    // strong capture
	myFunction { [weak self] in print(self!.title) }    // weak capture
	
你也可以向其中的非匿名值加入判断表达，当局部方法块执行时，将对表达式做出判断，并使用特定的引用强度抓取。例如：

	// Weak capture of "self.parent" as "parent"
	myFunction { [weak parent = self.parent] in print(parent!.title) }
	
更多局部方法块的信息和实例，请参考[局部方法块](for link)。

```
GRAMMAR OF A CLOSURE EXPRESSION

‌ closure-expression → {closure-signatureoptstatements}
‌ closure-signature → parameter-clausefunction-resultoptin
‌ closure-signature → identifier-listfunction-resultoptin
‌ closure-signature → capture-listparameter-clausefunction-resultoptin
‌ closure-signature → capture-listidentifier-listfunction-resultoptin
‌ closure-signature → capture-listin
‌ capture-list → [capture-specifierexpression]
‌ capture-specifier → weak  unowned  unowned(safe) unowned(unsafe)
```

##匿名成员的使用

匿名成员是一种访问成员的简单形式，例如在枚举类型或类方法的上下文中，引用类型可推断时使用。它有如下形式：

.```member name```

例如：

	var x = MyEnumeration.SomeValue
	x = .AnotherValue


GRAMMAR OF A IMPLICIT MEMBER EXPRESSION

	implicit-member-expression → .identifier
	
##括号表达式的使用

括号表达式内部表达式用逗号分隔，外部用括号括起。每个表达式内部可以有一个表达式名，它与表达式用冒号分隔。其格式如下：

(```identifier 1```: ```expression 1```, ```identifier 2```: ```expression 2```,``` ...```)

利用括号表达式我们可以为函数调用传参。如果括号内只有一个值，其括号表达式的类型即为此值得类型。例如（1）的类型是```Int```，不是```(Int)```。

```
GRAMMAR OF A PARENTHESIZED EXPRESSION

‌ parenthesized-expression → (expression-element-listopt)
‌ expression-element-list → expression-element  expression-element,expression-element-list
‌ expression-element → expression  identifier:expression
```
##占位符表达式的使用

占位符表达是为了在赋值时故意忽略掉一个值。例如在下面的例子中，10被赋给```x```，20则被忽略。

	(x, _) = (10, 20)
	

```
GRAMMAR OF A WILDCARD EXPRESSION

‌wildcard-expression → _
```

##后修饰表达的使用

使用后修饰运算符或其他后修饰语法可以创建后修饰表达式。从语法上讲，所有的简单表达式也是后修饰表达式。

Swift标准库提供了以下后修饰运算符：

* ++ 自加
* -- 自减

关于此运算符的使用，请参考[基本操作符]() 和[高级操作符]();


```
GRAMMAR OF A POSTFIX EXPRESSION

‌ postfix-expression → primary-expression
‌ postfix-expression → postfix-expressionpostfix-operator
‌ postfix-expression → function-call-expression
‌ postfix-expression → initializer-expression
‌ postfix-expression → explicit-member-expression
‌ postfix-expression → postfix-self-expression
‌ postfix-expression → dynamic-type-expression
‌ postfix-expression → subscript-expression
‌ postfix-expression → forced-value-expression
‌ postfix-expression → optional-chaining-expression
```

##函数调用的方法

函数的调用方法为：函数名家括号，括号内为参数列表，多个参数用逗号隔开。例如：

```function name```(```argument value 1```, ```argument value 2```)

函数名可以以任意具有函数类型的名字来命名。

如果函数声明中定义了参数名，那么函数调用时，参数名必须在参数值前面并用冒号分开，示例如下：

```function name```(```argument name 1```: ```argument value 1```, ```argument name 2```: ```argument value 2```)

函数的调用时可以在闭合括号的后面紧跟一个局部方法块后缀。这个后缀代码块也是这个函数的后加的参数。下面的两个函数调用等效：

```
// someFunction 的参数列表由一个参数x和一个局部方法块组成
someFunction(x, {$0 == 13})
someFunction(x) {$0 == 13}
```

如果局部方法块是唯一参数，小括号可以省略。

```
// someFunction 只有一个参数
myData.someMethod() {$0 == 13}
myData.someMethod {$0 == 13}
```
```
GRAMMAR OF A FUNCTION CALL EXPRESSION

‌ function-call-expression → postfix-expressionparenthesized-expression
‌ function-call-expression → postfix-expressionparenthesized-expressionopttrailing-closure
‌ trailing-closure → closure-expression
```

##初始化器表达式的用法

初始化表达式将访问该类型的初始化器。其结构如下：

```expression```.init(```initializer arguments```)

初始化器的调用方法与函数调用相同，但与函数不同的是，初始化器不是一个值。例如：

var x = SomeClass.someClassFunction // 正确
var y = SomeClass.init              // 错误

你也可以使用初始化器来调用超类的初始化器。

```
class SomeSubClass: SomeSuperClass {
    init() {
        // subclass initialization goes here
        super.init()
    }
}
```
```
GRAMMAR OF AN INITIALIZER EXPRESSION

‌ initializer-expression → postfix-expression.init 
```
##显式成员表达式的使用

显式成员表达式可以访问有名的类，元组，或组件的成员。在表达式和成员名之间用点隔开。

```expression```.```member name```

有名成员变量可作为类的声明的一部分或其拓展。例如：

```
class SomeClass {
    var someProperty = 42
}
let c = SomeClass()
let y = c.someProperty  // Member access
```

元组的命名是用数字由零开始按顺序隐式命名的。例如:

```
var t = (10, 20, 30)
t.0 = t.1
// 现在 t 是 (20, 20, 30)
```
组件成员可以获取其最顶层声明。

```
GRAMMAR OF AN EXPLICIT MEMBER EXPRESSION

‌ explicit-member-expression → postfix-expression.decimal-digit
‌ explicit-member-expression → postfix-expression.identifiergeneric-argument-clauseopt
```

self的后缀表达方式的使用

self的后缀表达使用是用表达式或类型名加“.self” 构成的。其形式如下：

```expression```.self
```type```.self

第一种形式得到表达式的值。例如x.self就是x值。

第二种形式将获得类型的值。利用这种形式可以以值的形式获得类型。例如。SomeClass.self就是SomeClass类，你可以将其传递给接受类类型参数的函数或方法。

```
GRAMMAR OF A SELF EXPRESSION

‌ postfix-self-expression → postfix-expression.self
```

##动态类型表达式的使用

动态类型表达式由表达式加“.dynamicType”构成。其结构如下：

```expression```.dynamicType

表达式不能是类名。完整的dynamicType表达式获得表达式的运行时类名值。如下例：

```
class SomeBaseClass {
    class func printClassName() {
        println("SomeBaseClass")
    }
}
class SomeSubClass: SomeBaseClass {
    override class func printClassName() {
        println("SomeSubClass")
    }
}
let someInstance: SomeBaseClass = SomeSubClass()
// someInstance的编译时类型是SomeBaseClass , 但是
// someInstance的运行时类型是SomeSubClass
someInstance.dynamicType.printClassName()
// 打印 "SomeSubClass"
```


```
GRAMMAR OF A DYNAMIC TYPE EXPRESSION

‌ dynamic-type-expression → postfix-expression.dynamicType
```

##角标表达式的使用

角标表达式使用对应声明的get方法个set方法来获取角标。它的格式如下：

```expression```[```index expressions```]

为了获取角标表达式的值，将角标表达方式作为参数调用角标的get方法。当然，为了设置它的值，角标的set方法也用同样的方式调用。


更多关于角标声明，请参见[角标声明]()。

```
GRAMMAR OF A SUBSCRIPT EXPRESSION

‌ subscript-expression → postfix-expression[expression-list]
```

##强制拆包表达式的使用

当一个可选值不为空时，可以对可选值进行强制拆包。 它的结构如下：

```expression```！

##可选赋值链表达式的使用

可选赋值链通过使用可选值加后缀，使用非常简单。它的结构如下：

```expression```.?

后缀？将以可选值的形式返回其参数。

在后缀表达中，可选赋值链的取值方式很特别。如果可选赋值链为空，后面其他的表达式不会被执行且整个表达式返回空。如果可选赋值链结果不是空，表达式将被拆包并用继续执行余下的后缀表达式。不论何种情况，后缀表达式的值都是一个可选值类型。

如果可选赋值链是嵌套的，只返回最外层的的可选值类型。在下面的例子中，如果```c```不是nil，它的值将被拆包，并用以获得```.property``` 和 ```.performAction()```的值。整个```c?.property.performAction() ```是可选值类型。

```var c: SomeClass?
var result: Bool? = c?.property.performAction()
```

下面的例子则示意了不使用可选赋值链的情形。

```
if let unwrappedC = c {
    result = unwrappedC.property.performAction()
}
```

```
GRAMMAR OF AN OPTIONAL-CHAINING EXPRESSION

‌ optional-chaining-expression → postfix-expression?
```