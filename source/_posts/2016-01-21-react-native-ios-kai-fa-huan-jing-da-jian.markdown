---
layout: post
title: "React Native iOS 开发环境搭建"
date: 2016-01-21 01:07:09 +0800
comments: true
tags: [iOS, React Native, Node.js, Hybrid, Xcode]
keywords: React Native, Hybrid, iOS, Xcode
categories: Others
---
#### 一、React Native基础环境搭建

1、安装<a href="http://brew.sh">Homebrew</a>

打开终端输入：
`
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
`
<!--more-->
2、安装<a href="https://github.com/creationix/nvm#installation">nvm</a>方法

通过脚本安装方式，在终端执行：
`
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.1/install.sh | bash
`

激活nvm,在安装脚本执行完之后，在终端执行:
`
. ~/.nvm/nvm.sh
`

nvm其他安装方式详见<a href="https://github.com/creationix/nvm#installation">安装向导</a>

3、默认安装最新版本的Node.js并且设置好命令行的环境变量
`
nvm install node && nvm alias default node
`

可以输入node命令启动Node.js环境，npm(node package manager)文档传送门:<a href="https://docs.npmjs.com">"点击此处"</a>

4、安装watchman

在终端输入：
`
brew install watchman
`

到此处React Native基础环境搭建基本完成，推荐经常运行`brew update && brew upgrade`命令保持你的程序是最新的。

#### 二、iOS开发环境

1、iOS开发环境：（默认读者已经安装**Xcode7.0或以上版本**，没有可自行前往AppStore下载）

* 使用npm（node包管理工具）安装react-native-cli(此处应注意权限问题，如果遇到权限问题，请在下面的命令前加上sudo)

`
npm install -g react-native-cli
`

* 初始化工程（下面的语句中千万不要加sudo，后果自负）

`
react-native init ReactNativeProject（工程名）
`

在初始化的过程中，如果进度缓慢，推荐你将npm仓库源换成国内镜像：(翻墙用户请忽略)
在终端输入：

```
npm config set registry https://registry.npm.taobao.org
npm config set disturl https://npm.taobao.org/dist
```

2、使用Xcode运行调试程序

* 查找ReactNativeProject文件目录

按住快捷键`Command+Shift+G`，在弹出的“前往文件夹”对话框中输入路径`/Users/yangshebing/ReactNativeProject`（yangshebing表示电脑用户名，请自行更改）,点击前往。在文件目中的ios文件夹下找到“ReactNativeProject.xcodeproj”工程文件。如图所示：（有关终端查找该文件目录方法，请自行到文章末尾的参考文章中寻找）
![](http://ww4.sinaimg.cn/large/7f266405jw1f0651k88wij216s0oa42t.jpg)

* 运行ReactNativeProject工程

使用Xcode打开“ReactNativeProject.xcodeproj”工程文件，按住快捷键"Command+R"运行此项目，运行成功后便可在模拟器上看到效果。

* 编辑index.ios.js文件修改界面

笔者编辑index.ios.js文件工具是Vim，每当编辑完index.ios.js文件并保存之后，在模拟器(Simulator)中，按`Command+R`可刷新界面查看改变后的效果。

下面我们也试着稍微修改一下index.ios.js文件：

```
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 */
'use strict';
import React, {
  AppRegistry,
  Component,
  StyleSheet,
  //添加Image全局变量
  Image,
  Text,
  View
} from 'react-native';

class ReactNativeProject extends Component {
  render() {
    return (
      <View style={styles.container}>
      //添加文本展示
        <Text style={styles.welcome}>
          美女
        </Text>
        //添加图片展示
        <Image 
        source={{uri: 'http://h.hiphotos.baidu.com/image/pic/item/6609c93d70cf3bc7f0f5b7eed300baa1cd112a3e.jpg'}} 
        //这里需要指定图片的大小
        style={styles.picture} />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  //这里可以设置文本的字体，对齐方式等
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  //新增图片style，设置图片大小
  picture: {
    width: 80,
    height: 150,
  },
});

AppRegistry.registerComponent('ReactNativeProject', () => ReactNativeProject);
```
刷新后运行效果如下图所示：
![](http://ww3.sinaimg.cn/mw690/7f266405jw1f06flkcxxgj20ku12a3zr.jpg)

* 真机运行调试项目

在`ReactNativeProject`工程中找到AppDelegate.m文件，将`localhost`替换成自己电脑的IP地址。

```
//  jsCodeLocation = [NSURL URLWithString:@"http://localhost:8081/index.ios.bundle?platform=ios&dev=true"];

  jsCodeLocation = [NSURL URLWithString:@"http://192.168.31.168:8081/index.ios.bundle?platform=ios&dev=true"];
```

替换完成之后再次运行程序，这个时候可以通过摇晃设备唤出开发菜单。

* 禁用开发菜单

在打包提交市场前，需要禁用开发菜单。禁用开发菜单方法如下：
打开工程，选择`Product → Scheme → Edit Scheme`或使用快捷键“Command + <”,在弹出的窗口中选择左边的Run菜单项。更改“Build Configuration” 设置项为“Release”。
![](http://ww1.sinaimg.cn/large/7f266405jw1f06e3sf5qjj21ds0s0tc2.jpg)

#### 三、参考资料地址：

* <a href="https://facebook.github.io/react-native/docs/tutorial.html">React Native开发指南英文版（官方）</a>
* <a href="http://reactnative.cn/docs/getting-started.html#content">React Native开发指南中文版</a>
* <a href="http://www.cnblogs.com/meteoric_cry/p/4862314.html">React Native入门遇到的一些问题</a>

**此文仅供学习交流，转载请注明出处！**
