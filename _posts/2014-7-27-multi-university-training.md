---
layout: post
tags: algorithm
---

### 2014.7.22 [contest 1](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%201&source=1&searchmode=source)    

3题 ADJ ` rank90 ` 总体节奏是:大小建建过了A、D，然后我一个人苦逼地花了四个小时搞定了J    
赛后补题：E，I

### Code: [contest1](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest1)     

A.Couple doubi  找规律    
B.Jump  最小K路径覆盖     
C.Centroid of a Tree  树形dp     
D.Task  贪心     
E.Peter's Hobby  概率    
F.Shooting  线段树     
G.Xor  线段树     
H.Information Extraction  模拟    
I.Turn the pokers  组合数+逆元     
J.Rating  概率dp+高斯消元     
K.Shortest-path tree  树链剖分       

#### J 
题意：一个小女孩注册了两个帐号比赛，每次拿rating低的号做比赛，有p的概率能涨50分，(1-p)的概率降100分.     
分数一定在[0,1000]范围内，求有一个号到1000分的期望。    
思路：首先降低一下范围，记总分为[0,20],每场比赛p的概率+1分，(1-p)的概率-2分    
1)首先说下比赛的时候的做法(略复杂)：      
设dp[i][j]表示当前两个帐号分别为i,j(i<=j)分，要达到目的仍需参加比赛的期望      
if(i==j) dp[i][j]=1+p\*dp[i][j+1]+(1-p)\*dp[i-2][j];        
else  dp[i][j]=1+p\*dp[i+1][j]+(1-p)\*dp[i-2][j];        
构造一个20\*21个方程，时间复杂度O(6\*10^7)会超时，考虑到i<=j中间很多状态都没有用.
那么其实只需要构造(21+2)\*20/2-1=229个方程，时间复杂度O(8\*10^6),勉强可以接受,剩下的就直接高斯模板.     

2）赛后立马被喵呜鄙视了。我们的目的只是一个帐号+1就行了，那么只需要考虑一个号从0到20的期望的两倍.       
再减去一个号从19到20的期望就行。设dp[i]表示i分时达到目的的期望，dp[i]=1+p\*dp[i+1]+(1-p)\*dp[i-2].      
构造21个方程即可，时间复杂度O(8\*10^3),比上面小的多。        

#### E 
题意：已知三种天气对应四种湿度的概率，以及三种天气对应其第二天天气的概率.给出n天的湿度，问这n天最可能的天气        
思路：设dp[i][j]表示第i天天气为j的最大概率，dp[i][j]=max(dp[i-1][k]\*p[k][j]\*q[j][now]).      
表示从i-1天的某个天气k转变到j,而且当前湿度跟now一样的概率，记录路径。      

#### I
题意：m张牌，n次操作，每次操作选择x张牌翻转，求最后局势的方案数。      
思路：维护一个区间[l,r],l为当前被翻转的最小牌数，r为最多，这区间内的数肯定是l+2,l+4,..r都能取到.方案数为∑(C(m,i))    

<br>
     
### 2014.7.24 [contest 2](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%202&source=1&searchmode=source)    

1题 K ` rank106 ` 总体节奏是：签到题过了后，一人开一题，都没过，被虐的很惨
赛后补题：B，E，H，I    
     
### Code:[contest2](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest2)

A.ZCC Loves COT  差分法    
B.ZCC Loves Intersection  数学    
C.ZCC Loves RPG  词法分析器+线段树    
D.ZCC loves Army  Splay树     
E.ZCC loves cards  暴力+状态枚举    
F.ZCC loves Minecraft  正交凸包     
G.ZCC loves words  AC自动机+dp     
H.ZCC loves march  模拟+并查集     
I.ZCC loves ranklist  循环赛构造算法    
J.ZCC loves traffic lights  图伦    
K.ZCC Loves Codefires  贪心     

