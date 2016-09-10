---
layout: post
title: "Swift2.2新特性"
date: 2016-05-09 23:09:48 +0800
comments: true
tags: [iOS, Xcode, Swift, Swift2.2]
keywords: Swift, Swift2.2, Swift2.2新特性
categories: Swift
---

#### 1、编译期Swift版本检测

```

#if swift(>=3.0)
    print("Running Swift 3.0 or later")
#else
    print("Running Swift 2.2 or earlier")
#endif

```

它和Swift2中介绍过的`#available`表达式不同,#available表达式是运行时检查,`#if swift(>=3.0)... #else... #endif`是编译期检查。


**一个警告:这个特性这次不可用,因为Swift2.1编译器并不知道`#if swift(>=2.2)`意味着什么。然而,一旦Swift3.0以及未来所有版本可用,Swift编译期版本检查将会是一个非常有用的工具。**
<!--more-->

#### 2、编译期选择器检查

在Swift2.1,下面这段代码完全没有问题:

```

override func viewDidLoad() {
  super.viewDidLoad()
  navigationItem.rightBarButtonItem = UIBarButtonItem(barButtonSystemItem: .Add, target: self, action: "addNewFireflyRefernce")
}

func addNewFireflyReference() {
   gratuitousReferences.append("We should start dealing in black-market beagles.")
}

```


上面这段代码在应用程序中会崩溃,因为导航栏按钮调用了一个方法`addNewFireflyRefernce`,方法名中的`Refrence`少了一个字母”e”.这类简单的单词拼写错误很容易造成bug,所以在Swift2.2中弃用了使用字符串作为选择器。使用新语法:`#selector`来取代它。

使用`#selector`将会在编译期检查你的代码,确定你想调用的方法真实存在。甚至更好的是,如果方法不存在,你会得到一个编译错误。

下面是通过`#selector`来重写之前的代码:

```
override func viewDidLoad() {
        super.viewDidLoad()
        navigationItem.rightBarButtonItem =
        UIBarButtonItem(barButtonSystemItem: .Add, target: self, action: #selector(addNewFireflyReference))
}

func addNewFireflyReference() {
  gratuitousReferences.append("Curse your sudden but inevitable betrayal!")
}

```

这里需要注意的:

Objective-C Selectors(OC选择器)

一个OC选择器是一种特指OC方法名的类型。在Swift里,OC选择器是通过Selector结构来表示的.你可以使用#selector表达式来构造一个选择器。例如`let mySelector = #selector(MyViewController.tappedButton)`,用一个直接引用一个OC方法作为子表达式。一个OC方法引用能被括号括起来,它能使用”as”操作符消除它与重载之间的歧义。例如`let anotherSelector = #selector(((UIView.insertSubview(_:at:)) as(UIView) ->(UIView, Int) ->Void))`。

示例代码如下:为按钮添加事件

```

import UIKit
class MyViewController: UIViewController {
    let myButton = UIButton(frame: CGRect(x: 0, y: 0, width: 100, height: 50))

    override init?(nibName nibNameOrNil: String?, bundle nibBundleOrNil: NSBundle?) {
        super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)
        let action = #selector(MyViewController.tappedButton)
        myButton.addTarget(self, action: action, forControlEvents: .TouchUpInside)
    }

    func tappedButton(sender: UIButton!) {
        print("tapped button")
    }

    required init?(coder: NSCoder) {
        super.init(coder: coder)
    }
}

```

<a href="https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html">官方文档入口</a>

#### 3、更多的关键词作为参数标签

Swift中有很多关键词，像`class`、`fund`、`let`和`public`这些都有其特殊意义，所以不能作为标识符使用。Swift中是允许你使用关键词作为参数标签的。如果你像下面代码一样把它们放在引号里面：

```

func visitCity(name: String, `in` state: String) {
   print("I'm going to visit \(name) in \(state)")
}

visitCity("Nashville", `in`: "Tennessee")

```

Swift2.2中除了`inout`、`var`、`let`之外，任何关键字都能被用作一个参数标签。如果你在代码中把关键字放在引号里，你将会得到一个Xcode的Fix-it警告，点击该警告便可自动移除引号。去掉引号的代码很可能是这样的：

```

func visitCity(name: String, in state: String) {
  print("I'm going to visit \(name) in \(state)")
}

visitCity("Nashville", in: "Tennessee")

```

#### 4、内置元组比较


Swift2.2介绍了比较两个元组相等的能力。这意味着它将会检查元组中的每一个元素与另一个元组中的元素是否匹配，如果所有元素匹配，返回true。


举个例子，下面的代码将会打印“不匹配”：

```

let singer = (first: "Taylor", last: "Swift")
let alien = (first: "Justin", last: "Bieber")

if singer == alien {
   print("They match! That explains why you never see them together…")
} else {
   print("No match.")
}

```

**Swift2.2元组的比较元素个数不能超过6个。**


