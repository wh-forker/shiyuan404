---
layout: post
tags: algorithm
---

### Stable Marriage Problem


Description:    
给出n个男人心中n个女人的排名，和n个女人心中n个男人的排名，求一种匹配使之形成稳定匹配。(稳定指的是形成的集合中，任意两对之间，不存在某个人和某个人在一起 比集合中的分配更优)    

{% highlight c  %}
Solution:
  function stableMatching { 
       Initialize all m ∈ M and w ∈ W to free 
       while ∃ free man m who still has a woman w to propose to { 
          w = m's highest ranked such woman to whom he has not yet proposed 
          if w is free 
            (m, w) become engaged 
          else some pair (m', w) already exists 
            if w prefers m to m' 
              (m, w) become engaged 
              m' becomes free 
            else 
              (m', w) remain engaged 
       } 
   }   
{% endhighlight  %}

Think:      
这个算法中，man做的选择总是越来越差的，woman做的选择总是越来越好的。     

Wiki:[Stable_marriage_problem](http://en.wikipedia.org/wiki/Stable_marriage_problem)    
Essay:[matrix67](http://www.matrix67.com/blog/archives/2976)     
Practice:    
[poj3487.cpp](https://github.com/shiyuan/acm/blob/master/Poj/poj3487.cpp)   [zoj1576.cpp](https://github.com/shiyuan/acm/blob/master/Zoj/zoj1576.cpp)    

<br>

### 未完待续

