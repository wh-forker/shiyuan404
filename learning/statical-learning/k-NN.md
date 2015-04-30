---
layout: page
title: K邻近法
permalink: /k-nn/
tags: learning
---

Description:

学习笔记：[《统计学习方法》](/tjxxff)第三章 \\(K\\)邻近法

<br>
### 1.\\(k\\)近邻算法
对于输入的训练数据集，找到与该实例\\(x\\)最近的\\(k\\)个实例，在涵盖这\\(k\\)个实例的邻域中根据分类决策规则决定\\(x\\)的类别\\(y\\)：
$$
y = arg \max\sum_{x_i \in N_k(x)} I(y_i=c_j)
$$

<br>
### 2.\\(k\\)近邻模型
距离度量：\\(L_p\\)距离    
$$
L_p(x_i,x_j)=(\sum_{l=1}^{n}(|x_i^{(l)}-y_i^{(l)}|)^{p})^{\frac{1}{p}}
$$
\\(k\\)值的选择：取值太小，估计误差很大，近似误差很小，模型越复杂，容易过拟合；取值太大，估计误差很小，近似误差很大，模型更简单    
分类决策规则：多数表决规则等价于经验风险最小化：    

$$
误分类率 \frac{\sum\_{x_i \in N_k(x)}I(y_i \neq c_j)}{k} = 1- \frac{\sum_{x_i \in N_k(x)} I(y_i=c_j)}{k}
$$    

$$     
so  arg \max\sum_{x_i \in N_k(x)} I(y_i=c_j)
$$

<br>
### 3.\\(kd\\)树
\\(kd\\)树是对\\(k\\)维空间中点的实例进行存储并以进行快速存储的二叉树，建树就是不断对\\(k\\)维空间进行切分，生成子节点。    

构造平衡\\(kd\\)树：    
选取方差最大的维度，找到中位数上的节点，由该节点对应的超矩形区域切分成两个子区域，建立儿子节点，递推    

\\(kd\\)树最近邻搜索：    
目标：\\(x\\)的最近邻    
(1)找到包含\\(x\\)的叶节点,并使之为当前最近节点\\(y\\)    
(2)递归：若父节点或者父节点的子节点与圆(以x为圆心，\\(Dist(x,y)\\)为半径)不相交，则往上回退；否则递归子节点进行最邻近算法    

\\(hdu4347\\)就是一个模板题：求\\(k\\)维空间输入点集中距离\\(x\\)最近的\\(m\\)个点的坐标    

{% highlight c %}
// kuangbin 的模板
// 这里对切分的维度选择没有按照计算方差来选择，而是依次选择切分
#include <cstdio>
#include <iostream>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <queue>
using namespace std;
const int DIM = 10;
const int MAXN = 50010;
double sq(double x){
    return x*x;
}
namespace KDTree{
    int K;
    struct Point{
        int x[DIM];
        double dist(const Point &b)const{
            double ans=0;
            for(int i=0;i<K;i++){
                ans+=sq(x[i]-b.x[i]);
            }
            return ans;
        }
        void input(){
            for(int i=0;i<K;i++){
                scanf("%d",&x[i]);
            }
        }
        void output(){
            for(int i=0;i<K;i++){
                printf("%d%c",x[i],i<K-1?' ':'\n');
            }
        }
    };
    struct qnode{
        Point p;
        double dis;
        qnode(){}
        qnode(Point _p,double _dis){
            p=_p;
            dis=_dis;
        }
        bool operator <(const qnode &b)const{
            return dis<b.dis;
        }
    };
    priority_queue<qnode>q;
    struct cmpx{
        int div;
        cmpx(const int &_div){
           div=_div;
        }
        bool operator()(const Point &a,const Point &b){
            for(int i=0;i<K;i++){
                if(a.x[(div+i)%K]!=b.x[(div+K)%K])
                    return a.x[(div+K)%K]<b.x[(div+K)%K];
            }
            return true;
        }
    };
    bool cmp(const Point &a,const Point &b,int div){
        cmpx cp=cmpx(div);
        return cp(a,b);
    }
    struct Node{
        Point e;
        Node *lc,*rc;
        int div;
    }pool[MAXN],*tail,*root;
    void init(){
        tail=pool;
    }
    Node* build(Point *a,int l,int r,int div){
        if(l>=r) return NULL;
        Node *p = tail++;
        p->div = div;
        int mid =(l+r)>>1;
        nth_element(a+l,a+mid,a+r,cmpx(div));
        p->e = a[mid];
        p->lc = build(a,l,mid,(div+1)%K);
        p->rc = build(a,mid+1,r,(div+1)%K);
        return p;
    }
    void search(Point p,Node *x,int div,int m){
		if(!x)return;
		if(cmp(p,x->e,div)){
			search(p,x->lc,(div+1)%K,m);
			if(q.size()<m){
				q.push(qnode(x->e,p.dist(x->e)));
				search(p,x->rc,(div+1)%K,m);
			}
			else {
				if(p.dist(x->e)<q.top().dis){
					q.pop();
					q.push(qnode(x->e,p.dist(x->e)));
				}
				if(sq(x->e.x[div]-p.x[div])<q.top().dis)
					search(p,x->rc,(div+1)%K,m);
			}
		}
		else {
			search(p,x->rc,(div+1)%K,m);
			if(q.size()<m){
				q.push(qnode(x->e,p.dist(x->e)));
				search(p,x->lc,(div+1)%K,m);
			}
			else {
				if(p.dist(x->e)<q.top().dis){
					q.pop();
					q.push(qnode(x->e,p.dist(x->e)));
				}
				if(sq(x->e.x[div]-p.x[div])<q.top().dis)
					search(p,x->lc,(div+1)%K,m);
			}
		}
	}
	void search(Point p,int m){
	    while(!q.empty()){
            q.pop();
	    }
	    search(p,root,0,m);
	}
};
KDTree::Point p[MAXN];
int main(){
    int n,k,m;
    while(scanf("%d%d",&n,&k)==2){
        KDTree::K=k ;
        for(int i=0;i<n;i++){
            p[i].input();
        }
        KDTree::init();
        KDTree::root=KDTree::build(p,0,n,0);
        int Q;
        scanf("%d",&Q);
        KDTree::Point o;
        while(Q--){
            o.input();
            scanf("%d",&m);
            KDTree::search(o,m);
            printf("the closest %d points are:\n",m);
            int cnt=0;
            while(!KDTree::q.empty()){
                p[cnt++]=KDTree::q.top().p;
                KDTree::q.pop();
            }
            for(int i=0;i<m;i++){
                p[m-i-1].output();
            }
        }
    }
    return 0;
}
{% endhighlight %}