#### B
题意：D维空间，每维空间的值为[0,n-1],题目保证每条线段都平行于某一个轴，问D条线段相交的期望     
思路：一对相交的能量期望为1，故直接求概率      
D维空间里，两条平行于坐标轴的线段相交的充要条件是，某两维不一样，其余的D-2维都相同.所以期望为 C(D,2)\*1/N^(D-2)\*E(二维相交的概率),二维中线段总的方案数为 C(n,2)*n) 选出两条的方案是 (C(n,2)\*n)^2=n^4\*(n-1)^2/4    
对于相交的情况要枚举交点为(i,j)时，线段分别在该点或其某个方向时的所有情况：∑((i+1)\*(n-1-i)\*(j+1)\*(n-1-j) )=∑((i+1)\*(n-1-i))^2=(1/6\*n^3+1/2\*n^2-2/3\*n)^2      
化简后得：D\*(D-1)\*(n+4)^2/(18\*n^D)      
可以用java水过，也可以质因数分解，然后模拟大数乘法    

#### E
题意：n个数字选出k个并以某一种顺序围成圈，使得任意连续数字异或得到[L,R]的所有值，求能得到R的最大值。    
思路：二进制状态枚举选出的k个数字能否产生[L,ans]的所有数字，如果可以，再对k个数字进行全排列。    

#### H
题意：n(n<=10w)个点在m\*m(m<10^18)的平面内，两种操作，一种操作是把与当前点在同一行或同一列的数字,拉到这个点来，输出花费，另一种操作是移动某个点。
思路：比赛的时候没用并差集优化，超时。对于每个点，记录father,并记录该点有多少个相同的点，对其进行Q操作时，把与它同行同列的点的father更新为一个新的节点，并从行或者列上去掉编号，对于这样被Q后的点，下次进行Q的时候只移动father节点，就可以避免不必要的时间浪费    

#### I
题意：英语渣，自己翻译吧     
思路：第一问要构造k个n个全排列，使得每行的k个数字之和中的最大值和最小值之差最小。对于k是偶数的情况，可以使每两个数字之和都是n+1，保证差为0，对于k是奇数的情况，1的时候特判，大于1的奇数留三列，其余的同偶数处理，剩下的三列，第一列为1～n,第二列为i+n/2 or i-n/2,第三列则构造了。第二问，不会，看题解说是要求一个循环赛构造算法，然后我就YY了一下论文敲了个，然后Wa了，看标称多了四排代码，拷进去，AC了，懒得去理解题意了，就只看看这个YY吧。构造挺有趣的，第一行第一列赋值为[1,n],对角线赋值为1，然后开始填数字，依次顺着填，如果当前要填的数字和所在行一样，那么填n然后跳过该行直到n-1行，然后第n行的数字就看前面n-1行中没有出现过的那个数字了。     
例如 n=6 时：     

```
	  1 2 3 4 5 6
	  2 1 6 3 4 5
	  3 5 1 2 6 4
	  4 6 5 1 2 3
	  5 3 4 6 1 2
	  6 4 2 5 3 1
```

<br>

### 2014.7.29 [contest 3](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%203&source=1&searchmode=source)    

2题 EG ` rank168 ` 总体节奏是:小建建写线段树G，跪了12次后过了。大健健YY C题构造图的，比赛结束前没能过。我过了签到题E后就YY B，结果特么是网络流。感觉节奏不对啊，三场完全呈下降的趋势。已经不是第一次觉得比赛超出了我的认知，痛苦。
赛后补题：没啥我的题要补，A题做虾米啊，H题还没开始学啊，至于D题看了标称感觉不难，但是也没啥写的必要了。     

### Code: [contest3](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest3)

A.Endless Punishment  数学    
B.Redraw Beautiful Drawings  网络流    
C.Scary Path Finding Algorithm  构造题     
D.One to Four  分类讨论     
E.The Great Pan  水    
F.Defence of the Trees  状压+最短路    
G.Wow! Such Sequence!  线段树    
H.Mart Master  树形背包    
I.Paper  物理+计算几何+积分    
J.Minimal Spanning Tree  图+dp+矩阵     

<br>

### 2014.7.31 [contest 4](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%204&source=1&searchmode=source)

