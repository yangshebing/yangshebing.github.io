---
layout: post
title: "iOS开发之基础控件（Swift版）"
date: 2016-01-02 11:08:19 +0800
comments: true
tags: [iOS, Xcode, Swift]
keywords: iOS开发, 基础控件, Swift
categories: Swift
---
  1、文本（UILabel）：
  
```
  let titleLabel = UILabel(frame: CGRectMake(0, 0, view.frame.size.width, view.frame.size.height))
        //设置titleLabel的文本对齐方式：左：.Left, 中:.Center, 右:.Right
        titleLabel.textAlignment = .Center
        //设置titleLabel的文本颜色
        titleLabel.textColor = UIColor.lightGrayColor()
        //设置titleLabel文字的字体为系统字体，大小为15
        titleLabel.font = UIFont.systemFontOfSize(15)
        //设置titleLabel文字的字体为"Helvetica", 大小为15
        titleLabel.font = UIFont(name: "Helvetica", size: 15)
        titleLabel.text = "心灵鸡汤，每天一起干"
        //titleLabel字体大小自适应宽度，根据指定宽度titleLabel自动调整字体显示大小
        titleLabel.adjustsFontSizeToFitWidth = true
        //指定titleLabel的显示行数，0代表自动折行
        titleLabel.numberOfLines = 1
        //获取系统所有字体名称
        let fontFamily = UIFont.familyNames()
        print(fontFamily)
        view.addSubview(titleLabel)
```
<!--more-->
2、按钮（UIButton）：

```
//按钮类型：常见的两种如下
         /**
         *.Custom:自定义类型
         *.System:系统类型（iOS7.0）
         **/
        let followButton = UIButton(type: .Custom)
        followButton.frame = CGRectMake(100, 100, 100, 50)
        //设置按钮在特定状态下的文本显示，常见的有：
        /**
         *.Normal:按钮常规显示状态
         *.Selected:按钮选中显示状态
         *.Highlighted:按钮高亮显示状态
         *.Disabled:按钮关闭状态显示
        **/
        followButton.setTitle("关注", forState: .Normal)
        //设置按钮在特定状态下的文本色值显示
        followButton.setTitleColor(UIColor.whiteColor(), forState: .Normal)
        //设置按钮在特定状态下的图片显示
        followButton.setImage(UIImage(named: "back"), forState: .Normal)
        followButton.setImage(UIImage(named: "select"), forState: .Disabled)
        //设置按钮显示文本的字体大小
        followButton.titleLabel?.font = UIFont.systemFontOfSize(15)
        //设置按钮选中状态
        followButton.selected = true
        //设置按钮是否打开状态
        followButton.enabled = true
        //设置是否响应用户touch事件
        followButton.userInteractionEnabled = true
        //设置按钮的背景颜色
        followButton.backgroundColor = UIColor.grayColor()
        //关闭按钮处于高亮状态图片变暗显示状态
        followButton.adjustsImageWhenHighlighted = false
        //设置按钮图片的内部填充
        followButton.imageEdgeInsets = UIEdgeInsetsMake(10, 10, 10, 10)
        //设置按钮文本的内部填充
        followButton.titleEdgeInsets = UIEdgeInsetsMake(10, 10, 10, 10)
        //为按钮添加点击事件
        followButton.addTarget(self, action: "followAction", forControlEvents: .TouchUpInside)
        view.addSubview(followButton)
```

* 按钮点击事件响应方法：

```
    func followAction(sender: UIButton) {
        print("关注")
    }

```
3、图片展示视图（UIImageView）：

```
let imgView = UIImageView(frame: CGRectMake(0, 0, view.frame.size.width, view.frame.size.height))
        //设置图片视图显示图片
        imgView.image = UIImage(named: "exam.png")
        //设置图片视图高亮状态显示图片
        imgView.highlightedImage = UIImage(named: "exam1.png")
        //设置图片视图内容展示模式：常见的几种内容展示模式
        /**
        *.ScaleToFill:缩放图片填满整个图片视图
        *.ScaleAspectFit:缩放内容以适应原图尺寸，没有铺满的地方保持透明
        *.ScaleAspectFill:按钮高亮显示状态
        **/
        imgView.contentMode = .ScaleAspectFit
        //设置图片视图是否高亮显示
        imgView.highlighted = true
        //设置是否响应用户touch事件,默认为false
        imgView.userInteractionEnabled = true
        //播放一组图片
        let img1 = UIImage(named: "play1.png")!
        let img2 = UIImage(named: "play2.png")!
        let img3 = UIImage(named: "play3.png")!
        let images = [img1, img2, img3]
        //设置需要播放的一组图片
        imgView.animationImages = images
        //设置播放持续时间
        imgView.animationDuration = 1
        //设置播放重复次数
        imgView.animationRepeatCount = 20
        //开始播放
        imgView.startAnimating()
        //停止播放
        imgView.stopAnimating()
        
        view.addSubview(imgView);
```

