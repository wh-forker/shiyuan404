---
layout: post
tags: algorithm
---

基础的概率期望学习：     

Poblem: [hit2866](http://acm.hit.edu.cn/hoj/problem/view?id=2866)   Code: [hit2866.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/hit2866.cpp) 
 
```
题意：两个人丢骰子，已知两个人的HP，分别投出1~6的概率，值小的人HP--，相等时没有变化，问第一个人赢的概率
思路：设dp[i][j]表示当前的局面为第一个人赢了i局，第二个人赢了j局的概率。去掉相等的局面，
total=win+lose; win=win/total,lose=lose/total;
dp[0][0]=1; dp[i][j]=dp[i-1][j]*win+dp[i][j-1]*lose;
```

<br>

Problem: [poj2151](http://poj.org/problem?id=2151)  Code: [poj2151.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/poj2151.cpp)      

```
题意：已知M道题，T个队伍，冠军至少要过N道题，每个队伍过题的概率，求满足冠军存在，且每个队伍都能过题的情况
思路：设dp[i][0]表示枚举到第i个队伍，一定不可能出现冠军，dp[i][1]表示枚举到第i个队伍，冠军可能出现：
dp[i][0]=dp[i-1][0]*done (done表示至少完成了一道题，但不一定是冠军的概率)
dp[i][1]=dp[i-1][0]*champion+dp[i-1][1]*(1-none)
(none表示一道题也没出，champion表示可能是冠军的概率)
设dq[i][j]表示M道题里，前i道题出了j道的概率 dq[i][j]=dq[i-1][j-1]*p+dq[i-1][j]*(1-p);
```

<br>

Problem: [hdu3853](http://acm.hdu.edu.cn/showproblem.php?pid=3853)  Code: [hdu3853.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/hdu3853.cpp)    

```
题意：给出一个R*C的矩阵，以及从(r,c)移动到(r,c),(r,c+1),(r+1,c)的概率，每次移动需要花费两个力量，
求从(1,1)移动到(R,C)的期望    
思路：设dp[i][j]表示从(i,j)移动到(R,C)的期望，
dp[i][j]=1+dp[i][j]*p[i][j][0]+dp[i][j+1]*p[i][j][1]+dp[i+1][j]*p[i][j][2];   
化简得 dp[i][j]=(1+dp[i][j+1]*p[i][j][1]+dp[i+1][j]*p[i][j][2])/(1-p[i][j][0]);
```
<br>

Problem: [hdu4050](http://acm.hdu.edu.cn/showproblem.php?pid=4050)  Code: [hdu4050.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/hdu4050.cpp)   

```
题意：从0出发，到达n以外的格子停止，每步至少A步但不超过B步，而且到达的格子分别有四种概率对应四种格子：
不能踩，只能左脚入右脚出，只能右脚进左脚出，左右脚都可以进出。每步步长尽量小，求停止的期望。
设dp[i][j]表示枚举到第i个格子的状态为j的概率，那么很简单的可以得到
dp[i+x][2]=dp[i][3]*p[i+x][2]+dp[i][4]*p[i+x][2];
但是要求每步尽量步长较小，那么从小开始枚举的时候，判断小的步长到达的格子可达不可达，
同时算出概率，这里巧妙的用p2,p3,p4来记录，很神奇，看别人解题报告这部分的时候觉得很经典。
dp[i+x][2]=dp[i][3]*p3*p[i+x][2]+dp[i][4]*p4*p[i+x][2];
同理：
dp[i+x][3]=dp[i][2]*p2*p[i+x][3]+dp[i][4]*p4*p[i+x][3];
dp[i+x][4]=dp[i][2]*p2*p[i+x][4]+dp[i][3]*p3*p[i+x][4]+dp[i][4]*p4*p[i+x][4];
p2*=(p[i+j][1]+p[i+j][2]),p3*=(p[i+j][1]+p[i+j][3]),p4*=p[i+j][1];
然后这里把所有的概率加起来得到期望
```

<br>

Problem: [poj3071](http://poj.org/problem?id=3071)  Code: [poj3071.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/poj3071.cpp)   

```
题意：2^n只队伍比赛，进行n轮比赛，已知各个队伍对其他队伍的胜率，求胜率最大的队伍
思路：第i轮比赛，j与k能够进行比赛的条件是 (j>>(i-1))==((k>>(i-1))^1)
设dp[i][j]表示第i轮，j获胜没有被淘汰的概率 dp[i][j]=dp[i-1][k]*dp[i-1][j]*p[j][k];
```

<br>

Problem: [cf148D](http://codeforces.com/problemset/problem/148/D)  Code: [cf148D.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/cf148d.cpp) 

```
题意：w只白老鼠，b只黑老鼠，公主和龙轮流取出一只，取出颜色为白的获胜，若只剩下黑的，龙获胜.
求公主获胜的概率。龙每次取老鼠时会随机跳出剩下老鼠中的某一只。
思路：设dp[i][j]表示i只白鼠，j只黑鼠的局势下公主获胜的概率，dragon[i][j]表示当前局势下龙输的概率
dp[i][j]=i/(i+j)+j/(i+j)*dragon[i][j-1];
dragon[i][j]=j/(i+j)*(dp[i-1][j-1]*i/(i+j-1)+dp[i][j-2]*(j-1)/(i+j-1));
```

<br>

Problem: [sgu495](http://acm.sgu.ru/problem.php?contest=0&problem=495)  Code: [sgu495.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/sgu495.cpp)   

```
题意：n个奖放进m个盒子，由m个选手来抽奖，问抽到奖的期望
思路：概率累积起来就行，设dp[i]表示第i个人抽到的概率，dq[i]表示抽不到的概率，当前一个人抽不到时，
当前这个人抽到的概率等于前一个人抽到的概率，当前一个人抽到时，当前这个人抽到的概率等于dp[i-1]-1/n,
所以dp[i]=dp[i-1]*dq[i-1]+dp[i-1]*(dp[i-1]-1/n);
```

<br>

Problem: [poj2096](http://poj.org/problem?id=2096)  Code: [poj2096.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/poj2096.cpp)   

```
题意：n个bug，s个子系统，bug存在于不同的子系统，问发现子系统中所有bug的期望
思路：设dp[i][j]表示已经发现i个bug,j个子系统，其余的需要被发现的期望，dp[n][s]=0;
dp[i][j]=1+dp[i][j]*(i/n)*(j/s)+dp[i+1][j]*((n-i)/n)*(j/s)+dp[i][j+1]*(i/n)*((s-j)/s)
+dp[i+1][j+1]*((n-i)/n)*((s-j)/s);
化简一下直接倒着暴力就行
```

<br>

Problem: [zoj3329](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemId=3754)  Code: [zoj3329.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/zoj3329.cpp)   

```
题意：三个面数分别为k1,k2,k3的骰子，得到值的概率为1/k1,1/k2,1/k3,当得到值为a,b,c时将计数板调为0，
当值小于等于n时继续掷骰子，求期望
思路：设dp[i]表示计数为i时，仍需要掷骰子的期望，设p[k]表示一次掷三个骰子得到的和为k的概率
dp[i]= 1 + dp[i+k]*p[k]（for 3<=k<=k1+k2+k3）+ dp[0]*(1/(k1*k2*k3))
这里开两个数组分别记录常数项e1和dp[0]的系数项e2，dp[0]=e1+e2*dp[0],所以dp[0]=e1/(1-e2);
```

<br>

Problem: [zoj3380](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemId=3957)  Code: [zoj3380.java](https://github.com/shiyuan/acm/blob/master/Study/Probability/zoj3380.java)   

```
题意：用n种符文填m个元素，求m个元素中至少出现l个位置相同的概率
思路：总的方案数n^m,设dp[i][j]表示前i种符文填充后最多出现j个位置相同的方案
dp[i][j]+=dp[i-1][j-k]+C(m-(j-k),k) (0<k<=j)
```

<br>

Problem: [hdu4405](http://acm.hdu.edu.cn/showproblem.php?pid=4405)  Code: [hdu4405.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/hdu4405.cpp)   

```
题意：飞行棋，骰子的值可以是[1,6],每次投出多少向前走多少，n个点，有m条路可以飞，
即可以从x直接飞到y,一次可以连续飞，求走到大于等于n的地方的期望
思路：用dp[i]表示到达i之后，走到终点的概率，dp[i>=n]=0 fly数组用来求直达的终点
dp[i]=1+sum(1/6*dp[fly[i+j]]) 1<=j<=6
```

<br>

Problem: [zoj3640](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemId=4808)  Code: [zoj3640.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/zoj3640.cpp)   

```
题意：n条路，每条都有一个c,当攻击力超过c时需要花费cal(c)能逃出洞穴，否则需要提升c的攻击力
思路：设dp[i]表示当前攻击力为i时逃出洞穴的期望
if(i>c[j]) dp[i]+=cal(c)/n;
else dp[i]+=(dp[i+c]+1)/n;
```

<br>

Problem: [uva10529](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1470)  Code: [uva10529.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/uva10529.cpp)   

```
题意：有策略的放n个古洛牌，有一定的概率左倒，有一定的概率右倒，问放好n个的期望
思路：设dp[i]表示i是1~i放好了的期望，dp[i]=1+(1-pl-pr)*(dpl+dpr)+pl*(dpl+dp[i])+pr*(dpr+dp[i])
分别表示，不倒的情况下填充左边跟右边的情况(1-pl-pr)*(dpl+dpr),向左边倒存在左边的全部重排dpl和
右边有i-1块没有影响仍是dp[i],向右边倒存在右边的全部重排dpr和左边有i-1块没有影响仍是dp[i]
```

<br>

Problem: [hdu4035](https://github.com/shiyuan/acm/blob/master/Study/Probability/uva10529.cpp)  Code: [hdu4035](https://github.com/shiyuan/acm/blob/master/Study/Probability/hdu4035.cpp)    

```
题意：像树一样的迷宫，每个点有k[i]的概率回到第一个点，有e[i]的概率逃出去，剩下的均等概率进入相连接的点。
看别人的题解，感觉好像zoj3329,都是一类题   
设通式为 E[i]=A[i]*E[1]+B[i]*E[father[i]]+C[i];
叶子节点：
E[i]=k[i]*E[1]+e[i]*0+(1-k[i]-e[i])*E(father[i]+1)
非叶子节点：
E[i]=k[i]*E[1]+e[i]*0+(1-k[i]-e[i])*(E[father[i]]+1+∑(E[child[j]]+1))/v[i].size();
其中：   
∑(E[child[j]]+1)=∑(A[j]*E[1]+B[j]*E[father[j]]+C[j])=∑(A[j])*E[1]+∑(B[j])*E[i]+∑(C[j]);
带进去：
E[i]=(k[i]+(1-k[i]-e[i])/v[i].size()*∑(A[j]))*E[1]+(1-k[i]-e[i])/v[i].size()*∑(B[j])*E[i]+
B[i]*E[father[i]]+((1-k[i]-e[i])/v[i].size()*∑(C[j])+1-k[i]-e[i])
so
A[i]=(k[i]+(1-k[i]-e[i])/v[i].size()*∑(A[j]))/(1-(1-k[i]-e[i])/v[i].size()*∑(B[j]));
B[i]=(1-k[i]-e[i])/v[i].size()/(1-(1-k[i]-e[i])/v[i].size()*∑(B[j]));
C[i]=(1-e[i]-k[i]+(1-k[i]-e[i])/v[i].size()*∑(C[j]))/(1-(1-k[i]-e[i])/v[i].size()*∑(B[j]));
```

<br>

Problem: [poj3744](poj.org/problem?id=3744)  Code: [poj3744.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/poj3744.cpp)  

```
题意：已知n(<=10)个雷的位置(<10^8),每次往前走一步的概率是p,走两步的概率是1-p,求最后安全的概率
思路：设dp[i]表示走到i安全的概率，dp[0]=0,dp[1]=1
if(is_mine(i)) dp[i]=0;
else dp[i]=p*dp[i-1]+(1-p)*dp[i-1];
雷之间的距离比较分散，用矩阵快速幂进行优化
```

<br>

Problem: [zoj3605](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemCode=3605)  Code: [zoj3605.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/zoj3605.cpp)   

```
题意：n个容器，初始时第s个里面有石头，交换m次容器，被看到了k次，求现在最有可能有石头的容器编号
思路：设dp[i][j][k]表示前i次交换被看到了j次，石头在k容器的方案数
if(第i次交换和k无关) dp[i][j][k]=dp[i-1][j-1][k]+dp[i-1][j][k];
else dp[i][j][k]=dp[i-1][j][k]+dp[i-1][j-1][anther];
```

<br>

Problem: [hdu4089](http://acm.hdu.edu.cn/showproblem.php?pid=4089)  Code:[hdu4089.cpp](https://github.com/shiyuan/acm/blob/master/Study/Probability/hdu4089.cpp)

```
题意：n个人在队列中排队去激活游戏，某人是第m个，现在每个人会出现四种情况：p1的概率激活失败，队列不发生变化
队列第一个人持续等待；p2的概率连接失败，第一个人被移到队列末尾重新排队；p3的概率激活成功并离开队列；
p4的概率服务器崩溃，所有人都不能再激活。问这个人在排队的过程中，服务器崩溃，且位置小于等于k的概率。
思路：设dp[i][j]表示队列里i个人，某人排第j位时，服务器崩溃且最终位置小于等于k的概率
由于第一种p1,不会有什么影响，处理一下，p2/=1-p1,p3/=1-p1,p4/=1-p1
j=1时 dp[i][j]=p2*dp[i][i]+p4; //p2*被移到队列末尾+p4   --(1)
1<j<=k时 dp[i][j]=p2*dp[i][j-1]+p3*dp[i-1][j-1]+p4;  --(2)
k<j<=i时 dp[i][j]=p3*dp[i][j-1]+p3*dp[i-1][j-1];      --(3)
设e1为dp[i][i]的系数，e2为常数，依次带进(1)(2)(3),即dp[i][j]=e1*dp[i][i]+e2;
求得dp[i][i],然后依次得到dp[i][1],dp[i][2~i]的值
```

<br>

高斯消元求期望：        

   14年多校第一场[ J ](/multi-university-training)题   

<br>

