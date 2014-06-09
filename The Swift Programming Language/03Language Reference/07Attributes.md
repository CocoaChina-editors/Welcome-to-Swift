# 属性


*属性* 提供了更多关于声明和类型的信息。在 Swift 中有两种类型的属性，一种用于声明，一种用于类型。例如，`required` 属性－当用于类的指定或者便利初始化声明时－指明每个子类都必须实现它的初始化函数。`noreturn` 属性－当用于函数或者方法的类型时－指明函数或者方法无需返回值。

你可以用字符 `@` 加上属性名和属性参数来指定一个属性：

>@属性名

>@属性名(属性参数)
	
含有参数的声明属性可以为属性指定更多的信息，可以用于特殊的声明。这些*属性参数* 被包含在圆括号里，参数的格式由属性决定。

## 声明属性

声明属性只能用于声明，当然，你也可以使用 `noreturn` 属性作为函数或者方法的*类型*。


####assignment

此属性可用于修饰重载复合赋值运算符的函数。这个重载复合赋值运算符的函数必须用 `inout` 来标记初始输入参数。`assignment` 属性示例参见[复合赋值运算符](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AdvancedOperators.html#//apple_ref/doc/uid/TP40014097-CH27-XID_45)。

####class_protocol

此属性可用于定义类类型协议。

如果你使用 `objc` 属性的协议， 那么这个协议就隐式含有 `class_protocol` 属性，你无需显式标记 `class_protocol` 属性。

####exported

此属性可用于内部声明，可以将当前模块的内部模块、子模块或者声明暴露给外部其他模块。如果另一个模块引用了当前模块，那么这个模块就可以访问当前模块中暴露出来的部分。

####final

此属性可用于修饰类或者类的属性、方法或成员下标运算符。用于一个类的时候，表明这个类是不能被继承的。用于类的属性、方法或成员下标运算符的时候，表明这个类的这些成员函数不能在任何子类中重写。

####lazy

此属性可用于修饰类或者结构体中的存储变量属性，表明这个属性在第一次被访问时，其初始值最多只能被计算和存储一次。 `lazy` 属性示例参见[惰性存储属性](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html#//apple_ref/doc/uid/TP40014097-CH14-XID_326)。

####noreturn

此属性用于函数或者方法的声明，表明其函数或者方法相应的类型`T`是`@noreturn T`。当一个函数或者方法无需返回其调用者时，你可以用这个属性来修饰其类型。

你可以重写没有标示`noreturn`属性的函数或者方法。也就是说，你不能够重写有`noreturn`属性的函数或者方法。当你实现此类型的协议方法时，也有相似的规则。

####NSCopying

此属性可用于修饰类中的存储变量属性。被修饰的这个属性的赋值函数是由这个属性值的*拷贝*组成－由`copyWithZone`方法返回－而不是这个属性本身的值。此属性类型必须符合`NSCopying`协议。

`NSCopying`属性类似于Objective-C中的`copy`属性。

####NSManaged

用于修饰类中的存储变量属性，此类继承于`NSManagedObject`，表明这个属性的存储和实现是由Core Data基于相关的实体描述实时动态提供的。

####objc

此属性可用于能用Objective-C表示的任何声明中－例如，非嵌套的类、协议、类和协议的属性和方法(包括取值函数和赋值函数)、初始化函数、析构函数以及下标运算符。`objc`属性告诉编译器此声明在Objective-C代码中可用。
 
如果你使用`objc`属性修饰类或者协议，它会显式的应用于这个类或者协议中的所有成员。当一个类继承于标注`objc`属性的另一类时，编译器会显式的为这个类添加`objc`属性。标注`objc`属性的协议不能够继承于不含有`objc`属性的协议。

`objc`属性可以接受由标识符组成的单个属性参数。当你希望暴露给Objective-C的部分是一个不同的名字时，你可以使用`objc`属性。你可以使用这个参数去命名类、协议、方法、取值函数、赋值函数以及初始化函数。下面的示例就是`ExampleClass`的`enabled`属性的取值函数，它暴露给Objective-C代码的是`isEnabled`，而不是这个属性的原名。

```python
@objc
class ExampleClass {
    var enabled: Bool {
    @objc(isEnabled) get {
        // Return the appropriate value
    }
    }
}
```

####optional

此属性可用于协议的属性、方法或者成员下标运算符，用来表明实现那些成员函数时，此类型的不是必需实现的。

`optional`属性只能用于标注`objc`属性的协议。因此，包含可选成员的协议只有类类型适用。更多的关于怎样使用`optional`属性，以及怎样访问可选协议成员的指导－例如，当你不确定它们是否实现了此类型时－参见[可选协议需求](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-XID_365)。

####required

此属性用于类的指定或者便利初始化函数时，表明这个类的每个子类都必须实现这个初始化函数。

需求的指定初始化函数必须被显式的包含。当子类直接实现所有超类的指定初始化函数时(或者子类使用便利初始化函数重写了指定初始化函数时)，需求的便利初始化函数必须被显式的包含或者继承。

####使用Interface Builder声明属性

Interface Builder属性就是使用Interface Builder声明属性以与Xcode同步。Swift提供了如下几种Interface Builder属性：`IBAction`，`IBdesignable`，`IBInspectable`以及`IBOutlet`。这些属性理论上与Objective-C中相应的属性一样。

`IBOutlet`和`IBInspectable`属性可用于类的属性声明，`IBAction`属性可用于类的方法声明，`IBDesignable`属性可用于类的声明。

##类型属性

类型属性可以只用于类型。当然`noreturn`属性也可以用于函数或者方法的声明。

####auto_closure

此属性用于延迟表达式的赋值计算，将表达式自动封装成一个无参数的闭包。此属性还可作为函数或者方法的类型，此类型无参数并且其返回的是表达式类型。`auto_closure`属性示例参见[函数类型](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Types.html#//apple_ref/doc/uid/TP40014097-CH31-XID_1005)。

####noreturn

此属性用于函数或者方法时表明此函数或者方法无返回值。你也可以用此属性标记函数或者方法的声明，以表明其函数或者方法相应的类型`T`是`@noreturn T`。


>属性语法

>*attribute* → @­*[attribute-name](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/attribute-argument-clause) [attribute-argument-clause](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/attribute-argument-clause)* opt

>*attribute-name* → *[identifier](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/LexicalStructure.html#//apple_ref/swift/grammar/identifier)*

>*attribute-argument-clause* → (*[balanced-tokens­](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/balanced-tokens)* opt)

>*attributes* → *[attribute­](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/balanced-tokens) [attributes](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/balanced-tokens)­* opt­

>*balanced-tokens* → *[balanced-token](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/balanced-tokens) ­[balanced-tokens­](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/balanced-tokens)* opt­

>*balanced-token* → (*­[balanced-tokens­](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/balanced-tokens)* opt­)­

>*balanced-token* → [*[balanced-tokens­](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/balanced-tokens)* opt­]­

>*balanced-token* → {*[balanced-tokens­](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html#//apple_ref/swift/grammar/balanced-tokens)* opt­­}­

>*balanced-token* → 任意标识符，关键字，常量，或运算符

>*balanced-token* → 任意的标点符号 (­， )­， [­， ]­， {­， 或 }­