2题 EF ` rank86 ` 总体节奏是:小建建过了F线段树,然后大建建过了E，然后我在那水I，没水过，这场爆零了，0_0    
赛后补题：J    
I题赛后补了下平行四边形优化dp,但事实表明四边形优化在本题是不行的，没有满足优化的条件    

### Code: [contest4](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest4)

A.Little Devil I  LCT+树链剖分    
B.The Revenge of the Princess’ Knight 二分    
C.Hero meet devil  LCT      
D.NO ACM NO LIFE      
E.The Romantic Hero dp    
F.Nice boat  线段树     
G.The only survival  dp    
H.Hero’s assistant  几何+积分    
I.The Little Devil II  dp    
J.Our happy ending  状态dp    

###J
题意：求n个属于[0,L]的序列满足子序列和为k的方案数    
思路：由于n,k的范围比较小不超过20，那么枚举20内的答案就好,i∈[1,1<<20],对应第j位表示j已经出现在和内。对于当前新考虑的一位数，枚举所有状态i，可能到达的下一个状态是i|(1<<j)(1<=j<=L),也可能是i|(i<<j)(1<=j<=L)表示某部分的和加j.    

<br>

### 2014.8.5 [contest 5](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%205&source=1&searchmode=source)

3题 AIJ ` rank71 ` 总体节奏是:大建建过了A,然后我水了一下J，然后用java水了I，然后
大小建建搞E没能过，我和小建建搞C没能过，然后排名哗啦啦从两个小时一直掉到比赛结束
。     
赛后补题：E   小建建过掉了B ,G      

### Code: [contest5](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest5)

A.Inversion  逆序数    
B.Paths on the tree 树形dp+贪心    
C.Least common multiple  线段树    
D.Linear recursive sequence  FFT    
E.Parenthese sequence  预处理    
F.Count on the path  图论    
G.Permutation  拓扑排序+状态dp    
H.Query on the subtree  点分治+树状数组    
I.Exclusive or  数学    
J.Matrix multiplication  bitset+matrix     

### E
题意：一个含有'(',')','?'的字符串，看匹配是否唯一    
思路：预处理出每个位置左边最多和最少未能匹配的左括号，每个位置右边最多和最少未能匹配的右括号，当最少可能匹配的左括号等于右括号时才可能唯一。    

### I
题意：Sum(n)=∑(i\^(n-i)) (1<=i<=n-1)    
思路：    
if(n&1){ // k=(n-1)/2;    
Sum(n)=∑(i\^(n-i)) (1<=i<=n-1)    
=2\*∑(i\^(n-i)) (1<=i<=k)     
=2\*∑(2\*i\^(n-2\*i)) (1<=i<=k)     
=2\*∑(2\*i\^(2\*k+1-2\*i)) (1<=i<=k)     
=2\*∑(2\*i\^(2\*k-2\*i)+1) (1<=i<=k)    
=4\*∑(i\^(k-i))+2\*k (1<=i<=k)     
=4\*∑((i\^(k-i)))+4\*k+2\*k (1<=i<k)    
=4\*Sum(k)+6\*k    
}    
else{ // k=n/2;    
Sum(n)=∑(i\^(n-i)) (1<=i<=n-1)    
=2\*∑(i\^(n-i)) (1<=i<=k)    
=2\*(∑(2\*i\^(n-2\*i))(1<=i<=(k-1)/2) + ∑((2\*i-1)\^(n-1-2\*i))(1<=i<=k/2) )    
=2\*(∑(2\*i\^(n-2\*i))(1<=i<=(k-1)/2) + ∑((2\*i)\^(n-2-2\*i))(1<=i<=k/2-1) + 2\*(k-1) )     
=2\*(2\*∑(i\^(k-i))(1<=i<=(k-1)/2) + 2\*∑(i\^(n-1-i))(1<=i<=k/2-1) + 2\*(k-1))     
=2\*(Sum(k)+Sum(k-1)+2\*(k-1))     
}     

<br>


### 2014.8.7 [contest 6](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%206&source=1&searchmode=source)