4、文字输入框（UITextField）：

```
let textField = UITextField(frame: CGRectMake(20, 100, 100,30))
        //设置文本编辑框显示文字
        textField.text = "将编程进行到底"
        //设置文本编辑框占位文字
        textField.placeholder = "客官，你想不想要？"
        //设置文本编辑框的边框显示类型
        textField.borderStyle = .RoundedRect
        //设置文本编辑框的文字字体
        textField.font = UIFont.systemFontOfSize(15)
        //设置文本编辑框的文本颜色
        textField.textColor = UIColor.blackColor()
        //设置文本编辑框输入文本时首字母是否自动大写
        textField.autocapitalizationType = .None
        //设置文本编辑框清除按钮显示模式
        textField.clearButtonMode = .WhileEditing
        //设置文本编辑框的文本对齐方式
        textField.textAlignment = .Center
        //设置文本编辑框返回键显示类型
        textField.returnKeyType = .Search
        //设置是否为安全文本输入状态
        textField.secureTextEntry = true
        //设置输入键盘类型
        textField.keyboardType = .NumberPad
        //设置文本编辑框的代理
        textField.delegate = self
        //唤起键盘，让文本编辑框成为第一响应者
        textField.becomeFirstResponder()
        view.addSubview(textField)
```
* UITextFiled代理方法（UITextFiledDelegate）:

```
//将要开始编辑代理方法,此方法会在键盘弹出之前调用
    func textFieldShouldBeginEditing(textField: UITextField) -> Bool {
        return true
    }
    
    //已经开始编辑代理方法，此方法会在键盘弹出之后调用
    func textFieldDidBeginEditing(textField: UITextField) {
        print(textField.text)
    }
    
    //当return按钮被点击时调用
    func textFieldShouldReturn(textField: UITextField) -> Bool {
        //收起键盘，注销文本编辑框为第一响应者
        textField.resignFirstResponder()
        return true
    }
    
    //将要结束编辑代理方法，此方法会在收起键盘的时候调用
    func textFieldShouldEndEditing(textField: UITextField) -> Bool {
        return true
    }
    
```
5、开关控件（UISwitch）：

```
        let sliderControl = UISlider(frame: CGRectMake(30, 200, 100, 30))
        //设置滑动条控件最大值
        sliderControl.maximumValue = 10
        //设置滑动条控件最小值
        sliderControl.minimumValue = 1
        //设置滑动条控件初始值
        sliderControl.value = 5
        //设置滑动条控件最大值方向颜色显示
        sliderControl.maximumTrackTintColor = UIColor.redColor()
        //设置滑动条控件最小值方向颜色显示
        sliderControl.minimumTrackTintColor = UIColor.brownColor()
        //设置滑动条控件拖动按钮颜色
        sliderControl.thumbTintColor = UIColor.blueColor()
        //设置滑动条控件最大值方向图片显示：正常状态
        sliderControl.setMaximumTrackImage(UIImage(named: "slidermax.png"), forState: .Normal)
        //设置滑动条控件最小值方向图片显示：正常状态
        sliderControl.setMinimumTrackImage(UIImage(named: "slidermin.png"), forState: .Normal)
        //设置滑动条控件拖动按钮显示图片：正常状态
        sliderControl.setThumbImage(UIImage(named: "thumb.png"), forState: .Normal)
        //为滑动条控件添加：值改变触发事件
        sliderControl.addTarget(self, action: "sliderAction:", forControlEvents: .ValueChanged)
        view.addSubview(sliderControl)
```

* 开关值改变事件响应方法：

```
func sliderAction(sliderControl: UISlider) {
        print("the slider value is \(sliderControl.value)")
    }
```

6、加载指示器视图（UIActivityIndicatorView）：

```
let activityControl = UIActivityIndicatorView(frame: CGRectMake(100, 200, 30, 30))
        //设置加载指示器视图的风格
        activityControl.activityIndicatorViewStyle = .Gray
        //设置加载指示器视图的背景颜色
        activityControl.backgroundColor = UIColor.redColor()
        //开始动画
        activityControl.startAnimating()
        //停止动画
        activityControl.stopAnimating()
        view.addSubview(activityControl)
```

7、分段控件视图（UISegmentedControl）:

