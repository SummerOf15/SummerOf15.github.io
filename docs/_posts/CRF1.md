---
layout: _hight
title: Conditional Random Field(条件随机场)
date: 2018-10-18 16:04:26
tags: thesis notes
mathjax: true
---
#### 1. 基本概念
`条件随机场（crf）`：是给定一组输入随机变量条件下，另一组输出随机变量的条件概率的分布模型.
`线性链条件随机场`：定义在线性链上的特殊的条件随机场（输出变量$Y_i$只与$Y_{i-1},Y_{i+1}$有关）用数学式子表达就是P(Y|X)，在标注过程中，Y是输出变量，表示标记序列，X是输入变量，表示需要标记的观测序列。比如在自然语言处理中，X是句子（词向量），Y则是对应的词性标注（名词、动词、形容词）
<!-- more -->
`条件随机场应用过程`：应用过程分为两个步骤。
- 学习：在训练数据上通过极大似然估计得到一个最符合的条件概率模型$\hat{P}(Y|X)$
- 预测：利用训练好的条件概率模型$\hat{P}(Y|X)$，输入序列X，获得概率最大的$\hat{y}$

#### 2. CRF的表示
线性链条件随机场的参数化形式为：
![linear chain crf.png](https://s1.ax1x.com/2018/10/30/i2cVhR.png)
![Z(x)](https://s1.ax1x.com/2018/10/30/i2c7CR.png)
其中，$t_k$和$S_l$是特征函数，$t_k$表示转移特征(transition function)，$S_l$表示状态特征函数，通常两个函数取值为1或者0. Z(x)为规范化因子(normalization factor)，相当于对所有输出的统计，使得最终结果在1以内。$\lambda_k,\mu_l$为权重，是将来学习的参数。
这个式子可以用在这个例子中：在一个标注问题中，输入观测序列为$X=(X_1,X_2,X_3)$，输出序列为$Y=(Y_1,Y_2,Y_3)$，取值为{1,2}，现在给相应的特征函数$t_k$和$s_l$以及对应的权值$\lambda_k,\mu_l$，求对于给定的观测序列*x*,输出标记序列为y=(1,2,2)的概率。(具体问题请参考李航 统计学习方法)

#### 3. 条件随机场的学习算法
##### 3.1 改进的迭代尺度算法(IIS)
IIS的想法是：假设最大熵模型当前的参数向量是$w=(w_1,w_2,...,w_n)^T$，希望找到一个新的参数向量w+$\delta$使得模型的对数似然值增大，然后不断循环这个过程直到$\delta$小于某个阈值，可以认为现在w达到了最理想的值。
##### 3.2 拟牛顿法
![Newton method](https://s1.ax1x.com/2018/10/30/i2cm1x.gif)
##### 4. 条件随机场的预测算法
条件随机场的预测问题是给定条件随机场P(Y|X)和输入序列x，求条件概率最大的输出序列y。
常用的算法为viterbi算法。(原理与dijestela最短路径算法相似)
![viterbi_pic](https://s1.ax1x.com/2018/10/30/i2chbF.png)
预测算法的目标可以表达成y=argmax(P(y|x))等效于argmax(w*F(y,x))
算法步骤：
1. 初始化：从第一个预测序列$y_0$开始，求w*F(y,x)。
![viterbi_1.png](https://i.loli.net/2018/10/30/5bd7c1894b811.png)
2. 递推，依次求出剩余的预测序列的最大概率以及最大概率所对应路径。
![viterbi_2](https://s1.ax1x.com/2018/10/30/i2cfDU.png)
3. 递推直到结束，获取$y_n$对应的最大概率以及路径。
![viterbi_3](https://s1.ax1x.com/2018/10/30/i2cWuT.png)
4. 从后往前一步一步返回最优路径
![viterbi_4](https://s1.ax1x.com/2018/10/30/i2c2vV.png)
最终的最优路径是$y^*=(y_1^*,y_2^*,...,y_n^*)$
#### Reference：
https://www.cnblogs.com/pinking/p/9194865.html
https://blog.csdn.net/wkebj/article/details/77965714
李航《统计学习方法》