3题 EGJ ` rank150 ` 总体节奏是:大小建建分别过了EG，然后他们一起过了J，然后我从头到尾都在那WA C题,贪心贪错了。。。    
赛后补题：C    

### Code: [contest6](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest6)

A.Map  DP?    
B.Hello, Your Package!  DP+Graph    
C.Room and Moor  贪心    
D.Football Manager  DP+KM    
E.Apple Tree  水题    
F.Robbery Plan  DP    
G.Series 1  组合数    
H.Series 2  YY    
I.Another Letter Tree  LCA+DP    
J.Fighting the Landlords  模拟    

### C
题意：已知一个01序列，求构造一个递增序列xi∈[0,1],使得差的平方和最小    
思路：对于某一段连续若干1再连续若干0的一定有所有的xi都相等，对于段与段之间的，因为要保证序列递增，每次可以找到一元二次函数的对称轴，如果取得到，xi取对称轴的值，如果取不到，就把当前段和前一段合并,取合并后的对称轴，直到前面没有其他段或者当前的对称轴大于前一段的对称轴。    

<br>

### 2014.8.12 [contest 7](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%207&source=1&searchmode=source)

3题 CEG ` rank122 ` 总体节奏是:大健健过了G，然后我在那贪心写E，跪了，然后大健健用dp搞E，Wa了好几次过了，小建建打表搞J没过，然后我去做C，是个数学题，最后几分钟才过，感觉这多校做的，被虐残了。    
赛后补题：J.    A实在搞不定，范围小点还好说，预处理不会搞。     

### Code: [contest7](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest7)

A.Prime Tree  数学+dp     
B.Rainbow Island  状态dp+高斯消元    
C.Lucky Number  数学    
D.Seeing People  ?    
E.Stupid Tower Defense  DP    
F.Destroy Transportation system  网络流    
G.Magical Forest  stl乱搞    
H.Game on S♂play  树的旋转+线段树    
I.K-th good string  后缀自动机    
J.FSF’s game  数学    

### C   

题意：求n(<=10^12)的数表示成x进制中只出现3,4,5,6的x的情况数    
思路：n表示成a0+a1\*x+a2\*x^2+...an\*x^n，其中a∈[3,6]    
其中n-a0=a1\*x+a2\*x^2+...an\*x^n=x\*(a1+a2\*x+...+an\*x^(n-1)),那么枚举n-3,n-4,n-5,n-6的约数x,并判断该a,x是否合法即可    

### E   
题意：n个塔，初始经过每个塔t秒，有红塔(经过时每秒x伤害)，绿塔(经过m个塔后累积每秒m\*y伤害)，蓝塔(每经过一个蓝塔，经过下一个塔时间加z)，求最大伤害。    
思路：首先贪心保证红塔一定放在最后，设dp[i][j]表示前i个中有j个蓝塔dp[i][j] = max( dp[i-1][j]+(t+j\*z)\*(i-1-j)\*y , dp[i-1][j-1]+(t+(j-1)\*z)\*(i-j)\*y ),特判全是红塔的伤害是 n\*x\*t    


### J   
题意：∑∑∑(i\*j/gcd(i/k,j/k)) (1<=i,j<=n,k|i,k|j)    
思路：设dp[i]=∑∑(i\*j/gcd(i/k,j/k)) (1<=j<=n,k|i,k|j)    
dp[i]=i\*∑∑(j/gcd(i/k,j/k)) (1<=j<=n,k|i,k|j)    
枚举gcd(i/k,j/k)为i的约数，j为gcd(i/k,j/k)的倍数，至多存在i/gcd(i/k,j/k)个这样的j，累积起来就是(i/gcd(i/k,j/k)+1)\*(i/gcd(i/k,j/k))/2,预处理     


<br>

### 2014.8.14 [contest 8](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%208&source=1&searchmode=source)

2题 FH ` rank165 ` 总体节奏是：大小建建过了F，H，然后我在那死磕A没过。之后他们搞B，我换成G，结果B没过，大建建说他G有想法，敲到最后都没过，比赛后面三个小时完全作死阿    
赛后补题：ABG     

### Code: [contest8](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest8)

