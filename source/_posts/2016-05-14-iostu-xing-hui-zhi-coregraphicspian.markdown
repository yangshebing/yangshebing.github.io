---
layout: post
title: "iOS图形绘制CoreGraphics篇"
date: 2016-05-14 21:57:43 +0800
comments: true
tags: [iOS9, Xcode7.3, Xcode, CoreGraphics, Quartz2D]
keywords: CoreGraphics, Quartz2D, 绘制图形
categories: Objective-C
---

#### 1、绘制直线

```

//获取当前上下文
    CGContextRef contextRef = UIGraphicsGetCurrentContext();
    UIColor *color = [UIColor colorWithRed:0.5 green:0.35 blue:0.8 alpha:0.7];
    //设置线条颜色
//    CGContextSetStrokeColor(contextRef, CGColorGetComponents([UIColor redColor].CGColor));
    //官方推荐首选这个API
    CGContextSetStrokeColorWithColor(contextRef, [UIColor redColor].CGColor);
    //设置线条颜色
    CGContextSetLineWidth(contextRef, 5);
    //设置线条端点(不带端点)
    CGContextSetLineCap(contextRef, kCGLineCapButt);
    //设置线条绘制的起始坐标点
    CGContextMoveToPoint(contextRef, 50, 20);
    //结束点
    CGContextAddLineToPoint(contextRef, 100, 20);
    //绘制线条路径
    CGContextStrokePath(contextRef);
    
```
<!--more-->
**这里插播一个从指定UIColor对象中获取RGB值得方式，上面设置线条颜色方法：CGContextSetStrokeColor第二个参数有用到。**

```

    //它是返回一个存储RGB与Alpha值得数组
    const CGFloat *componets =  CGColorGetComponents(color.CGColor);
    CGFloat r,g,b,a;
    //R值
    r = componets[0];
    //G值
    g = componets[1];
    //B值
    b = componets[2];
    //Alpha值
    a = componets[3];

    

```

**不信，此处看客可以打印RGB、Alpha值试试！**

#### 2、绘制矩形

```
  //设置线条颜色
    CGContextSetStrokeColorWithColor(contextRef, [UIColor blueColor].CGColor);
    CGContextSetLineWidth(contextRef, 2);
    //绘制矩形边框
    CGContextStrokeRect(contextRef, CGRectMake(50, 30, 80, 80));
//    CGContextSetFillColor(contextRef, CGColorGetComponents([UIColor cyanColor].CGColor));
    //绘制实心矩形
    CGContextSetFillColorWithColor(contextRef, [UIColor cyanColor].CGColor);
    CGContextFillRect(contextRef, CGRectMake(50, 120, 80, 80));

    //绘制带边框的矩形
    CGContextSetStrokeColorWithColor(contextRef, [UIColor brownColor].CGColor);
    CGContextSetFillColorWithColor(contextRef, [UIColor magentaColor].CGColor);
    CGContextSetLineWidth(contextRef, 2);
    CGContextAddRect(contextRef, CGRectMake(50, 210, 80, 80));
    
    //绘制路径方式：
    /*CGPathDrawingMode:
     *kCGPathFill,  //用非零绕数规则填充（什么鬼？）
     *kCGPathEOFill,    //用奇偶规则填充不带边框
     *kCGPathStroke,    //描线
     *kCGPathFillStroke,    //描线填充
     *kCGPathEOFillStroke   //奇偶填充带边框
     */
    CGContextDrawPath(contextRef, kCGPathFillStroke);
    
    
```

 **这里介绍一下设置线型为虚线的方式（虚线绘制方式）**
 

```

/*
*phase:    虚线开始部分绘制大小
*const CGFloat *lengths:   虚线排列的大小规则数组，数组的值用来指定绘制与未绘制部分的长度（间隔）
*例如{2，3}指交替绘制2个单元长度的线段与3个单元长度未绘制长度线段（间隙）
*例子示意图：      --   --   --   --   --   --
*{1，3，4，2}绘制一个单元长度线段，间隔3个单元长度空隙，接着绘制4个长度的线段，间隔2个单元长度的空隙
*例子示意图：      -   ----  -   ----  -   ----  -   ----  -
*size_t count: 虚线排列数组中参数值有用个数
*/
 
const CGFloat spaceArr[] = {6, 4, 5, 7};
CGContextSetLineDash(contextRef, 0, spaceArr, 4);

```

 
#### 3、绘制贝塞尔曲线
 
 
```
 
CGContextSetStrokeColorWithColor(contextRef, [UIColor redColor].CGColor);
CGContextSetFillColorWithColor(contextRef, [UIColor grayColor].CGColor);
CGContextSetLineWidth(contextRef, 1);
CGContextMoveToPoint(contextRef, 50, 300);
//添加三个point坐标。绘制一条曲线。
//CGContextAddCurveToPoint(contextRef, 100, 320, 150, 300, 200, 360);
//添加二次曲线
CGContextAddQuadCurveToPoint(contextRef, 250, 380, 290, 280);
CGContextDrawPath(contextRef, kCGPathStroke);
 
 
```
 
 
#### 4、绘制圆弧
 
```

CGContextAddArc(contextRef, 50, 390, 20.0, -10 * M_PI / 180, 60 * M_PI / 180, YES);
CGContextDrawPath(contextRef, kCGPathStroke);


```


#### 5、绘制圆形


```

//绘制圆形:方式一
CGContextSetStrokeColorWithColor(contextRef, [UIColor blueColor].CGColor);
CGContextSetFillColorWithColor(contextRef, [UIColor redColor].CGColor);
CGContextAddArc(contextRef, 50, 480, 20.0, -180 * M_PI / 180, 180 * M_PI / 180, NO);
CGContextDrawPath(contextRef, kCGPathFillStroke);
//绘制圆形：方式二
CGContextAddEllipseInRect(contextRef, CGRectMake(0, 500, 80, 80));
CGContextDrawPath(contextRef, kCGPathFillStroke);
    
    
```
  
    
#### 6、绘制多边型（六边形）

```

//绘制多边形
CGContextMoveToPoint(contextRef, 180, 500);
CGContextAddLineToPoint(contextRef, 100, 550);
CGContextAddLineToPoint(contextRef, 180, 600);
CGContextAddLineToPoint(contextRef, 260, 600);
CGContextAddLineToPoint(contextRef, 340, 550);
CGContextAddLineToPoint(contextRef, 260, 500);
CGContextAddLineToPoint(contextRef, 180, 500);
CGContextDrawPath(contextRef, kCGPathFillStroke);
//最后关闭它！
CGContextClosePath(contextRef);

```
 
**这里就不演示绘制图表了，其实过程都差不多，只是换一种绘制方法而已！
关于简单的图表绘制请参考上一篇，详细请点击：<a href="http://yangshebing.github.io/blog/2016/05/14/iostu-xing-hui-zhi-uibezierpathpian/">iOS图形绘制UIBezierPath篇</a>**

**文中代码效果图：**

![](http://ww1.sinaimg.cn/mw690/7f266405jw1f3va3hd4i9j20ku12aq57.jpg)
**不要问我为什么画的这么丑，因为我是粗糙的程序员！随性**

#### 参考博客地址： 
* <a href = "http://ghui.me/post/2016/04/ios-core-graphics/">iOS绘图之用Core Graphics框架绘制常见图形（这篇是用Swift语言写的）</a>

转载请注明出处

    
    