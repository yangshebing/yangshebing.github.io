---
layout: post
title: "iOS FFmpeg编译及kxmovie、iFrameExtractor调试详解（无障碍阅读，绝对是你想要的）"
date: 2015-11-16 01:44:27 +0800
comments: true
tags: [iOS9, Xcode7.1, UICollectionView]
keywords: UICollectionView, WaterFlow, FlowLayout, 下拉刷新
categories: Others
---

**特此声明：本文是通过网上收集整理加上自己亲自实验得来的。感谢所有网上分享的辛勤劳动者，本文仅供iOS学习使用。在学习的过程中，如果有问题或者不清楚的地方，欢迎留言，我们共同探讨互相学习。欢迎批评指正。**

**如需转载，请注明出处：<a href = "http://blog.csdn.net/yangshebing21/article/details/43986913">http://blog.csdn.net/yangshebing21/article/details/43986913</a>**

#### 开发环境：最新 ffmpeg-2.5.4/iOS SDK8.1/Xcode6.1/OS X Yosemite版本10.10.2

### 一、编译ffmpeg库

#### 编译调试ffmpeg库的步骤：

**开发环境：ffmpeg-2.5.4/Xcode6.1**

#### （一）、下载ffmpeg源码：

请点击此链接<a href = "http://ffmpeg.org/download.html">http://ffmpeg.org/download.html</a>

 <font color = "red"><b>注：我的ffmpeg-2.5.4是解压放至Desktop的</b></font>
 
#### （二）、调试编译ffmpeg源码
 
 1、安装yasm:
 <!--more-->
 
 方法一:
 
 在终端输入
 
 ```
 sudo curlhttp://www.tortall.net/projects/yasm/releases/yasm-1.2.0.tar.gz >yasm.tar.gz
 ```

方法二：

（1）Download yasm sourcecode from：
  <a href = "http://www.tortall.net/projects/yasm/releases/yasm-1.2.0.tar.gz">http://www.tortall.net/projects/yasm/releases/yasm-1.2.0.tar.gz</a>
  
（2）Unpack tar xvzfyasm-1.2.0.tar.gz

（3）cd yasm-1.2.0

（4）Configure and build:

```
./configure && make -j 4 &&sudo make install
```

2、下载pearl脚本文件：gas-preprocessor.pl：

下载地址： <a href = "https://github.com/libav/gas-preprocessor">https://github.com/libav/gas-preprocessor</a>

（1）拷贝gas-preprocessor.pl文件到 /usr/bin 目录下。

如果找不到/usr/bin目录，直接使用”Finder—>前往—> 前往文件夹—>/usr/bin”或者使用快捷键“shift+command+G”前往文件夹，到指定目录下粘贴gas-preprocessor.pl文件
 
（2）修改gas-preprocessor.pl文件的读写权限，使用 chmod a+rwxgas-preprocessor.pl

用终端命令cd定位到 gas-preprocessor.pl文件夹下执行：`chmod a+rwxgas-preprocessor.pl`命令

3、下载build-shell.sh脚本文件：

下载地址：<a href = "https://github.com/kewlbear/FFmpeg-iOS-build-script">https://github.com/kewlbear/FFmpeg-iOS-build-script</a>


（1）下载解压build-shell.sh文件

（2）定位至build-shell.sh文件夹执行build-shell.sh脚本文件

使用方式有4种，我们可以根据特定的使用环境来执行对应的脚本，一般我们就直接执行：`./build-ffmpeg.sh`

* To build everything:

**`./build-ffmpeg.sh`**
                                    
* To build arm64 libraries：

**`./build-ffmpeg.sh arm64`**


* To build fat libraries for armv7 and x86_64 (64-bit simulator)：

**`./build-ffmpeg.sh armv7 x86_64`**

* To build fat libraries from separately built thin libraries:

**`./build-ffmpeg.sh lipo`**

注：我的build-shell.sh是解压放至Desktop的，以上四种shell脚本的使用方式来自<a href = "https://github.com/kewlbear/FFmpeg-iOS-build-script">https://github.com/kewlbear/FFmpeg-iOS-build-script</a>，后面几种方式大家可以自己去尝试。

4、脚本执行完成之后，会在桌面上生成三个文件夹：“FFmpeg-iOS”“scratch”和”thin”文件夹

scratch文件夹里面是单独编译的库，FFmpeg-iOS文件夹里面是合并编译的库。在使用的过程中，我们主要会用到FFmpeg-iOS 文件夹下的“include”文件夹和"lib"文件夹中的文件。


### 二、在工程中使用FFmpeg，网上成熟的有kxmovie。

1、下载kxmovie工程进行测试：

下载地址： <a href = "https://github.com/kolyvan/kxmovie">https://github.com/kolyvan/kxmovie</a>

把FFmpeg编译出来的.a文件添加到工程中。

