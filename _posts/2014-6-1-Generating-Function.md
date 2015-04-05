---
layout: post
tags: algorithm
---

Wiki：[Generating_function](http://en.wikipedia.org/wiki/Generating_function)    
Easy：[TankyWoo](http://www.wutianqi.com/?p=596) [matrix67](http://www.matrix67.com/blog/archives/120)      
Essay：[《母函数的性质及应用》](http://wenku.baidu.com/view/8589c9bff121dd36a32d82a8.html)    
Difficult：《Concrete Mathematics》7.Generating Function    

{% highlight c  %}
Common Types:   
   G(x)=1/(1-x)=1+x+x^2+x^3+...
   G(x)=1/(1-x)^2=G'(x)=1+2*x+3*x^2+...
   G(x)=1/(1-x)^n=(1+x+x^2+x^3+...)^n
   G(x)=1+C(n,n-1)*x+C(n+1,n-1)*x^2+...+C(n+k-1,n-1)*x^k+...
   G(x)=e^x=1+x+x^2/2!+x^3/3!+...     <1,1,1,1,1,1,...>
   G(x)=e^(-x)=1-x+x^2/2!-x^3/3!+...  <1,-1,1,-1,1,...>
   G(x)=(e^x+e^(-x))/2    <1,0,1,0,1,....>
   G(x)=(e^x-e^(-x))/2    <0,1,0,1,0,....>
{% endhighlight  %}

Practice:     

Easy: [Code](https://github.com/shiyuan/acm/tree/master/Study/Generating%20Function/easy)      

Problem: [poj1322](http://poj.org/problem?id=1322)  Code: [poj1322.cpp](https://github.com/shiyuan/acm/blob/master/Study/Generating%20Function/poj1322.cpp)     

题意：C(<=100)种巧克力，依次拿出n个，当某种巧克力的个数为2时，拿走这2个，求剩下恰好m个巧克力的概率(0<=n,m<=10^6)    
思路：无解的情况当然是m>n或m>c或(n-m)&1.    
对于C种巧克力出现的次数要么为奇数，要么为偶数.    
g1(x)=e^x=1+x/1!+x^2/2!+x^3/3!+...    
g2(x)=e^-x=1-x/1!+x^2/2!-x^3/3!+...    
那么奇数次的生成函数为f1(x)=(e^x+e^-x)/2,    
偶数次的生成函数为f2(x)=(e^x-e^-x)/2       
so G(x)=f1(x)^m\*f2(x)^(c-m)=(e^x+e^-x)^m\*(e^x-e^-x)^(c-m)/2^c     
化简得 令分别取s,t 得某项 (-1)^s\*C(m,s)\*C(c-m,t)\*e^(x\*(c-2\*s-2\*t))     
用泰勒展开式可知 x^n 的系数     
g(n)=sigma((-1)^s\*C(m,s)\*C(c-m,t)\*(c-2\*s-2\*t)^n) (0<=s<=m,0<=t<=c-m)     
so ans=C(c,m)\*g(n)/c^n (p.s.计算的时候把c^n的计算带到循环里，不然前面计算太大溢出答案会输出1.#IO)    

<br>

Problem: [CEOI2004 Sweet](www.z-trening.com/tasks.php?show_task=5000000041&lang=uk)  Code: [CEOI2004sweet.cpp ](https://github.com/shiyuan/acm/blob/master/Study/Generating%20Function/CEOI2004sweet.cpp)     

题意：n个糖果罐里分别有mi颗糖，问至少吃a颗，至多吃b颗的方案数%2004        
思路：对每个糖果罐L(i)(x)=(1+x+x^2+..+x^m[i])  so 生成函数为    
G(x)=(1+x+x^2+...+x^m[1])\*(1+x+x^2+...+x^m[2])\*...\*(1+x+x^2+...+x^m[n])    
   =(1-x^(m[1]+1))/(1-x)\*...\*(1-x^(m[1]+1))/(1-x)     
   =(1-x^(m[1]+1))\*...\*(1-x^(m[n]+1))\*1/(1-x)^n    
求的是一个区间值，那么我们令 Candy(x) 表示至多吃的糖数的前x项和    
左边用最暴力的方法可以得到一个至多为2^n项的式子，表示成F(x),第k项为 f(k)\*x^k,右边(1-x)^n展开得到 H(x)=1+C(n,n-1)\*x+C(n+1,n-1)\*x^2+...,第k项为 h(x)\*x^k,假设吃了I颗，那么它的系数为 g[I]=sigma(f(i)\*h(I-i)) (i∈set,不一定是连续的),对于前I项和，Candy(I)=sigma(f[i]\*(1+C(n,n-1)+C(n+1,n-1)+...+C(n+I-i-1,n-1))) (i∈set),化简多项式的结果，Candy(I)=sigma(f[i]\*C(n+I-i,n)) (i∈set)     
ans=Candy(B)-Candy(A-1)     


