---
layout: post
title: "iOS绘制虚线方式总结"
date: 2015-10-12 23:44:28 +0800
comments: true
tags: [iOS, xcode, Objective-C]
keywords: ios开发, 绘制虚线
categories: Objective-C
---
### 一、iOS中绘制虚线常见的几种方式：

1、重写drawRect方法。

```
- (void)drawRect:(CGRect)rect
{
    [super drawRect:rect];
CGContextRef currentContext = UIGraphicsGetCurrentContext();
//设置虚线颜色
    CGContextSetStrokeColorWithColor(currentContext, [UIColor BlackColor].CGColor);
    //设置虚线宽度
    CGContextSetLineWidth(currentContext, 1);
    //设置虚线绘制起点
    CGContextMoveToPoint(currentContext, 0, 0);
    //设置虚线绘制终点
    CGContextAddLineToPoint(currentContext, self.frame.origin.x + self.frame.size.width, 0);
    //设置虚线排列的宽度间隔:下面的arr中的数字表示先绘制3个点再绘制1个点
    CGFloat arr[] = {3,1};
    //下面最后一个参数“2”代表排列的个数。
    CGContextSetLineDash(currentContext, 0, arr, 2);
    CGContextDrawPath(currentContext, kCGPathStroke);
    
}
```
<!--more-->

2、采用CAShapeLayer方式绘制虚线

```
CAShapeLayer *shapeLayer = [CAShapeLayer layer];
[shapeLayer setBounds:self.bounds];
[shapeLayer setPosition:CGPointMake(self.frame.size.width / 2.0, self.frame.size.height)];
[shapeLayer setFillColor:[UIColor clearColor].CGColor];
//设置虚线颜色
shapeLayer setStrokeColor:[UIColor BlackColor].CGColor];
//设置虚线宽度
[shapeLayer setLineWidth:self.frame.size.height];
[shapeLayer setLineJoin:kCALineJoinRound];
//设置虚线的线宽及间距
 [shapeLayer setLineDashPattern:[NSArray arrayWithObjects:[NSNumber numberWithInt:3], [NSNumber numberWithInt:1], nil]];
 //创建虚线绘制路径
 CGMutablePathRef path = CGPathCreateMutable();
 //设置虚线绘制路径起点
 CGPathMoveToPoint(path, NULL, 0, 0);
 //设置虚线绘制路径终点
 CGPathAddLineToPoint(path, NULL, self.frame.size.width, 0);
 //设置虚线绘制路径
 [shapeLayer setPath:path];
 CGPathRelease(path);
 //添加虚线
 [self.layer addSublayer:shapeLayer];
    
```

##### 关于这种方式已经有人整理出了一个非常好用的类方法，具体请参看文章末尾的参考博客地址：“iOS绘制虚线方法【原创】”一文，此方法使用起来非常简单方便。
###### **<font color="red">注意：下面非完整代码，如有需要，请自行前往该博文中直接复制使用。</font>**

```
/**
 ** lineView:       需要绘制成虚线的view
 ** lineLength:     虚线的宽度
 ** lineSpacing:    虚线的间距
 ** lineColor:      虚线的颜色
 **/
 
+ (void)drawDashLine:(UIView *)lineView lineLength:(int)lineLength lineSpacing:(int)lineSpacing lineColor:(UIColor *)lineColor
{
    CAShapeLayer *shapeLayer = [CAShapeLayer layer];
    .....
    [shapeLayer setStrokeColor:lineColor.CGColor];
    ......
    [shapeLayer setLineDashPattern:[NSArray arrayWithObjects:[NSNumber numberWithInt:lineLength], [NSNumber numberWithInt:lineSpacing], nil]];
	　......
    [lineView.layer addSublayer:shapeLayer];
    
}

```

3、经济实惠型：采用贴图的方式绘制虚线（需要设计师切图配合）

```
UIImageView *imgDashLineView =[[UIImageView alloc] initWithFrame:CGRectMake(15, 200, self.view.frame.size.width - 30, 1)];

[imgDashLineView setBackgroundColor:[UIColor colorWithPatternImage:[UIImage imageNamed:@"xuxian.png"]]];

[self.view addSubview:imgDashLineView];

```
在以上这几种方式中我个人比较偏爱最后一种，简单粗暴。

以上内容部分来自于网络，本着分享的学习精神，如有涉及侵权问题，请及时告知。欢迎一起探讨学习，有问题请留言。我将会在第一时间对你的问题进行回复。如需转载，请注明出处。
###### 参考博客地址：
* 1、<a href="http://blog.it985.com/14080.html">iOS绘制虚线方法【原创】</a>