![](http://ww2.sinaimg.cn/large/7f266405jw1ey37q81fp5j205r09xwfq.jpg)

在使用的时候得注意添加下面的动态链接库文件:

```
libz.dylib
libbz2.dylib
libiconv.dylib
```

**运行工程**

以下是在编译运行工程中可能会报的错误：

注意：如果在FFmpeg编译的时候出现“ffmpeg yasm not found，use --disable for a crippled build”，原因是 yasm是汇编编译器，因为FFmpeg中为了提高效率用到了汇编指令，解决这个问题有两种方法，一是下载一个yasm.exe安装在mingw/bin下面；二是不使用汇编指令，在配置上加上 即 ./configure --disable-yash 。(这就是为什么前面提到了要安装yasm)
成功通过编译（这里是指FFmpeg编译成静态库文件）

通过后会生成一个compiled(FFmpeg-iOS)的文件夹，里面有include和lib两个文件夹，这两个文件夹都要拷贝至工程目录下面。include是静态链接库要用到的头文件，lib是存放静态链接库的文件，然后把这些文件存放在项目里面即可，一定要把文件也copy进去，然后在项目的build settings中的header search paths字段加入include文件的路径 如：“$(SRCROOT)/ffmpeg/include” 前面的复选框选不选都可以；再在library search paths字段中加入a文件的路径 如：“$(SRCROOT)/ffmpeg/lib” 。否则可能会出现libavformat/avformat.h file not found 的错误。（出现这个错误是指编译kxmovie工程找不到相应的头文件）

![](http://ww2.sinaimg.cn/large/7f266405jw1ey37rw3nknj20m207wwgg.jpg)

上面图示：在工程目录下有个“FFmpeg”的文件夹，分别把编译FFmpeg完成后产生的“ FFmpeg-iOS ”文件夹中的“include”和”lib”文件夹拷贝至”FFmpeg”文件夹中，include文件夹中存放的是头文件，lib文件夹中存放的是.a静态库文件。最后就是在工程目录下的FFmpeg文件夹包含“include”和”lib”两个文件夹。设置Header Search Paths和Library Search Paths时直接include和lib两个文件夹拖上去，自动显示路径。

![](http://ww4.sinaimg.cn/large/7f266405jw1ey37subv8hj20gu06omye.jpg)

如果还出现找不到 avformat_open_input 的错误（我的没有出现过这个错误），说明你的ffmpeg还是太新了，之前的那个方法名已经改成了这个名字，把avformat_open_input改成av_open_input_file 就可以了。其他错误请自行谷歌或者留言，我们共同探讨。
上面报错总结文章来自：http://www.dnetzj.com/Content/267.html

最后一步，别忘了安装kxmovie.xcworkspace的cocoapods

**`pod install --verbose --no-repo-update`**

否则会报链接错误

以上kxmovie工程编译调试完毕，模拟器真机都通过测试。目前没有进一步的去研究这个工程，发现播放的视频质量有点粗糙。有时间再做进一步的研究。

### 三、iFrameExtractor-master（iFrameExtractor）工程编译和运行

（1）运行iFrameExtractor-master工程，与上面kxmovie工程操作一样，把FFmpeg文件夹拷贝至工程目录下:
       (工程目录下本来是没有FFmpeg文件夹的)

![](http://ww1.sinaimg.cn/large/7f266405jw1ey37tiypu8j20gx07b75s.jpg)

iFrameExtractor-master下载地址：https://github.com/lajos/iFrameExtractor

(2)设置好Header Search Paths和Library Search Paths

![](http://ww2.sinaimg.cn/large/7f266405jw1ey37u3v6q5j20kj02x0ts.jpg)

![](http://ww2.sinaimg.cn/large/7f266405jw1ey37v057v9j20kg07p40a.jpg)


（3）最后还是会报链接错误：

```
Undefined symbols for architecture i386:
"_iconv", referenced from:      
    _avcodec_decode_subtitle2 in libavcodec.a(utils.o)  
"_iconv_close", referenced from:
    _avcodec_open2 in libavcodec.a(utils.o)
    _avcodec_decode_subtitle2 in libavcodec.a(utils.o)
"_iconv_open", referenced from:
    _avcodec_open2 in libavcodec.a(utils.o)
    _avcodec_decode_subtitle2 in libavcodec.a(utils.o)
ld: symbol(s) not found for architecture i386
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```
 
![](http://ww2.sinaimg.cn/large/7f266405jw1ey37vhigjfj20fq0a8djf.jpg)

**解决办法："TARGETS" - > "BuildSettings" - > "Other Linker Flags" 添加other Linker Flags 项：-liconv**

![](http://ww1.sinaimg.cn/large/7f266405jw1ey37wvjqv1j20dl01s74f.jpg)

此报错问题出自：<a href = "http://stackoverflow.com/questions/21211215/ffmpeg-wont-build-in-my-project-works-fine-in-example-app/28777851#28777851">http://stackoverflow.com/questions/21211215/ffmpeg-wont-build-in-my-project-works-fine-in-example-app/28777851#28777851</a>

至此完成iFrameExtractor-master工程编译和运行

### 本文参考文章资料：

* <a href = "http://blog.csdn.net/oqqQuZi1234567/article/details/43152689">iOS－－kxmovie之FFmpeg编译和使用</a>
* <a href = "http://www.th7.cn/Program/Ruby/201502/385491.shtml">iOS ffmpeg kxmovie 编译 调试</a>

**欢迎共同学习进步，本着分享的精神，只是为了让你学习起来更加轻松。**

**如需转载，请注明出处：<a href = "http://blog.csdn.net/yangshebing21/article/details/43986913">http://blog.csdn.net/yangshebing21/article/details/43986913</a>**