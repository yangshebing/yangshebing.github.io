---
layout: post
title: "Swift中UITableView的用法(自定义UITableView和UITableViewCell)"
date: 2016-01-04 21:23:33 +0800
comments: true
tags: [iOS, Xcode, Swift, UITableView, UITableViewCell]
keywords: Swift, UITableView, UITableViewCell
categories: Swift
---
一、自定义表视图（UITableView）
<!--more-->
```

import UIKit
class HomeTableView: UITableView, UITableViewDataSource, UITableViewDelegate {
//表视图的数据源：dataList
    var dataList = [AnyObject]()
//单元格的标识符：homeCellId
    let identify = "homeCellId"
    //重写表视图初始化方法
    override init(frame: CGRect, style: UITableViewStyle) {
        super.init(frame:frame, style:style)
        //调用初始化子视图方法
        initSubviews()
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    //初始化表视图的子视图方法
    func initSubviews () {
    //设置数据源代理
        dataSource = self;
   //设置表视图代理
        delegate = self;
        //注册单元格class方式
        registerClass(HomeCell.self, forCellReuseIdentifier: identify)
       //设置表视图的分割线显示风格
        separatorStyle = .None;
        //注册单元格xib方式
        //registerNib(UINib(nibName: "HomeCell", bundle: nil), forCellReuseIdentifier:identify)
    }
   //实现表视图返回组数代理方法：不实现默认为1
    func numberOfSectionsInTableView(tableView: UITableView) -> Int {
        return 1;
    }
//实现表视图返回行数代理方法
    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return dataList.count;
    }
    //实现表视图返回单元格代理方法
    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    //取单元格
        let cell = tableView.dequeueReusableCellWithIdentifier(identify) as! HomeCell
        //设置单元格的选中风格
        cell.selectionStyle = .None;
        cell.titleStr = dataList[indexPath.row] as? String
        return cell
    }
    //选中单元格的代理方法
    func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
        print("the indexpath row is \(indexPath.row)")
    }
    //返回单元格高度的代理方法
    func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
        return 80;
    }
}
```

二、自定义单元格（UITableViewCell）

```
import UIKit

class HomeCell: UITableViewCell {
//定义子视图变量
    var imgView: UIImageView!
    var titleLabel: UILabel!
    var button: UIButton!
    var titleStr: String?
    // 重写单元格初始化方法
    override init(style: UITableViewCellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier);
        //调用单元格初始化方法
        initSubviews()
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:)has not been implemented")
    }
    
    override func awakeFromNib() {
        super.awakeFromNib()
        // Initialization code
    }
    //单元格子视图初始化方法
    func initSubviews () {
        imgView = UIImageView(frame: CGRectMake(0, 0, 30, 30))
        imgView.image = UIImage(named: "exam.png")
        contentView.addSubview(imgView);
        
        titleLabel = UILabel(frame: CGRectMake(0, 0, frame.size.width, frame.size.height))
        titleLabel.textAlignment = .Left;
        titleLabel.textColor = UIColor.lightGrayColor();
        titleLabel.font = UIFont.systemFontOfSize(15);
        titleLabel.text = "心灵鸡汤，每天一起干"
        contentView.addSubview(titleLabel)
        
        button = UIButton(type: .Custom)
        button.frame = CGRectMake(0, 0, frame.size.width, frame.size.height)
        button.setTitle("关注", forState: .Normal)
        button.setTitleColor(UIColor.whiteColor(), forState: .Normal)
        button.backgroundColor = UIColor.grayColor()
        contentView.addSubview(button)
    }
    //重写单元格布局子视图方法
    override func layoutSubviews() {
        super.layoutSubviews()
        titleLabel.text = titleStr
      //...
      //布局单元格子视图
      //...
    override func setSelected(selected: Bool, animated: Bool) {
        super.setSelected(selected, animated: animated)
        // Configure the view for the selected state
    }
}
```