**警告：Swift2.2在检查平等性时将会忽略你的元素名，因此下面代码中的“singer”与”bird”会被认为是相等的：**

```

let singer = (first: "Taylor", last: "Swift")
let bird = (name: "Taylor", breed: "Swift")

if singer == bird {
   print("This explains why she sings so well.")
} else {
   print("No match.")
}

```

#### 5、元组的splat语法被弃用

在Swift2.1之前，有可能会使用一个元组来填充一个函数的参数。因此，如果你有一个函数带了两个参数，你能用一个含有两个元素的元组来调用它，只要元组有正确的类型和元素名称，例如：

```

func describePerson(name: String, age: Int) {
    print("\(name) is \(age) years old")
}
let person = ("Malcolm Reynolds", age: 49)
describePerson(person)
        
```

这种语法，被称为“元组splat语法”。这是不符合Swift的自身描述的语言习惯及可读性的风格。因此在Swift2.2中被弃用。

#### 6、C语言风格的for循环被弃用


即使Swift有很多种循环语法可选择，但是C语言风格的for循环仍然是语言中的一部分，偶尔会被使用。例如：

```

for var i = 0; i < 10; i++ {
    print(i)
}

```

这种语法在Swift2.2中已经被弃用了，可能在Swift3.0中全部移除。

如果你使用的是Xcode，你可能会得到一个Fix-it的警告，它将会把你C语言风格的for循环转换成现代的Swift。现在我们应该这样写：

```

 for i in 0 ..< 10 {
    print(i)
 }

```

然而，修复功能是被限制的，因此，有些事情需要你自己去做。例如，下面两个循环它将不会帮你修复它：

```

for var i = 10; i > 0; i-- {
   print(i)
}

for var i = 0; i < 10; i += 2 {
   print(i)
}

```

第一种情况，你应该使用"(1...10).reverse()"创建一个反向范围。这个不能像之前一样写成"i in 10…1”,这样写编译没有问题，但是在运行时会崩溃。第二种情况，你应该使用“stride(to:by:)”去累加2。因此，正确重写上面两个循环的方式如下所示：

```

for i in (1...10).reverse() {
    print(i)
}

for i in 0.stride(to: 10, by: 2) {
    print(i)
}

```

#### 7、++和—-运算符被弃用


如果你使用过C语言风格的for循环，肯定对++和——运算符并不陌生。在Swift3.0中，++和--运算符也被弃用了。例如像：`i++`,`i--`,`++i`,`--i`,`i = i++`这些都不能用了。取而代之的是:`i += 1`或`i -= 1`，像上面例子中的代码，Xcode也会提供Fix-it修复警告帮你修复代码。


#### 8、var参数被弃用


Swift2.2之前，如果你想在函数里面修改函数参数，可以把函数参数声明为var,例如：

```

func greet(var name: String) {
    name = name.uppercaseString
    print("Hello, \(name)!")
}

var name = "Taylor"
greet(name)
print("After function, name is \(name)")

```

这样容易困惑，最后print()语句输出到底是“Taylor”还是”TAYLOR”呢？在inout关键字中存在更多的困惑：下面的例子中使用inout而不是使用var，然后添加一个”&”符号。便产生了这段代码：

```

func greet(inout name: String) {
    name = name.uppercaseString
    print("Hello, \(name)!")
}

var name = "Taylor"
greet(&name)
print("After function, name is \(name)")

```

当程序运行时，var的例子与inout例子会产生不同的输出结果，因为改变var参数仅仅应用于函数内部，而改变inout参数则会直接影响到初始值。


所以，在Swift2.2中，通过在函数参数中弃用var关键字来清楚这个困惑。在Swift3.0中将会被移除。如何你还想按照以前的习惯写，可以像下面这样：

```

 func greet(name: String) {
    let uppercaseName = name.uppercaseString
    print("Hello, \(uppercaseName)!")
 }
    
```

#### 9、重命名debug标识符


Swift编译器在调试的时候自动提供了一些有用的符号，`__FILE__`会被替换成当前Swift文件名，`__LINE__`代表行数等等。在Swift2.2中，这些老的标识符被弃用，用`#file`,`#line`,`#column`,和`#function`取代。


下面的例子中列出了新旧语法的区别：

```

 func visitCity(name: String, in state: String) {
        // old - deprecated!
        print("This is on line \(__LINE__) of \(__FUNCTION__)")

        print("I'm going to visit \(name) in \(state)")

        // new - shiny!
        print("This is on line \(#line) of \(#function)")
  }
  
```

和其他的变化一样，Xcode会有Fix-it警告，来帮助你更新正确的代码。

**本文内容来自网络，后续会持续更新完善。以上部分是我自己的学习与理解，欢迎一起学习交流！多多批评与指正！**

**参考资料如下：**

（1）<a href="https://swift.org/blog/swift-2-2-new-features/">New Features in Swift 2.2</a>