---
layout: post
title: "iOS图形绘制UIBezierPath篇"
date: 2016-05-14 21:55:19 +0800
comments: true
tags: [iOS9, Xcode7.3, Xcode, UIBezierPath, Quartz2D]
keywords: UIBezierPath, Quartz2D, 绘制图形
categories: Objective-C
---
#### 1、绘制直线

```
UIBezierPath *line = [[UIBezierPath alloc] init];
//设置线宽
line.lineWidth = 3;
[line moveToPoint:CGPointMake(50, 20)];
[line addLineToPoint:CGPointMake(150, 20)];
//设置绘制线条颜色，这个地方需要注意！UIBezierPath本身类中不包含设置颜色的属性，它是通过UIColor来直接设置。
[[UIColor orangeColor] setStroke];
/*
*线条形状
*kCGLineCapButt,   //不带端点
*kCGLineCapRound,  //端点带圆角
*kCGLineCapSquare  //端点是正方形
*/
line.lineCapStyle = kCGLineCapRound;
[line stroke];

```
<!--more-->
#### 2、绘制矩形

* 直接通过UIRectFill绘制（不带边框）


```

[[UIColor blueColor] setFill];
UIRectFill(CGRectMake(50, 30, 100, 40));
    
```

* 使用UIBezierPath绘制（带边框）

```

UIBezierPath *rectangle = [UIBezierPath bezierPathWithRect:CGRectMake(50, 80, 80, 80)];
//边框线宽
rectangle.lineWidth = 3;
//边框线颜色
[[UIColor purpleColor] setStroke];
/*
*线条之间连接点形状
*kCGLineJoinMiter,      //内斜接
*kCGLineJoinRound,     //圆角
*kCGLineJoinBevel      //外斜接
*/
rectangle.lineJoinStyle = kCGLineJoinRound;
//绘制边框
[rectangle stroke];
//设置填充颜色
[[UIColor yellowColor] setFill];
//绘制矩形内部填充部分
[rectangle fill];
    
```

* 直接绘制带圆角的矩形（实心）：这里也是可以添加边框，具体设置同上

```

UIBezierPath *rectangleCorner = [UIBezierPath bezierPathWithRoundedRect:CGRectMake(50, 170, 80, 80) cornerRadius:4];
[[UIColor brownColor] setFill];
[rectangleCorner fill];
    
```

* 绘制部分带圆角的矩形边框（空心）

```

/*
*UIRectCorner:圆角位置
*UIRectCornerTopLeft     = 1 << 0,     //左上角
*UIRectCornerTopRight    = 1 << 1,     //右上角
*UIRectCornerBottomLeft  = 1 << 2,     //左下角
*UIRectCornerBottomRight = 1 << 3,     //右下角
*UIRectCornerAllCorners        //所有角
*
*/
rectangleCorner = [UIBezierPath bezierPathWithRoundedRect:CGRectMake(50, 260, 40, 40) byRoundingCorners:UIRectCornerTopLeft | UIRectCornerTopRight cornerRadii:CGSizeMake(4, 4)];
[rectangleCorner stroke];
    
```

#### 3、绘制圆形（绘制椭圆只需更改高宽即可）


```

UIBezierPath *circle = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(50, 310, 80, 80)];
[[UIColor blueColor] setStroke];
circle.lineWidth = 3;
[circle stroke];
[[UIColor redColor] setFill];
[circle fill];

```


#### 4、绘制多边形:(这是正宗的三角形)

```

UIBezierPath *polygon = [UIBezierPath bezierPath];
[polygon moveToPoint:CGPointMake(50, 390)];
[polygon addLineToPoint:CGPointMake(30, 450)];
[polygon addLineToPoint:CGPointMake(100, 450)];
[[UIColor magentaColor] setFill];
[polygon fill];

```

**啊，三角形不是多边形吗？不调戏各位看客了，还是来个正宗多边形，哈哈哈（六角形）**

```

[polygon moveToPoint:CGPointMake(50, 460)];
[polygon addLineToPoint:CGPointMake(20, 500)];
[polygon addLineToPoint:CGPointMake(50, 540)];
[polygon addLineToPoint:CGPointMake(100, 540)];
[polygon addLineToPoint:CGPointMake(130, 500)];
[polygon addLineToPoint:CGPointMake(100, 460)];
[[UIColor magentaColor] setFill];
[polygon fill];
    
```

#### 5、绘制弧线

```

//clockwise参数表示是否顺时针绘制
//弧长计算公式：
UIBezierPath *arcLine = [UIBezierPath bezierPathWithArcCenter:CGPointMake(160, 20) radius:50 startAngle:0 endAngle:90 *  M_PI / 180 clockwise:YES];
//下面这种创建方式也是一样的
//[arcLine addArcWithCenter:CGPointMake(160, 20) radius:50 startAngle:0 endAngle:90 * M_PI / 180 clockwise:YES];
arcLine.lineWidth = 3;
[[UIColor grayColor] setStroke];
[arcLine stroke];
    
```

#### 6、绘制贝塞尔曲线

