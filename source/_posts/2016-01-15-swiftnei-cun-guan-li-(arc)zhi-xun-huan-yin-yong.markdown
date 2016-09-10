---
layout: post
title: "Swift内存管理（ARC）之循环引用"
date: 2016-01-15 01:37:31 +0800
comments: true
tags: [iOS, Xcode, Swift, ARC, 内存管理, 循环引用, Block, Delegate]
keywords: Swift, ARC, 循环引用, 内存管理
categories: Swift
---
Swift中是采用自动引用计数(ARC，AutomaticReferenceCounting)机制来对内存进行管理的。

#### 一、简述ARC如何工作：
每当你每创建一个新的对象，它便会分配一块内存来存储该对象的相关信息。当你不再需要这个对象的时候，它就会自动释放这个对象，避免它再占用内存空间。当然，如果该对象只要至少存在一个引用就不会被释放。另外，你如果访问了一个已经被释放的对象，则很有可能会出现崩溃（野指针）。
拿《The Swift Programming Language》里面的例子来套一下：

```
//创建一个Cat类，自带常量name属性
class Cat {
    let name: String
    init(name: String) {
    //初始化name属性
        self.name = name;
        print("\(name) 对象已经被初始化")
    }
    
    deinit {
        print("\(name) 对象释放成功！")
    }
}

```
<!--more-->
接下来定义三个变量类型为:Cat?用来对一个新的Cat对象建立多个引用。因为变量是一个可选类型（是Cat?而不是Cat）， 他们会自动给变量初始化为nil，所有当前它不需要引用一个Cat对象。

```
var cat1: Cat?
var cat2: Cat?
var cat3: Cat?
```

创建一个新的Cat对象，并对之前定义的三个变量进行赋值操作：

```
cat1 = Cat(name: "jack")
//输出：jack 对象已经被初始化

```
当你调用Cat类构造器，它会输出："jack 对象已经被初始化"。这就代表已经完成了对象初始化操作。接下来，一个新的Cat对象被赋值给了 cat1变量，从而建立了cat1对新Cat实例对象的强引用。因此，它存在了至少一个强引用。这个新的Cat对象就不会被释放。
如果你把同一个Cat实例对象赋值给另外两个变量，它则又多建立了两个强引用。

```
//通过赋值对Cat实例对象建立强引用
cat2 = cat1
cat3 = cat1
```

此时这个Cat实例对象已经有三个强引用了。
如果你用把其中两个变量赋值为nil的方式来打破这些强引用，对象是不会被释放的。因为它还存在一个强引用。

```
//通过把置nil来打破cat1,cat2对Cat实例对象的强引用
cat1 = nil
cat2 = nil
```
只有直到对象所有的引用被打破，ARC才会释放该对象

```
//打破最后一个强引用
cat3 = nil
//输出：jack 对象释放成功！
```
当最后一个强引用被打破后，这个Cat对象被成功释放，输出：jack 对象释放成功！

#### 二、循环引用

1、什么是循环引用？
简单的来说就是两个对象互相持有对方，使对方处于活跃状态不被释放，从而导致了循环引用。套个例子：

```
//创建一个Cat类，拥有一个String类型的name属性与一个可选类型的dog属性。
class Cat {
    let name: String
    init(name: String) {
        self.name = name;
        print("\(name) 对象已经被初始化")
    }
    var dog: Dog?
    
    deinit {
        print("\(name) 对象释放成功！")
    }
}

//创建一个Dog类，拥有一个String类型的type属性与一个可选类型的jack属性。
class Dog {
    let type: String
    init(type: String) {
        self.type = type
        print("\(type) 对象已经被初始化")
    }
    
    var cat: Cat?
    deinit {
        print("\(type) 对象释放成功")
    }
}
```
定义两个变量并初始化

```
var jack: Cat?
var tom: Dog?

jack = Cat(name: "jack")
//print:jack 对象已经被初始化
tom = Dog(type: "red")
//print:red 对象已经被初始化
```
建立强引用

```
jack!.dog = tom
tom!.cat = jack
```
通过把对象置nil打破强引用

```
jack = nil
tom = nil
```
这个地方Cat对象和Dog对象都不会被释放，因为他们两个之间还存在强引用。

* 解决循环引用办法
	Swift提供了两种方法来解决循环引用问题：
	
	（1）弱引用（weak）
	
	（2）无主引用（unowned）
	
