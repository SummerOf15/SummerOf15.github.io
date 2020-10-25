---
layout: _hight
title: windows7+Pycharm+Docker+paddlepaddle配置
date: 2018-12-05 14:04:26
tags: paddlepaddle
---
因为paddlepaddle只支持windows的docker下的配置，但是docker下的编程不是很方便，考虑到pycharm支持远程python编辑器，所以考虑使用pycharm结合docker进行windows下的paddlepaddle环境搭建。
<!-- more -->
#### docker配置
首先下载docker，安装的时候一定要记得安装virtuoBox，然后按照官方的教程pull下来paddlepaddle的docker image。
然后打开virtuoBox。
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvuv9ozftj30r40fi0v9.jpg)
点击`共享文件夹`。
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvuwzo8o9j30k30c7abd.jpg)
上面的路径是我分配过的，想要设置自己的同步文件路径可以点击右上角的符号，比如这样设置：
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvuxseri2j309a065mx8.jpg)
代表把windows本机的文件夹E：\doc和docker中的doc文件夹进行同步。
在确定之后启动虚拟机
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvuyfaq09j30kg0dbjtm.jpg)
使用ls命令可以看到我之前添加的pypaddle文件夹
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvuyxjsaaj30kb036aae.jpg)
这个文件夹是对应我的windows电脑的`F:/java/pypaddle`。
接下来运行镜像（我要在pypaddle文件夹中用pycharm写程序，所以我把pypaddle文件夹与paddlepaddle镜像中进行文件同步，同步文件夹为`/opt/program`）
使用如下命令：
docker run -it -v pypaddle:/opt/program hub.baidubce.com/paddlepaddle/paddle /bin/bash
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv08fd2gj30jz02awep.jpg)
进入相应的文件夹下，可以看到
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv0xzfgxj30ej01t3yh.jpg)
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv15omqoj30f006qjrh.jpg)
此时，文件同步已经完成。
#### pycharm配置
Pycharm一定要用professional版本的才行。具体应该如何下载请自行百度。
在pycharm中，打开一个工程，进入settings。
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv25yeqnj30sk0abq48.jpg)
右边设置处点击出现add，进入。
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv3ceqjoj30nh0g4dgz.jpg)
点击new
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv3jctabj30co0ev3ze.jpg)
修改下面的path，其中virtuo machine path指的是虚拟机的path，localpath是指本机的path
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv4kpt5sj30f906eaau.jpg)
在我的工程中，我按照F:/program/java/pypaddle映射到opt/program的方式设置之后如下
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv5ceuuaj308l04l0t6.jpg)
设置完成之后点击确定，需要等待一段时间。
等到程序运行结束之后，就可以在pycharm中写paddlepaddle的代码了。
#### PS
但是有一个问题没有解决，程序的运行需要在virtuobox的命令行运行。
![](http://ww1.sinaimg.cn/mw690/006c6RCqgy1fxvv5n0xp2j30d6011wee.jpg)
不能直接在pycharm中运行，会报错。
我现在还不知道怎么解决这个问题，如果哪位朋友知道，欢迎分享。