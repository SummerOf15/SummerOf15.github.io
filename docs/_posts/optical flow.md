---
layout: _hight
title: Hight Accuracy Optical Flow Estimation Based on a Theory for Warping_ reading notes
date: 2018-07-18 16:04:26
tags: thesis notes
---
作者采用了一个能量方程计算光流，这个方程基于三个假设：亮度恒定，梯度恒定，时空平滑约束( a discontinuity-preserving spatio-temporal smoothness constraint). 
<!-- more -->
## Introduction
光流法是估计两张图片像素之间的位移范围，可以应用在获取像素之间的对应关系的情景。在过去的研究中，很多新的方法被提出，解决了之前的模型的一些缺点。比如，为了解决光流场中的不连续性，平滑约束替代了二次正则化。由粗到精(coarse to fine)的策略和非线性模型的方法可以用来解决大的位移。时空结合的方法用一个额外维度的信息改善了最终的结果。
在本文中，作者整合了之前的一些概念，并且提出一个能用数学方法最小化的能量方程。它使用了非线性的光流约束条件，并且提出了一个多分辨率的方法解决像素对应问题。另外，还使用了梯度恒定约束扩展灰度恒定约束，提升了对灰度变化的鲁棒性。  

## The Variational Model
作者对能量方程中基于的假设进行了介绍。
### 灰度恒定假设(grey value constancy assumption)
从光流估计开始，像素的灰度值不会随着位移的变化而变化
<img src="https://latex.codecogs.com/gif.latex?$$I(x,y,t)=I(x&plus;u,y&plus;v,t&plus;1)\tag{1}$$" />
线性化后的灰度恒定约束是(推导参考[wiki](https://en.wikipedia.org/wiki/Optical_flow))
<img src="https://latex.codecogs.com/gif.latex?$$I_xu&plus;I_yv&plus;I_t=0\tag{2}$$"/>

由于线性化的约束只存在与图像沿着位移方向线性变化，并不符合事实，所以作者采用最原始的，即公式(1)，非线性的灰度恒定约束。  
### 梯度恒定假设(Gradient constancy assumption)
灰度恒定假设不能适应亮度有轻微变化的场景，但是这种情况在自然界是非常常见的。因此，梯度作为一种不随着位移和线性光照的变化的量可以对这种场景有着较好的适应性。
<img src="https://latex.codecogs.com/gif.latex?$$&space;\nabla&space;I(x,y,t)=\nabla&space;I(x&plus;u,y&plus;v,t&plus;1)&space;$$"/>
<img src="https://latex.codecogs.com/gif.latex?$\nabla=(\partial&space;x,\partial&space;y)$"/>表示空间上的梯度。  
### 平滑假设(smoothness assumption)
到目前为止，用来估计像素位移的模型只考虑了单个像素，并没有考虑像素的邻域。因此在一些梯度消失的场景（比如朝向镜头移动的物体的边界处的外部，由于受到遮挡会消失掉，或者因为单个物体平移而覆盖了场景中一些其它的物体）和沿着光轴方向移动的物体的位移([aperture problem](http://fourier.eng.hmc.edu/e180/lectures/motion/node11.html))。因此，通过对光流场像素平滑（pixelwise smooth）来满足平滑假设。  
### 多尺度方法（Multiscale approach）
当位移大于一个像素的时候，能量函数最小化很容易陷入一个局部最优解。为了达到一个全局最优，多尺度的方法比较有效。通过对原始图像进行降采样，然后在采样后的图像上寻找最优，此时得到一个比较粗略的解，然后把它作为初值，代入到优化的步骤中，得到精化后的最优解。  

假设$ x:=(x,y,t)^T $和$ w:=(u,v,1)^T $。
考虑灰度恒定约束和梯度恒定约束的能量方程为：
<img src="https://latex.codecogs.com/gif.latex?$$&space;E_{Data}(u,v)=\int_{\Omega}({|I(x&plus;w)-I(x)|}^2&plus;\gamma{|\nabla&space;I(x&plus;w)-\nabla&space;I(x)|}^2)dx&space;$$"/>
因为使用二次方对粗差太敏感，所以使用了凹函数$\Psi(s^2)=\sqrt{s^2+\epsilon^2}$来减少影响，其中$\epsilon$是一个很小的正数，为了在后续最小化过程中更容易而引入，在原文中，作者令$ \epsilon = 0.001 $.
平滑约束是通过惩罚光流场的变化（variation）来进行约束的。可以表示为:
<img src="https://latex.codecogs.com/gif.latex?$$&space;E_{Smooth}(u,v)=\int_{\Omega}(\Psi({|\nabla_{3}u|}^2&plus;{|\nabla_{3}v|}^2)dx&space;$$"/>  
其中$ \nabla_3:={(\partial_x,\partial_y,\partial_t)}^T $表示在时空方向上的变化。  
最终的能量函数为：
$$ E(u,v)=E_{Data}+\alpha E_{Smooth} $$
$\alpha $是权重系数，最终通过最小化能量函数得到相应的$u$和$v$。

## Minimisation
利用拉格朗日等式，对能量函数在空间域上求偏导，得到：
<img src="https://latex.codecogs.com/gif.latex?$$&space;\Psi^{'}({I_z}^2&plus;\gamma(I_{xa}^2&plus;I_{yz}^2))\cdot&space;(I_{x}I_z&plus;\gamma(I_{xx}I_{xz}&plus;I_{xy}I_{yz}))-\alpha&space;div({\Psi}^{'}({|\nabla_3&space;u|}^2&plus;{|\nabla_3&space;v|}^2)\nabla_3&space;u)=0&space;$$"/>
<img src="https://latex.codecogs.com/gif.latex?$$&space;\Psi^{'}({I_z}^2&plus;\gamma(I_{xa}^2&plus;I_{yz}^2))\cdot&space;(I_{y}I_z&plus;\gamma(I_{yy}I_{yz}&plus;I_{xy}I_{xz}))-\alpha&space;div({\Psi}^{'}({|\nabla_3&space;u|}^2&plus;{|\nabla_3&space;v|}^2)\nabla_3&space;u)=0&space;$$" />
一个极小值点一定满足这个拉格朗日等式。
因为该式子中对于参数$ w={(u,v,1)}^T $ 依旧是非线性的。通常解决这种问题的方法是在固定点处迭代，即确定一个初始值的情况下迭代。在本文中，通过一个coarse-to-fine的多尺度方式首先在coarse层确定一个初始值，然后再作为初值放到finer层中迭代精化，直至得到最终结果。

## Reference
代码参考 [matlab](http://people.csail.mit.edu/celiu/OpticalFlow/)