#与Objective-C的API交互
**互用性**是让Swift和Objective-C相接合的一种特性，使你能够在一种语言编写的文件中使用另一种语言。当你准备开始把Swift融入到你的开发流程中时，你应该懂得如何利用互用性来重新定义并提高你写Cocoa应用的方案。
互用性很重要的一点就是当你在写Swift代码时使用Objective-C的API接口。当你导入一个Objective-C框架后，你可以使用原生的Swift语法实例化它的Class并且与之交互。
##初始化
为了使用Swift实例化Objective-C的Class，你应该使用Swift语法调用它的一个初始化器。当Objective-C的`init`方法变化到Swift，他们用Swift初始化语法呈现。"init"前缀被截断当作一个关键字，用来表明该方法是初始化方法。那些以“initWith”开头的`init`方法，“With”也会被去除。从“init”或者“initWith”中分离出来的这部分方法名首字母变成小写，并且被当做是第一个参数的参数名。其余的每一部分方法名依次变味参数名。这些方法名都在圆括号中被调用。

举个例子，你在使用Objective-C时会这样做：

```Objective-C
UITableView *myTableView = [[UITableView alloc] 
initWithFrame:CGRectZero style:UITableViewStyleGrouped];
```