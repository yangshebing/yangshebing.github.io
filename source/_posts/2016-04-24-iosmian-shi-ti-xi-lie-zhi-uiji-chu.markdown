---
layout: post
title: "iOS面试题系列之UI相关"
date: 2016-04-24 23:58:39 +0800
comments: true
tags: [iOS, xcode, iOS面试]
keywords: ios开发, iOS面试
categories: Others 
---

**1、	简述app启动周期。**

打开应用程序，它先会执行main函数，再执行UIApplicationMain函数。初始化
UIApplication，设置代理对象，开启事件循环，调用代理方法进行事件处理。


首先会调用application:didFinishLaunchingWithOptions:代理方法。

<!--more-->
接下来会调用applicationDidBecomeActive:代理方法。监听系统事件，结束程序。当程序正常退出UIApplicationMain函数才会返回。


监听系统事件：

锁屏后app会调用：applicationWillResignActive:代理方法。然后接着调用
applicationDidEnterBackground:方法。

解锁后app会调用：applicationWillEnterForeground:代理方法。接着调用
applicationDidBecomeActive代理方法。

杀死进程会调用：applicationWillTerminate:代理方法。

接听电话：先调用applicationWillResignActive:代理方法。挂断电话后会直接进入
应用调：applicationDidBecomeActive:代理方法。

出现内存警告会调用：applicationDidReceiveMemoryWarning:代理方法


**2、	简单的说说UIApplication的作用及继承关系。**

（1）UIApplication是继承UIResponder的。UIApplication是整个应用程序的核
心。每一个程序在运行期必须有UIApplication(或子类)的一个实例（有且只有一
个），通过[UIApplication shareApplication]可以得到这个单例实例。

（2）UIApplication帮助管理应用程序的生命周期，通过delegate来履行任务。

（3）UIApplication可以接收事件，把所有用户事件都放入队列，逐个处理。它会发送
当前事件给一个合适的目标控件进行处理。他还将部分事件转给delegate对象来处理。
delegate可以处理的事件包括：应用程序的生命周期事件（如程序启动与关闭）、系统事
件（如来电）。


**3、	简述推送消息原理。**

（1）应用程序注册接收远程通知的时候，系统会发送请求到APNS服务器。APNS服务器收
到请求后会根据请求所带的key值生成一个独一无二的deviceToken。然后APNS服务器会把deviceToken包装成NSData对象发送到对应请求的App上。接下来APP把deviceToken发送给我们自己的服务器（Provider）。服务器接收到deviceToken后进行存储等相关处理。服务器会把推送给用户的消息发送至APNS服务器，最后APNS服务器再发送通知给app。

（2）有关于deviceToken变化问题：重新安装或卸载应用程序，deviceToken不会发生变化。系统升级deviceToken可能会发生变化。抹掉所有内容和设置，重置设备后，deviceToken会变化

（3）注册远程通知方法注册远程通知：一般在app启动完成的时候注册远程通知，注册方法一般在didFinishLaunchingWithOptions：代理方法中处理注册远程通知回调方法：注册成功会回调didRegisterForRometoNotificationsWithDeviceToken:其中的deviceToken参数就是APNS服务器返回的token。注册失败会回调didFailToRegisterForRemoteNotificationsWithError:

（4）处理接收到的远程通知消息程序启动时，app从Terminate状态进入Foreground状态时，会根据didFinishLaunchingWithOptions:代理方法中的luanchOptions参数来获取userInfo。判断是否有推送消息。方式如下：   // userInfo为收到远程通知的内容NSDictionary*userInfo=launchOptions[UIApplicationLaunchOptionsRemoteNotificationKey];if (userInfo) {   // 有推送的消息，处理推送的消息  }如果app处于Background状态时，只有用户点击了通知消息才会调用didReceiveRemoteNotification:，如果当前app处于Foreground状态，会直接调用该方法。


**4、	简单说下UITableView、UIButton、UIWindow控件的继承关系。**

（1）UITableView的继承关系：UITableView继承UIScrollView。UIScrollView继承UIView。UIView继承UIResponder。UIResponder继承NSObject。

（2）UIButton的继承关系：UIButton继承UIControl，UIControl继承UIView。UIView继承UIResponder，UIResponder继承NSObject。

（3）UIWindow继承UIView。


**5、	UIView与UIButton有什么区别？UISwitch呢？**

UIView继承自UIResponder，它能响应某个动作。UIButton继承UIControl。它不仅可以响应某个动作，还能为某个对象添加对应的动作加以响应。UIButton也能实现UISwitch的功能，它们的区别是分发的事件不一样。


**6、	简述UIControl的继承关系，它用来做什么？**

UIControl继承UIView。UIView继承UIResponder，UIResponder继承NSObject。它能为某个特定对象添加事件。能进行事件分发。


**7、	简述UIViewController中view的生命周期。**


