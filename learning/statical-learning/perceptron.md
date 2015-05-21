---
layout: page
title: 感知机
permalink: /perceptron/
tags: learning
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

Description:

学习笔记：[《统计学习方法》](/tjxxff)第二章 感知机    


<br>
### 1.感知机模型
感知机是二类分类的线性模型，设输入空间为\\( X∈R^{n} \\)，\\( Y∈\\{-1,+1\\} \\)感知机模型如下：    
$$f(x)= sign(w \cdot x+b)$$    
其中\\(w\\)和\\(b\\)为模型参数，\\(w\\)为权值，\\(b\\)为偏置。几何解释为：超平面\\(S\\)将特征空间分成两部分，\\(w\\)为超平面的法向量，\\(b\\)为截距。    

<br>
### 2.学习策略
数据集的线性可分：存在某个超平面将正实例点和负实例点完全正确的划分到超平面两侧，几何解释为：两类点形成的空间凸包没有交集。    
考虑误分类点到超平面的总距离为：   
 
$$
-\frac{1}{||w||}\sum_{x_i\in M} y_i(w \cdot x_i + b)
$$    

不考虑范数部分，我们可以得到损失函数如下：    

$$
L(w,b)= -\sum_{x_i \in M} y_i(w \cdot x_i + b)
$$    

<br>
### 3.学习算法
随机梯度下降法：首先任意选取一个超平面，然后梯度下降极小化损失函数，极小化过程中每一次梯度下降即 <b>随机</b> 选取一个误分类点进行梯度下降：      

$$
\nabla_w L(w,b)=-\sum_{x_i \in M}y_i x_i
$$           
 
$$
\nabla_b L(w,b)=-\sum_{x_i \in M}y_i
$$     


输入：\\( T = \{ (x_1,y_1),...,(x_n,y_n) \} \ 其中 x_i \in \chi = R^{n},y_i \in Y = \\{ -1,+1 \\},i \in [1,N] \\)    
输出：\\( w,b \\)    
算法：每次对于误分类点执行如下操作，直到误分类点不存在，学习率为 \\( \eta \\)    
$$ w \gets w+\eta \cdot y_i x_i $$
$$ b \gets b+\eta \cdot y_i   $$
收敛性：证明(P31)    

Novikoff 定理:    
设\\(T=\\{ (x_1,y_1),...,(x_n,y_n) \\}\\)，学习率为\\( \eta \\)，点集线性可分，则：    
(1)存在满足条件\\(|| \hat{w}\_{opt} ||=1\\)的超平面\\(\hat{w}\_{opt} \cdot \hat{x} = w\_{opt} \cdot x + b\_{opt} =0\\) 将训练数据集完全正确分开且存在 \\( \gamma >0 \\) ,对所有的 \\( i \in [1,N]\\) ,则:    

$$
 y_i( \hat{w}\_{opt} \cdot \hat{x}\_{i})=y_i ( w\_{opt} \cdot x+ b\_{opt}) \geq \gamma
$$       
(2)令 \\(R= \max_{1\leq i \leq N} ||\hat{x_i}||\\) ，则算法在数据训练集上的误分类次数k满足\\(k \leq(\frac{R}{\gamma})^{2}\\)即迭代次数有限     

<br>
## 4.对偶形式
梯度下降每一次的修改，可以看成是每个点产生影响的累积，若第\\(i\\)个点更新了\\( n_i \\)次那么：     
 
$$
w=\sum_{i=1}^{N} \alpha_i y_i x_i
$$    

$$
b= \sum_{i=1}^{N} \alpha_i y_i
$$    

$$
其中 \alpha_i = \eta \ n_i
$$    

其实意义上就是将\\(w\\)向量表示成各个点更新次数的向量，那么：        

$$
f(x)=sign(\sum_{i=1}^{N} \alpha_j y_j x_j \cdot x+b)
$$    

若 \\(y_i (\sum_{i=1}^{N} \alpha_j y_j x_j \cdot x_i + b ) \leq 0\\) 即误分类点,则更新如下：     
$$
\alpha_i \gets \alpha_i + \eta
$$
$$
b \gets b + \eta \ y_i
$$

