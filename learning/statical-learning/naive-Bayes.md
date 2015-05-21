---
layout: page
title: 朴素贝叶斯法
permalink: /naive-Bayes/
tags: learning
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

Description:

学习笔记：[《统计学习方法》](/tjxxff)第四章 朴素贝叶斯法

<br>
### 1.朴素贝叶斯
朴素贝叶斯法是基于贝叶斯定理和特征条件独立假设的分类方法，本质上是学习生成数据的机制，属于生成模型    
后验概率：     

$$
P(Y=c_k|X=x)=\frac{P(X=x|Y=c_k)P(Y=c_k)}{\sum\_{k}P(X=x|Y=c_k)P(Y=c_k)}
$$    

$$
P(Y=c_k|X=x)=\frac{P(Y=c_k) \prod_j P(X^{(j)}=x^{(j)}| Y=c_k) }{\sum\_{k}P(X=x|Y=c_k)P(Y=c_k)}
$$

考虑到分母都是一样的，我们定义贝叶斯分类器如下：
$$
f(x)=arg \max_{c_k} P(Y=c_k) \prod_j P(X^{(j)}=x^{(j)}|Y=c_k)
$$

<br>
### 2.贝叶斯估计
最大似然估计的做法有可能导致概率值为0，因而导致最终后验概率均为0，故引入条件概率的贝叶斯估计：    

$$
P\_\lambda(X^{(j)}=a\_{jl}|Y=c_k) = \frac{ \sum\_{i=1}^{N} I(x_i^{(j)}=a\_{jl},y_i=c_k)+\lambda}{ \sum\_{i=1}^{N} I(y_i=c_k)+S_j \lambda}
$$    

\\(\lambda=0\\) 时,为朴素贝叶斯算法    
\\(\lambda=1\\) 时,为拉普拉斯平滑,即有:    
$$
\sum\_{l=1}^{S_j} P\_\lambda(X^{(j)}=a_{jl}|Y=c_k)=1
$$    

先验概率的贝叶斯估计为：   
 
$$
P\_\lambda (Y=c_k)=\frac{\sum_{i=1}^{N}I(y_i=c_k)+\lambda}{N+K \lambda}
$$