```
let items = ["全部", "电影", "新闻", "科技"]
        let segmentedControl = UISegmentedControl(items: items)
        segmentedControl.frame = CGRectMake(30, 200, 180, 30)
        //设置分段控件颜色
        segmentedControl.tintColor = UIColor.redColor()
        //设置分段控件默认选中索引
        segmentedControl.selectedSegmentIndex = 1
        //为分段控件添加值改变响应事件
        segmentedControl.addTarget(self, action: "segmentControlAction:", forControlEvents: .ValueChanged)
        view.addSubview(segmentedControl)
```

* 分段控件值改变事件响应方法：

```
func segmentControlAction(segmentedControl: UISegmentedControl) {
        if segmentedControl.selectedSegmentIndex == 0 {

        } else if segmentedControl.selectedSegmentIndex == 1 {

        } else if segmentedControl.selectedSegmentIndex == 2{

        } else {

        }
       print("the segmented Control selected Index is \(segmentedControl.selectedSegmentIndex)")
    }
```

8、页面控件（UIPageControl）：

```
let pageControl = UIPageControl(frame: CGRectMake(30, 200, 180, 30))
        //设置页面控件的背景颜色
        pageControl.backgroundColor = UIColor.brownColor()
        //设置总页数
        pageControl.numberOfPages = 4
        //设置当前页数
        pageControl.currentPage = 1
        //设置页面控件指示器颜色
        pageControl.pageIndicatorTintColor = UIColor.redColor()
        //设置页面控件当前页面指示器颜色
        pageControl.currentPageIndicatorTintColor = UIColor.blueColor()
        //添加页面控件点击响应事件
        pageControl.addTarget(self, action: "changeAction:", forControlEvents: .TouchUpInside)
        view.addSubview(pageControl)
```

* 页面控件点击响应事件：

```
func changeAction(pageControl: UIPageControl) {   
  print("the current select page is :\(pageControl.currentPage)")
}
```  

9、警告视图控制器（UIAlertController）：(UIAlertView 与 UIActionSheet在iOS8.0之后就弃用了，推荐使用UIAlertController)

```
        /**
         * .Alert
         * .ActionSheet
         */
        let alertCtrl = UIAlertController(title: "提示信息", message: "确定返回吗？", preferredStyle: .Alert)
        //初始化警告动作
        let cancelAction = UIAlertAction(title: "取消", style: .Cancel) { (action) -> Void in
            //...
            //添加相应处理代码
            //...
            //把UIAlertController从父视图控制器中移除，防止内存泄漏
            alertCtrl.removeFromParentViewController()
        }
        let okAction = UIAlertAction(title: "立即返回", style: .Default) { (action) -> Void in
            //...
            //添加相应处理代码
            //...
            //把UIAlertController从父视图控制器中移除，防止内存泄漏
            alertCtrl.removeFromParentViewController()
        }
        //添加动作
        alertCtrl.addAction(cancelAction)
        alertCtrl.addAction(okAction)
        //弹出警告视图控制器
        self.presentViewController(alertCtrl, animated: true, completion: nil)
```

10、 进度条视图（UIProgressView）：

```
     let progressView = UIProgressView(progressViewStyle: .Bar)
        progressView.frame = CGRectMake(30, 200, 180, 30)
        //设置默认进度
        progressView.progress = 0.3
        //设置进度条加载过的颜色
        progressView.progressTintColor = UIColor.redColor()
        //设置进度条未加载过的颜色
        progressView.trackTintColor = UIColor.blueColor()
        //设置进度条加载过的图片展示
        progressView.progressImage = UIImage(named: "progress.png")
        //设置进度条未加载过的图片展示
        progressView.trackImage = UIImage(named: "track.png")
        view.addSubview(progressView)
```

11、 工具条控件（UIToolBar）：

```
        let toolBar = UIToolbar(frame: CGRectMake(30, 200, 180, 49))
        //设置工具条展示风格
        toolBar.barStyle = .Default
        //添加工具条按钮
        let item = UIBarButtonItem(barButtonSystemItem: .Done, target: self, action: "itemButtonAction")
        let item1 = UIBarButtonItem(barButtonSystemItem: .Edit, target: self, action: "itemButtonAction")
        //固定间距工具条按钮
        let item2 = UIBarButtonItem(barButtonSystemItem: .FixedSpace, target: self, action: "itemButtonAction")
        item2.width = 30
        let customButton = UIButton(type: .DetailDisclosure)
        customButton.addTarget(self, action: "itemButtonAction", forControlEvents: .TouchUpInside)
        //自定义按钮为工具条按钮
        let item3 = UIBarButtonItem(customView: customButton)
        let items = [item, item1, item2, item3]
        //设置工具条要显示的所有按钮
        toolBar.items = items
        view.addSubview(toolBar)
```

* 工具条按钮点击响应事件：

```
func itemButtonAction() {
        print("tool bar button")
    }
```

