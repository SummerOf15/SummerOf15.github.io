---

layout: _hight
title: Java-web video communication software developing tutorial (1)
date: 2020-04-25 16:48
tags: JAVA-WEB

---

## Java-web video communication software developing tutorial (1)

本教程旨在利用JAVA开发一个在线视频通讯软件，并通过该过程学习java网络通信相关的知识。本系列从零开始，展示如何从网络寻找资源进行学习开发的过程。文章中很多部分来自于参考资源，想要了解更多，请参考对应的引用。
<!-- more -->

### Start

有一个非常出名的开源软件，[WebRTC]( https://webrtc.org/ ). 它具备了很多功能，并且代码在github上开源，但是它十分复杂，作为入门的参考项目很困难。经过google和baidu的重重筛选，最终从github上找到一个很简单的在线视频项目，[WebRTCSimple]( https://github.com/cdoer/webrtcSimple )。该项目只有两个java文件，其中综合了WebSocket的知识，十分适合上手。本教程从该项目出发。

### WebRTCSimple

该应用基于JAVA-Web，使用了Tomcat服务器，实现了局域网内的在线视频聊天功能。含有一个界面，类似于聊天室的样子。

![demo.png](http://ww1.sinaimg.cn/mw690/006c6RCqgy1ge6dkxpneaj31hc0smhc7.jpg)

项目使用的技术主要有servlet和websocket。对于servlet相关的内容不再讲述，主要介绍一下websocket。

### WebSocket

WebSocket与Http一样[1]，是一种网络通信协议。它与HTTP协议的不同在于，HTTP协议只能从客户端发起，而WebSocket可以从服务器端发起。由此可以想到类似于手机上的APP的消息推送。这种服务器和客户端互相发送消息的方式，可以更加节约资源。

比如使用HTTP协议的时候，如果服务器有连续的状态变化，客户端要获知就非常麻烦。我们只能使用["轮询"](https://www.pubnub.com/blog/2014-12-01-http-long-polling/)：每隔一段时候，就发出一个询问，了解服务器有没有新的信息。最典型的场景就是聊天室。轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开）。而使用了WebSocket之后，服务端有更新会通知客户端更新，节省了资源。

一个图可以说明HTTP和WebSocket的区别。具体可参考[1]

![image.png](http://ww1.sinaimg.cn/large/006c6RCqgy1ge6dv2qmlbj30hz0eidha.jpg)

有一篇教程[[2]( https://how2j.cn/k/websocket/websocket-develop/1628.html#nowhere )]，关于如何搭建一个WebSocket应用，可以很好理解WebSocket。

关于WebSocket详细内容，参考[[1](https://www.ruanyifeng.com/blog/2017/05/websocket.html )]，此处不再赘述。

---

### Reference

[1] https://www.ruanyifeng.com/blog/2017/05/websocket.html 

[2]  https://how2j.cn/k/websocket/websocket-develop/1628.html#nowhere 