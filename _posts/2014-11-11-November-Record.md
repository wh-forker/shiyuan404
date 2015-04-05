---
layout: post
tags: algorithm
---

Description:    

濒临退役，最后两个星期的刷题记录。    
弱，还是只能水水，唉    

[All records in Nov.](https://github.com/shiyuan/acm/tree/master/November)    

      

##网赛补题

[Code](https://github.com/shiyuan/acm/tree/master/November/%E7%BD%91%E8%B5%9B)    

2014 ACM/ICPC Asia Regional Beijing Online    
06 Frog    
已知青蛙每次至多可以跳L个距离，有N个石头，青蛙初始位置为0，目的地为M,问放置若干石头使得青蛙能到达目的地，但是跳的次数尽量多，假设青蛙足够聪明      
贪心，对当前石头，如果能一次到达就一次达到，不能一次到达的要避免用prepre那个石头直接跳过来，所以用一个变量来记录某次跳的过程的距离step,如果step加上当前要跳到石头的距离小于L，更新step加上这个距离即可，否则更新step为这个距离，ans++.当两块石头之间的距离很大时，贪心的想法是每(L+1)的距离，我都让他跳两次，具体石头放哪，还是跟已经跳的距离step有关系    

2014 ACM/ICPC Asia Regional Xi'an Online    
11 Ellipsoid     
已知椭球面方程 a\*x^2+b\*y^2+c\*z^2+d\*y\*z+e\*x\*z+f\*x\*y=1,求椭球面上离原点最近的点    
模拟退火     

2014 ACM/ICPC Asia Regional Shanghai Online    
10 Fraction    
求等比数列b*q^i中以n为前缀的数字的极限分布     
本福特定律：在b进位制中，以数n起头的数出现的机率为\log_b(n+1)-\log_b(n)    
或者枚举前20w项，求大致的概率，需要去掉前几项    
04 Contest
已知n个人做m道题正确的概率，求满足任意时刻，任意两个人已经做题数目不超过1时的最大期望    
状态dp,dp[now][s]表示当前做了now个题，且当前的n个人解决的状态，当当前状态为全1时置0       


## 红书(只看了一章)

[Code](https://github.com/shiyuan/acm/tree/master/November/%E7%BA%A2%E4%B9%A6)    

第一章 数学    

1.1 概率   
Coupons uva 10288    
n种(1<=n<=33)不同的彩票，每次得到每种彩票的概率相同，问集齐所有彩票的期望    
设dp[i]表示已经获得i张彩票，要继续集齐n张彩票的期望，用带分数的形式输出    
dp[i]=1+i/n\*dp[i]+(n-i)/n\*dp[i+1];    
(n-i)/n\*dp[i]=1+(n-i)/n\*dp[i+1];    
dp[i]=n/(n-i)+dp[i+1];    
其中dp[n]=0;    
dp[0]=n/n+dp[1]=n/n+n/(n-1)+dp[2]=n/n+n/(n-1)+...+n/1    
因为n的范围很小，可以先筛出n内的所有质数，质因数分解后，求最后的分母，然后约分什么的很暴力地解决    

Generator Hangzhou 2005     
求用26个字母的前n个生成给定串s的期望，思路比较好想，dp[i]=1+1/n*∑(dp[i:j]),dp[i:j]表示状态i后面加一个字符j后的状态，状态表示，==，不会。搜了下题解，说是KMP或AC自动机。。。那就算了吧    
具体数学上，有一个相应的结论，这里拓展一下：    
第8章离散概率里的抛掷硬币问题，两个人分别确定两个串(Sa,Sb,仅含有T,F),若干次投掷硬币后，某个人的串为当前的后缀时获胜，求两人分别赢的概率。用S:T表示串T在串S中的匹配情况，例如S=T="HTHTHHTHTH"时，S:T=(1000010101)2,当S[i:l)==T[0:l-i)时取1，那么两个人获胜比例为Sa:Sb=(B:B-B:A):(A:A-A:B),hdu4839就是这样的一道题，这题放在百度之星决赛里，对没见过的人来说，真是坑了。    
对于这个题，就是把2进制拓展成n进制，当串s[i:l]与串s[0:l-i]匹配的时候，该位(l-1-i)取1，很神奇地就暴力出答案了。    

1.2 代数    

1.2.1 Polya    

Arif in Dhaka UVa 10294     
s个珠子，每个珠子有c种染色，问有多少种不同的项链，分别输出旋转不同的，以及旋转和翻转均不同的方案数
polya计数，上模板。     

1.2.2 矩阵    

CERC 2008 Tower    
一个数列，a[1]=1,a[n]=2\*a[2]\*a[n-1]-a[n-2],输入(a[2],n,mod),输出a[n]的前n项平方和对mod取余   
构造4\*4的矩阵，很暴力的矩阵快速幂，不解释     

ZOJ 2113 XX Language    
模拟题，给出一系列赋值语句和操作语句，包括LOOP     
最多20个变量，问经过所有操作后值的情况，构造矩阵，字符串处理太恶心了，没写     


1.2.3 线性方程组    

Ars Longa World Finals 2006    
题意，看不懂，题解说是物理+高斯消元，这种题，实在头疼     


1.2.4 线性规划    

Expensive Drink Beijing 2007    
一种饮料由四种单价为c1,c2,c3,c4的材料构成，饮料单价为a1\*c1+a2\*c2+a3\*c3+a4\*c4,并给出a4\*c4的范围，现在给出n种组合，表示3种材料数量a1,a2,a3时，单价为pi。问构成(A1,A2,A3)的饮料的最大价值。(0<=c1<=c2<=c3)    
单纯形法，相当于给出了2*n个a1\*c1+a2\*c2+a3\*c3的限制条件,再加上c1<=c2和c2<=c3这两个限制，直接跑模板，可惜WA了，也不知啥原因。这题变量只有三个，也许别人是转化成三维平面的切割做的吧。    

1.3 组合    

1.3.1 基本排列组合    

UVa 10207 The Unreal Tournament    
两个人比赛，第一个人赢的概率为p,当第一个人赢的次数为I或者第二个人赢的次数为J时游戏结束，确定输赢，求概率以及递归次数。    
那么枚举第二个人赢的次数为[0,J),ans=∑(C(I-1+J-i,I-1)\*p^i\*q^(J-i))    
递归次数很好算就是2\*(C(I+J,I)-1),估计精度问题，WA了，搞不过    


1.3.2 容斥原理    

Jackpot NEERC 2004 Northern Sub    
求n个数的LCM内，能整除这n个数中任意一个数的个数     
简单容斥原理，java高精度     

The Almost Lucky Numbers TC SRM 453    
只包含4,7的数是Lucky数，能被lucky数整除的数是Almost Lucky Number,求区间内Almost Lucky Number的个数。    
范围是10^10,但是可以直接暴力，然后容斥原理。没在题库里找到，==。

1.3.3 生成函数    

Vasya's Dad Ural 1387    
求Ｎ个点的顶点无标号的有根数的数目是多少。     　
没想法，书上做法是打表或者生成函数，不会。　　　　　

1.3.4 生成树计数    

Organising the Organisation UVa 10766    
求一个点数为n(1<=n<=50),边数为m(0<=m<=1500)的无向图中，以编号k的节点为根的生成树的个数。    
书上做法是，Matrix-Tree定理+高斯消元。。。。。       

1.4 博弈    
略过

1.5 数论    

1.5.1 模线性方程    

模板题    


##Codechef Record

1.water    
[Chef and Frogs](http://www.codechef.com/problems/FROGV) [[solution]](http://www.codechef.com/viewplaintext/4404796)    

2.water 完全背包     
[Pizza Delivery](http://www.codechef.com/problems/DBOY/) [[solution]](http://www.codechef.com/viewplaintext/4405057)    

3.好题 dp[i][j]表示以i结尾j为公差的等差数列的个数       
[Maxim and Progressions](http://www.codechef.com/problems/MAXPR) [[solution]](http://www.codechef.com/viewplaintext/4406216)    

4.dp会超时，组合计数    
[Lucky Driving](http://www.codechef.com/problems/LUKYDRIV) [[solution]](http://www.codechef.com/viewplaintext/4414367)    

5.water dp or matrix     
[Barcelona Gameplay Tactics](http://www.codechef.com/problems/FCBARCA/) [[solution]](http://www.codechef.com/viewplaintext/4414904)

6.water dp       
[Save The Princess](http://www.codechef.com/problems/SHIRO/) [[solution]](http://www.codechef.com/viewplaintext/4416099)       

7.water 01背包     
[Polo the Penguin and the Test](http://www.codechef.com/problems/PPTEST) [[solution]](http://www.codechef.com/viewplaintext/4424566)     

8.概率推导     
[Need More Diamonds](http://www.codechef.com/problems/DIAMOND/) [[solution]](http://www.codechef.com/viewplaintext/4435756)   

9.water    
[Little Chef and Numbers](http://www.codechef.com/problems/PROSUM/) [[solution]](http://www.codechef.com/viewplaintext/4578832)     

10.求区间积取模，预处理     
[Chef and Segments](http://www.codechef.com/problems/CHMOD/) [[solution]](http://www.codechef.com/viewplaintext/4578852)     


Others:    


[MUTANT DESTRUCTION](http://www.codechef.com/problems/CW1) [[solution]](http://www.codechef.com/viewplaintext/5053925)    

[EXPECTED VALUE](http://www.codechef.com/problems/CW2) [[solution]](http://www.codechef.com/viewplaintext/5064115)    

[APP DEVELOPER DILEMMA](http://www.codechef.com/problems/CW3) [[solution]](http://www.codechef.com/viewplaintext/5064245)     

[CODERS RANKING](http://www.codechef.com/problems/CW4) [[solution]](http://www.codechef.com/viewplaintext/5057013)    

[CHESS MASTER](http://www.codechef.com/problems/CW5) [[solution]](http://www.codechef.com/viewplaintext/5056453)    

[MUSICAL PROBLEM](http://www.codechef.com/problems/CW6) [[solution]](http://www.codechef.com/viewplaintext/5056695)    

[Prime Generator](http://www.codechef.com/problems/PRIME1/) [[solution]](http://www.codechef.com/viewplaintext/5159929)    

[Marbles](http://www.codechef.com/problems/MARBLES) [[solution]](http://www.codechef.com/viewplaintext/5159979)    

[Closing the Tweets](http://www.codechef.com/problems/TWTCLOSE/) [[solution]](http://www.codechef.com/viewplaintext/5160043)    

[Mahesh and his lost array](http://www.codechef.com/problems/ANUMLA/) [[solution]](http://www.codechef.com/viewplaintext/5210759)    

[Walk on the Axis](http://www.codechef.com/problems/ANUWTA) [[soltution]](http://www.codechef.com/viewplaintext/5210456)    

[The Army](http://www.codechef.com/problems/ANUARM) [[solution]](http://www.codechef.com/viewplaintext/5210555)    


