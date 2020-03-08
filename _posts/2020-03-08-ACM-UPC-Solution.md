---
layout:    post   				    # 使用的布局（不需要改）
title:    【ACM-XCPC】2020-UPC新生赛题解  # 标题 
subtitle:  又名《这个菜鸡以为21:30就结束了于是停了半个小时真是丢死人了》   #副标题
date:      2020-03-08 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img9.png        #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - ACM-XCPC
    - algorithm
    - 题解
---



**[Contest Address](http://acm.upc.edu.cn/OnlineJudge/contest.php?cid=1007)**

我就是一个贪心都要wa五发，kmp手写要写二十分钟，并查集最后find(i)会写成i，A题O(n^3)刚写完比赛就结束连卡过去的机会都没有，博弈题还没来得及看的智障qwqrz  这次补三题qwq

事实证明偷懒不训练就是得挨打 不敢了不敢了呜呜呜

## Problem A

[Problem Link](http://acm.upc.edu.cn/OnlineJudge/problem.php?cid=1007&pid=0)

思路：区间dp

代码中设dp(i,j)为顶点i到顶点j的最优答案，显然当j==i或j==i+1时为0（不合法），故遍历时j需要从i+2开始。此处引入在i、j中的点k，则有：
$$
dp[i][j]=dp[i][k]+dp[k][j]+cost(i,k,j)
$$


，其中dp(i,k)、dp(k,j)为子问题，cost(i,k,j)即为由i、j、k三顶点组成的三角形的贡献值。最终dp(1,n)即为答案。

需要注意的是dp时，i需要从后往前遍历，由于最后答案i==1，即当遍历到i==1时需要用到其他状态的信息。同理，j、k需要从前往后遍历。AC代码如下：

```c++
#include<bits/stdc++.h>
#define INF 0x3f3f3f
using namespace std;
typedef long long ll;

int n;
int a[1010];
int dp[1010][1010];

void solve(){
    memset(dp,0,sizeof(dp));
    for(int i=n-1;i>=1;i--){
        for(int j=i+2;j<=n;j++){
            for(int k=i+1;k<=j-1;k++){
                dp[i][j]=max(dp[i][j],dp[i][k]+dp[k][j]+a[i]*a[k]*a[j]);
            }
        }
    }
}

int main(){
    int t;
    scanf("%d",&t);
    for(int k=1;k<=t;k++){
        scanf("%d",&n);
        for(int i=1;i<=n;i++) scanf("%d",&a[i]);
        solve();
        printf("Case #%d: %d\n",k,dp[1][n]);
    }
}

```



## Problem K

[problem link]()

思路：组合数公式:
$$
c[i][j]=c[i-1][j]+c[i-1][j-1]
$$
，并进行预处理操作。

先对n、m均2e3的范围进行预处理。又由于题目为求个数问题且时多组询问，需要将结果离线值存储。此处通过存二维前缀和：
$$
s[i][j]=s[i-1][j]+s[i][j-1]-s[i-1][j-1];\\
if(c[i][j]==0)\ \  s[i][j]+=1;
$$
如此通过n->i,m->j，在计算c数组时保证j<=i,在利用s数组时保证j<=m，使查询复杂度变为O(1)。

参考代码：

```C++
#include<bits/stdc++.h>
#define INF 0x3f3f3f
using namespace std;
typedef long long ll;

int n,m,g;
int c[2010][2010],s[2010][2010];

void initc(){
    c[1][1]=1;
    for(int i=0;i<=2000;i++) c[i][0]=1;
    for(int i=2;i<=2000;i++){
        for(int j=1;j<=i;j++){
            c[i][j]=(c[i-1][j-1]+c[i-1][j])%g;
        }
    }
}

void inits(){
    for(int i=2;i<=2000;i++){
        for(int j=1;j<=i;j++){
            s[i][j]=s[i-1][j]+s[i][j-1]-s[i-1][j-1];
            if(c[i][j]==0) s[i][j]++;
        }
    }
}

int main(){
    int t;
    scanf("%d%d",&t,&g);
    initc();
    inits();
    while(t--){
        scanf("%d%d",&n,&m);
        printf("%d\n",s[n][m]);
    }
}

```



## Problem H

[Problem Link](http://acm.upc.edu.cn/OnlineJudge/problem.php?cid=1008&pid=7)

~~得，现在oj关了没法交题了明天再改~~