A.2048  dp    
B.Area of Mushroom  凸包    
C.GCD Array  树状数组+数学    
D.Kingdom  图论    
E.Light 轮廓线dp    
F.Monster  签到题    
G.Multiplication table  找规律    
H.Number Transformation  数学    
I.Periodic Binary String  数学    
J.Permanent  FFT    
K.Tree  二分图    

### A
题意：已知n(<=10w)个数字(<=2048)的序列，对于每个子序列，每次能合并大小相同的数字，求满足子序列和能产生2048的子序列的个数    
思路：首先去掉非2的幂的数字，因为它对产生2048没有贡献。一个重要的结论，和超过2048的一定能够产生2048，那么我们只需要统计和没有超过2048的情况。分别统计2的幂出现了多少次，用dp[i][j]表示前i种数字(1,2,..,2^i)能够产生[j\*2^i,(j+1)\*2^i)间数的方案数 dp[i][j]=dp[i][j]+(dp[i-1][(j-k)/2]+dp[i-1][(j-k)/2+1])\*C(n,k)     

### B
题意：已知n个人的坐标和速度，坐标系中某点属于某个人当且仅当某个人严格地先到达这个点，求n个人中管辖范围无限大的    
思路：速度最大的人有可能有无限的管辖范围，除非他被速度一样的人包围或者在同一个位置上。求出速度最大的人凸包，然后在凸包上去掉位置重合的点。    

### G
题意：p进制的乘法表，(0~p-1)的数字都被调换成0~p-1的某个全排列，求乘法表的映射情况    
思路：找规律，出现次数最多的映射是0。对于每行，统计高位出现不同数字的频率，就是映射的数字。    

### H   
题意：k(<=10^10)次操作，第i次操作使x变成大于等于x，且为i的倍数    
思路：if x%i==0 x不变; else x=(x/i+1)\*i;     
令j=x/i，当j递减到某个程度时不会再减小，因为j=(i/(i+1)+1)\*j中随着i的增大，对j没有影响。     


<br>

### 2014.8.19 [contest 9](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%209&source=1&searchmode=source)

5题 ABFIK ` rank140 ` 总体节奏是：我看了下K题，感觉是个线段树啥的，赶紧让小建建去敲，结果他用树状数组过了，标称竟然是预处理，确实做复杂了；同时我暴力了一下I，过了；然后大建建把B过了；我和小建建在那找F的循环节，各种RE，TLE，然后我突然发现想复杂了，其实很水，敲了一下过了；然后大建建过了A；一直到比赛结束，我们都在做那个J题，公式写对了，积分求错了，其实也不是蛮难的式子，但确实忘干净了，伤心     

### Code: [contest9](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest9)

A.Another OCD Patient  DP    
B.Boring Sum   预处理    
C.Closed Paths   计算几何    
D.Dividing a String   ?    
E.Emmet   模拟    
F.Fast Matrix Calculation   矩阵    
G.GGS-DDU   最小树形图    
H.Handling the Past   线段树+离散化    
I.Improving the GPA   枚举    
J.Just a Joke   微积分    
K.Killing Monsters   预处理or树状数组    


<br>

### 2014.8.21 [contest 10](http://acm.hdu.edu.cn/search.php?field=problem&key=2014%20Multi-University%20Training%20Contest%2010&source=1&searchmode=source)

3题 CDE ` rank146 ` 总体节奏是：这场比赛在家里做的，大建建过了D，我看E是第二场还是第三场的某个题，遂改了改过了，小建建过的C题，线段树    

### Code: [contest10](https://github.com/shiyuan/acm/tree/master/2014%20Multi-University/contest10)

A.A simple brute force problem.   网络流    
B.A simple dynamic programming problem    
C.A simple simulation problem.    线段树    
D.A simple water problem     签到题    
E.A simple Gaussian elimination problem.   网络流    
F.A simple greedy problem.  贪心预处理+DP    
G.A simple problem from ZOJ.   计算几何    
H.A simple probability problem.  计算几何    
I.A simple math problem.   DLX    
J.A simple graph problem.   树形dp    


<br>