声明变量或属性时，在前面加上"weak"关键词表示它是一个弱引用。如果确定访问属性或函数的时候该对象不会被释放，也可以用无主引用（unowned）来取代。unowned跟Objective-C中的unsafe_unretained类似。它与weak的区别在于weak在引用的对象被释放后，会自动置nil。所以它必须声明成可选类型（？）。而unowned在引用对象被释放后不会自动置nil(因为非可选类型的变量不能被设置成nil)，而是保留对原有对象的无效引用，一旦对象被释放，再访问该对象的属性或函数就会造成崩溃。官方建议是当你在可以确定访问属性或函数所属对象不会被释放时，使用unowned。否则，还是使用weak吧！

解决上面代码的循环引用问题，可做如下修改：

```
class Dog {
    let type: String
    init(type: String) {
        self.type = type
        print("\(type) 对象已经被初始化")
    }
    
    weak var jack: Cat
    deinit {
        print("\(type) 对象释放成功")
    }
}

```

```
jack = nil
tom = nil
```

2、常见的循环引用问题：

（1）delegate
在项目中我们经常会用到代理模式，在Objective-C中我们一般在delegate前面加上weak关键词，表示它是一个弱引用。从而达到打破循环引用的效果。

```
//在前面加上@objc是指定这里的代码属于Objective-C的代码，因为Swift中协议可以用于class、struct、enum。对于基本数据类型，它不是对象，没有引用计数这个概念，因此无法使用weak关键词。  Objective-C中的协议只适用于类，所以这里需要加上@objc。
@objc protocol AClassDelegate {
    func wash()
}

class A {
    weak var delegate: AClassDelegate!
    func cry() {
        
    }
    func play() {
        delegate.wash()
    }
}

class B: AClassDelegate {
    
    func feed() {
        let nurse = A()
        nurse.delegate = self
        nurse.cry()
    }
    
    @objc func wash() {
        print("begin wash and sleep")
    }
}
```

（2）Block(闭包)中的循环引用
Block(闭包)的循环引用在于它会持有所有引用的元素。比如我们在Block(闭包)中访问了self，那么它就持有了self。这样一来就形成了一个环：self持有Block(闭包)，Block(闭包)持有self。下面我们举个栗子加以说明：类A中有个闭包，我们在闭包中访问了self的name属性。

```
class A {
    let name: String
    lazy var wash: Void ->Void = {
        var name = self.name + " nurse"
        print("wash room is \(name)")
    }
    
    init(name: String) {
        self.name = name
        print("\(self.name) 创建了")
    }
    
    deinit {
        print("\(self.name) 释放了")
    }
}

class B {
    func play() {
        var nurse: A?
        nurse = A(name: "jack")
        nurse?.wash()
    }
}

var baby: B?
baby = B()
baby?.play()

输出结果：
jack 创建了
wash room is jack nurse
```

 因为wash是self的一个属性，所以wash被self持有。这个时候我们又在闭包中访问了self的name属性，所以它又在闭包中持有了self。这样便导致了循环引用。为了解决闭包中的循环引用问题，Swift中提供了一个叫”闭包捕获列表”的解决方案。
 
 * 定义一个捕获列表
 
 捕获列表中的每一项都是一个用weak或 unowned关键字与一个实例对象的引用（如self）或者是一个用初始化值的变量（如delegate = self.delegate!）搭配成对，配对之间用逗号隔开，最后用方括号括起来。如下所示：
 
 ```
 
  lazy var someClosure:(Int, String) -> String = {
        [unowned self, weak delegate = self.delegate!] (index: Int, stringToProcess: String) -> String in
        
    }
    
 ```
 
 如果闭包中没有指定参数列表或者返回类型，可直接把捕获列表放置在闭包开始的地方（花括号后面）然后紧跟着是"in"关键词，示例代码如下：
 
  ```
  lazy var someClosure:Void -> String = {
        [unowned self, weak delegate = self.delegate!]  in
        
    }
 ```
 
 
 * 解决Block(闭包)中的循环引用，ClassA中的Block(闭包)代码可做以下修改：

```
lazy var wash:Void ->Void = { [weak self] in
//这个地方使用self!.name而不是self.name。是因为weak修饰，self有可能为nil，如果是unowned修饰，这个地方应该是self.name。因为unowned修饰的self永远不可能为空。
        var name = self!.name + " nurse"
        print("wash room is \(name)")
    }

 输出结果：
jack 创建了
wash room is jack nurse
jack 释放了
```

#### 参考资料及文章
* <a herf="https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/">《The Swift Programming Language》</a>
*  <a href="http://swifter.tips/retain-cycle/">内存管理，WEAK 和 UNOWNED </a>
* <a href="http://skyfly.xyz/2015/12/18/Swift/Swift%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0-%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86/">Swift学习笔记-内存管理</a>


