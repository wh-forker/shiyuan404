---
layout: post
tags: algorithm
---


### 递推式循环节


Description:    
f(n)=a\*f(n-1)+b\*f(n-2),求f(n)%p的循环节    
Solution:      
1.对p进行质因数分解，p = p1^a1\*p2^a2\*p3^a3 ... \*pn^an    
2.分别求 p1^a1,p2^a2,...,pn^an的循环节，然后取最小公倍数    
至于怎么求对 px^ax 的循环节，这里用到了二次剩余     
2.1 p mod px^ax 的循环节 = G(px)\*px^(ax-1),G(px) 就是 p mod px 的最小循环节    
2.2 对于递推式，我们可以得到特征根方程 x^2=a\*x+b ,delta=a\*a+4\*b    
2.3 对于G(px),如果delta是模px的二次剩余，G(px)是px-1的因子，否则G(px)是(px-1)\*(px+1)的因子    
暴力找到最小的就可以了    

这类的题目描述比较直观，这里就不说题意了。    
Problem: [acdream1124](http://acdream.info/problem?pid=1124)  Code: [acdream1124.cpp](https://github.com/shiyuan/acm/blob/master/Study/Repetend/acdream1124.cpp)    
Problem: [hdu4291](http://acm.hdu.edu.cn/showproblem.php?pid=4291)  Code: [hdu4291.cpp](https://github.com/shiyuan/acm/blob/master/Study/Repetend/hdu4291.cpp)    
Problem: [hdu3978](http://acm.hdu.edu.cn/showproblem.php?pid=3978)  Code: [hdu3978.cpp](https://github.com/shiyuan/acm/blob/master/Study/Repetend/hdu3978.cpp)    
Problem: [acdream1075](http://acdream.info/problem?pid=1075)  Code: [acdream1075.cpp](https://github.com/shiyuan/acm/blob/master/Study/Repetend/acdream1075.cpp)     
<br>




<br>
<br>