```

UIBezierPath *quadLine = [UIBezierPath bezierPath];
[quadLine moveToPoint:CGPointMake(160, 80)];
//带一个控制点
//[quadLine addQuadCurveToPoint:CGPointMake(210, 120) controlPoint:CGPointMake(240, 90)];
//带两个控制点
[quadLine addCurveToPoint:CGPointMake(310, 80) controlPoint1:CGPointMake(210, 150) controlPoint2:CGPointMake(270, 150)];
[[UIColor redColor] setStroke];
[quadLine stroke];
    
```

#### 7、绘制一个简单的曲线图(这个技术难度不高，项目中又常见，可以拿来练练手)

```

- (void)drawRect:(CGRect)rect {
//绘制线条
    UIBezierPath *grayLine = [UIBezierPath bezierPath];
    grayLine.lineWidth = 1;
    grayLine.lineCapStyle = kCGLineCapButt;
    [[UIColor lightGrayColor] setStroke];
    CGFloat x1 = 140;
    CGFloat x2 = 360;
    CGFloat y = 160;
    //循环绘制7条横向线条
    for (int i = 0; i < 7; i++) {
        [self drawPathWithLine:grayLine movePoint:CGPointMake(x1, y + i * 20) toPoint:CGPointMake(x2, y + i * 20)];
    }

    //绘制竖线
    [self drawPathWithLine:grayLine movePoint:CGPointMake(x1 + 5, y) toPoint:CGPointMake(x1 + 5, y + 6 * 20 + 5)];
    [self drawPathWithLine:grayLine movePoint:CGPointMake(x2, y) toPoint:CGPointMake(x2, y + 6 * 20 + 5)];
    //绘制圆点
    UIBezierPath *circleDot = [self drawCircleWithOvalInRect:CGRectMake(x1, y + 5 * 20 - 5, 10, 10)];
    [[UIColor blueColor] setStroke];
    UIBezierPath *blueLine = [UIBezierPath bezierPath];
    blueLine.lineWidth = 2;
    [self drawPathWithLine:blueLine movePoint:CGPointMake(circleDot.currentPoint.x - 2.5, circleDot.currentPoint.y - 5) toPoint:CGPointMake(x1 + 40 + 2.5 , y + 1 * 20 + 5)];
    circleDot = [self drawCircleWithOvalInRect:CGRectMake(x1 + 40, y + 1 * 20 - 5, 10, 10)];
    [self drawPathWithLine:blueLine movePoint:CGPointMake(circleDot.currentPoint.x , circleDot.currentPoint.y + 2.5) toPoint:CGPointMake(x1 + 80 , y + 3 * 20 - 12.5)];
    circleDot = [self drawCircleWithOvalInRect:CGRectMake(x1 + 80, y + 3 * 20 - 15, 10, 10)];
    [self drawPathWithLine:blueLine movePoint:CGPointMake(circleDot.currentPoint.x , circleDot.currentPoint.y + 5) toPoint:CGPointMake(x1 + 120 , y + 4 * 20)];
    circleDot = [self drawCircleWithOvalInRect:CGRectMake(x1 + 120, y + 4 * 20, 10, 10)];
    [self drawPathWithLine:blueLine movePoint:CGPointMake(circleDot.currentPoint.x, circleDot.currentPoint.y - 5) toPoint:CGPointMake(x1 + 160 , y + 1 * 20 - 5)];
    circleDot = [self drawCircleWithOvalInRect:CGRectMake(x1 + 160, y + 1 * 20 - 15, 10, 10)];
    [self drawPathWithLine:blueLine movePoint:CGPointMake(circleDot.currentPoint.x , circleDot.currentPoint.y + 5) toPoint:CGPointMake(x1 + 200 , y + 6 * 20 - 15)];
    circleDot = [self drawCircleWithOvalInRect:CGRectMake(x1 + 200 - 2.5, y + 6 * 20 - 15, 10, 10)];
}

- (void)drawPathWithLine:(UIBezierPath *)line movePoint:(CGPoint)movePoint toPoint:(CGPoint)toPoint {
    [line moveToPoint:movePoint];
    [line addLineToPoint:toPoint];
    [line stroke];
}

- (UIBezierPath *)drawCircleWithOvalInRect:(CGRect)frame {
    UIBezierPath *circleDot = [UIBezierPath bezierPathWithOvalInRect:frame];
    [[UIColor blueColor] setFill];
    [circleDot fill];
    return circleDot;
}

```

**看客：博主，你代码写的太差了！我看不下去了！死菜鸡！**

**博主：大哥，我已经尽力了！**

**看客：这么菜，你还有脸写博客！你咋不上天呢？**

**博主：那你咋不遁地！**

#### 文中代码效果图：

![](http://ww1.sinaimg.cn/mw690/7f266405jw1f3vbdc3656j20ku12adi4.jpg)
**不要问我为什么画的这么丑，哥已经尽力了！**

#### 参考博客地址： 
* <a href = "http://ghui.me/post/2016/04/ios-bezierpath/">iOS绘图之用UIKit框架绘制常见图形（这篇是用Swift语言写的）</a>

转载请注明出处

