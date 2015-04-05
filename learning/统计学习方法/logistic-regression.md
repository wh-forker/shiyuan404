---
layout: page
title: 逻辑回归模型
permalink: /logistic-regression/
tags: learning
---

Description:
    
学习笔记:[《统计学习方法》](/tjxxff)第六章 逻辑回归模型     

###１.逻辑回归模型
逻辑分布：设\\(X\\)是连续变量，\\(X\\)具有以下分布函数和概率密度函数：(\\(\mu\\)为位置参数，\\(\gamma\\)为形状参数）
$$
F(x)=P(X \leq x)=\frac{1}{1+e^{-(x-\mu)/\gamma}}
$$

$$
f(x)=F'(x)=\frac{e^{-(x-\mu)/\gamma}}{\gamma(1+e^{-(x-\mu)/\gamma})^{2}}
$$

概率函数曲线类似正态分布；分布函数为\\(S\\)形曲线，且关于\\((\mu,\frac{1}{2})\\)中心对称，也就是有：
$$
F(-x+\mu)=1-F(x+\mu)
$$

逻辑回归模型：设输入为 \\(x \in R^{n}\\)，输出为 \\(Y \in \\{-1,+1\\}\\)，\\(w\\)为权值向量，\\(b\\)为偏置(可以加到 \\(w\\)中)

$$
P(Y=1|x)=\frac{e^{w \cdot x}}{1+e^{w \cdot x}}
$$

$$
P(Y=0|x)=\frac{1}{1+e^{w \cdot x}}
$$

这里定义几率 \\(odds\\) 为事件发生和不发生的概率之比，那么对于逻辑回归就有：
$$
odds=\frac{p}{1-p}=\frac{P(Y=1|x)}{P(Y=0|x)}=e^{w \cdot x}
$$
对其取对数可以得到线性函数 \\(w \cdot x \\)

接下来用极大似然估计法估计模型的参数 \\(\hat{w}\\)
设： \\(P(Y=1|x)=\pi(x)，P(Y=0|x)=1-\pi(x)\\)
似然函数为： 
$$
\prod_{i=1}^{N}[\pi(x_i)]^{y_i}[1-\pi(x_i)]^{1-y_i}
$$
对数似然函数为：

$$
L(w)=\sum_{i=1}^{N}[y_i \log(\pi(x_i))+(1-y_i) \log(1-\pi(x_i))]
$$

$$
L(w)=\sum_{i=1}^{N}[y_i\log\frac{\pi(x_i)}{1-\pi(x_i)}+\log(1-\pi(x_i))]
$$

$$
L(w)=\sum_{i=1}^{N}[y_i(w \cdot x)-\log(1+e^{w \cdot x})]
$$

\\(L(w)\\)取极大值，\\(\hat{w}\\) 即为逻辑回归模型参数

多项逻辑回归模型：
$$
P(Y=k|x)=\frac{e^{w_k \cdot x}}{1+\sum_{k=1}^{K-1}e^{w_k \cdot x}}，k=1,2,...,K-1
$$

$$
P(Y=K|x)=\frac{1}{1+\sum_{k=1}^{K-1}e^{w_k \cdot x}}
$$


### 2.最大熵模型

最大熵原理：在约束条件下服从均匀分布，熵最大

确定联合分布\\(P(X,Y)\\)的经验分布和边缘分布\\(P(X)\\)的经验分布，以\\(\widetilde{P}(X,Y),\widetilde{P}(X)\\)表示：    

$$
\widetilde{P}(X=x,Y=y)=\frac{v(X=x,Y=y)}{N}，\widetilde{P}(X=x)=\frac{v(X=x)}{N}，v表示频数
$$

用特征函数来表示输入和输出的关系：    
$$
f(x,y)=1,x与y满足事实
$$

$$
f(x,y)=0,其他
$$

### 未完待续
