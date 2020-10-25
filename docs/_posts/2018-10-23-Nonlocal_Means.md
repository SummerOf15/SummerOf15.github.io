---
layout: _hight
title: Non-Local Means
date: 2018-10-23 16:04:26
tags: denseCRF
mathjax: true
excerpt_separator: <!--more-->
---
#### 1. Definition
`局部均值(local means):`就是常用的在像素周围开一个窗口，求窗口中像素灰度的平均值，此时权重都为1，常用到的卷积核为：
<!--more-->
$$\frac{1}{9}
 \left[
 \begin{matrix}
   1 & 1 & 1 \\
   1 & 1 & 1 \\
   1 & 1 & 1
  \end{matrix}
  \right]
$$
`非局部均值(Non-local Means):`非局部代表使用全局的图片，按照某种相似度的方式进行加权取平均。滤波后图像清晰度高，而且不丢失细节。
#### 2. Method description
(1)以像点A(x,y)为中心，取[x-d:x+d,y-d:y+d]大小的窗口为搜索区域。另外，以A为中心，取半径为r的[x-r:x+r,y-r:y+r]窗口为邻域窗口，命名为窗口X，其中r< d。
![nlm1.bmp](https://i.loli.net/2018/10/30/5bd7c150e78d5.bmp)
(2)从搜索窗口的左上角开始构建一个同样以r为半径的邻域窗口，假设为窗口Y，对窗口Y与窗口X的相应位置求差的平方和，作为相似度。
$$||V(x)-V(y)||^2=\frac{1}{d^2}\sum_{z=-d}^{d}||v(x+z)-v(y+z)||^2$$
$$w(x,y)=\frac{1}{Z(x)}exp(-\frac{||V(x)-V(y)||^2}{h^2})$$
Z(x)为规范化因子
$$Z(x)=\sum_y exp(-\frac{||V(x)-V(y)||^2}{h^2})$$
(3)以相似度为权，乘以邻域窗口Y，然后移动窗口Y重复步骤(2)，以一种类似加权求平均的方式求得最终的区域X中心的像素灰度值。
$$X(x)=\sum w(x,y)*V(y)$$
#### Reference
https://blog.csdn.net/u010839382/article/details/48229579