先调用loadView方法，再调用viewDidLoad方法，调用viewWillAppear方法，调用viewWillLayoutSubviews方法，调用viewDidLayoutSubviews方法，再调用viewDidApper方法。控制器消失的时候先调用viewWillDisappear方法，再调用viewDidDisapper方法。最后调用dealloc方法。当出现内存警告的时候会调用didReceiveMemoryWarning方法。


**8、	简述UITableView的代理方法调用顺序。**

先调用numberOfSectionsInTableView:方法，

numberOfRowsInSection:

heightForRowAtIndexPath:

cellForRowAtIndexPath:



**9、	你在用CollectionView的时候有没有遇到什么问题？问题是怎么解决的？**

刷新的时候会闪烁。在刷新的时候会默认附加一个隐式的fade动画。可以通过取消动画来达到取消闪烁的目的。



**10、	简述UIResponder类的作用。**

UIResponder类主要是为那些需要响应并处理事件的对象定义了一组接口。这些事件包括：触摸事件、运动事件、远程控制事件（如耳机控制音视频播放）。当用户触发某一事件时，UIKit会创建一个UIEvent事件对象，事件对象会加入到一个FIFO先进先出的队列中。UIApplication对象处理事件时，会从队列头部取出一个事件对象进行分发。


**11、	如何监听View的触摸事件？事件如何传递？简述视图的响应者链。**

可以通过View类的touchesBegan、touchesMoved、touchesEnded、touchesCancelled方法监听视图的触摸。
当触摸一个视图对象时，系统会捕捉此事件，并给这个事件创建一个UIEvent对象，将此对象加入当前应用程序的事件队列中，然后由UIApplication对象从队列中取出来进行事件分发，首先会分发给UIWindow对象，然后由UIWindow对象分发给触摸的视图对象，即第一响应者对象。

响应者链：当事件交给第一响应者处理，如果第一响应者不处理，事件就会沿着响应者链向上传递，交给下一个响应者。一般来说，第一响应者是一个视图对象或者是其子类对象，当其被触摸后事件被交由它处理，如果它不处理，事件就会被传递给它的视图控制器对象，然后是它的父视图对象，以此类推，直到顶层视图。接下来会沿着顶层视图到UIWindow对象，再到UIApplication对象。如果整个过程都没有响应这个事件，该事件就会被丢弃，一般情况下，在响应者链中只要有对象处理事件，事件就会停止传递。有时候可以在视图的响应方法中根据一些条件判断来决定是否需要继续传递事件。

**12、	CALayer与UIView有什么区别？**

（1）UIView是iOS系统中界面元素的继承。所有的界面元素都继承自它，他本身完全是由CoreAnimation来实现的。它真正的绘图部分，是由一个叫CALayer的类来管理。UIView本身更像是一个CALayer的管理器，访问它的跟绘图和跟坐标有关的属性，如frame,bounds等，实际上内部都是在访问它所包含的CALayer的相关属性。


（2）UIView有个layer属性，可以返回它的主CALayer实例，UIView有一个layerClass方法，返回主layer所使用的类，UIView的子类，可以通过重载这个方法，来让UIView使用不同的CALayer来显示。

（3）UIView的CALayer类似UIView的子View树形结构，也可以向它的layer上添加子layer。

（4）CALayer坐标系统与UIView有点不一样，它多了一个anchorPoint的属性。

（5）UIView的layer树形在系统内部，被系统维护着三份copy。逻辑树，动画树，显示树。

（6）CALayer默认修改属性支持隐式动画

（7）View可以接受并处理事件，Layer不可以。


**13、	如何优化UITableView?**

（1）复用单元格

（2）使用不透明的视图，单元格中少使用动画。

（3）图片使用异步加载，设置图片加载的并发数。

（4）滑动时不加载图片，停止滑动时再加载。

（5）图片和文字可以直接drawRect

（6）如果cell是动态行高，计算缓存单元格高度

（7）尽量少reloadData，只reloadRowsAtIndexPaths

（8）cell高度固定直接用rowHeight属性设置高度。

**14、	简述加载到控制器上的根视图创建与加载时机。**


视图控制器的根视图加载是在UIViewController的loadView方法中。当视图控制中的loadView方法被调用时，会先通过NSBundle加载，通过控制器的类名作为xib文件名加载xib文件，如果存在，加载View为根视图，如果不存在，则自动创建一个View作为根视图。



**15、	简述UITableView中cell的复用机制。**

UITableView会创建屏幕显示单元格个数（N）+1个单元格，当滑动到第N+2个单元格时就会复用第1个单元格。UITableView中还有单元格复用池的概念。当tableView调用初始化单元格协议方法获取单元格时，它会根据你设定的reuse identifier先去复用池中查找可用的复用单元格，找到则复用该单元格，没找到这创建一个新的单元格对象。


**本文内容中部分来自网络，后续会持续更新完善。欢迎一起学习交流！**

**如需转载，请注明出处**