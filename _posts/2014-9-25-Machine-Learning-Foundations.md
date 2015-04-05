---
layout: post
tags: learning 
---

<hr>

##1.The Learning Problem   

###learning:   
observations ---> learnig ---> skill   

###ML:
data ---> ML ---> improved performance measure    

###Key Essence of ML
1.exist some 'underlying pattern' to be learned    
2.but no programmable definition    
3.somehow there is data about the pattern    


###Applications of ML
1.Food    
data: Twitter data( word + location )    
skill: tell food poisoning likeliness of restaurant properly    
2.Clothing    
data: sales figures + client surveys    
skill: give good fashion recommendations to client    
3.Housing     
data: characteristics of buildings and their energy load    
skill: predict energy load of other buildings closely    
4.Transportation    
data: some traffic sign images and meanings    
skill: recognize traffic signs accurately    
5.Education    
data: students' records on quizzes on a Math tutoring system    
skill: predict whether a students can give a correct answer to another problem    
6.Recommender System    
data: how many users have rated some movies    
skill: predict how a user would rate an unrated movie    

###The learning Model:
1.unknown target function f: x --> y    
2.final hypothesis g ~ f    
3.learning algorithm A    
4.hypothesis set H   
       
use data to compute hypothesis g that approximates target f    

<br>
<hr>

##2.Learning to Answer Yes/No

perceptron(感知器)    

###Perceptron Learning Algorithm(PLA)    
Cyclic PLA:    
For t=0,1,...    
(1) find the next mistakes of wt called ( Xn(t), Yn(t))    
	sign( w(t)^T * Xn(t) ) != Yn(t)    
(2) correct the mistake by     
	w(t+1) = w(t) + Yn(t) * Xn(t)    
(Linear Separability)    

{% highlight c %}
// R^N维线性可分(PLA实现)
#include <cstdio>
#include <iostream>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <cstdlib>
#include <vector>
#include <ctime>
using namespace std;
#define N 4
double w[N+1];
double x[N+1];
vector<double>y;
vector<double>v[N+1];
int main(){
    freopen("in.txt","r",stdin);
    memset(w,0,sizeof w);
    y.clear();
    for(int i=0;i<=N;i++){
        v[i].clear();
    }
    while(scanf("%lf",&x[1])!=EOF){
        for(int i=2;i<=N;i++){
            scanf("%lf",&x[i]);
        }
        scanf("%lf",&x[0]);
        y.push_back(x[0]);
        x[0]=1; // set a threshold
        for(int i=0;i<=N;i++){
            v[i].push_back(x[i]);
        }
    }
    while(1){
        bool mistake=false;
        for(int i=0;i<v[0].size();i++){
            double now=0;
            for(int j=0;j<=N;j++){
                now+=w[j]*v[j][i];
            }
            if(now*y[i]>0) ;
            else{
                mistake=true;
                for(int j=0;j<=N;j++){
                    w[j]+=v[j][i]*y[i];
                }
                break;
            }
        }
        if(!mistake){
            break;
        }
    }
    for(int i=0;i<=N;i++){
        cout<<w[i]<<endl;
    }
    return 0;
}

{% endhighlight %}

###Pocket Algorithm(PA)    
Learning with Noisy Data    
assume 'little' noise: Yn=f(Xn) usually    
if so , g ~ f on D equals Yn=g(Xn) usually    

<br>
<hr>

##3.Types of Learning 

Supervised learning : every Xn comes with corresponding Yn    

Unsupervised learning : diverse , with possibly very different performance goals (clustering)    

Semi-supervised learning : leverage unlabeled data to avoid 'expensive' labeling      

Reinforcement Learning : learn with 'partial/implicit information' (often sequentially)        

Online : hypothesis 'improves' through receiving data instances sequentially        

Active Learning : Learning by 'Asking'    

<br>
<hr>

##4.Feasibility of Learning

###Hoeffding’s Inequality

In big sample,v is probably close to μ (within ε )    
P(|v-u|<ε)<=2e^(-2ε^2*N)    



<br>
<hr>
