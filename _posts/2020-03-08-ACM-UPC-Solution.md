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

我就是一个贪心都要wa五发，kmp手写要写二十分钟，并查集最后find(i)会写成i，A题O(n^3)刚写完比赛就结束连卡过去的机会都没有，博弈题还没来得及看的智障qwqrz

事实证明偷懒不训练就是得挨打 不敢了不敢了呜呜呜

# Part I

这部分是一定要补的题，相应的专题也需要多刷题。

### Problem A

[Problem Link](http://acm.upc.edu.cn/OnlineJudge/problem.php?cid=1007&pid=0)

思路：**区间dp**

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



### Problem K

[problem link]()

思路：**组合数公式**:
$$
c[i][j]=c[i-1][j]+c[i-1][j-1]
$$
，并进行预处理操作。

先对n、m均2e3的范围进行预处理。又由于题目为求个数问题且时多组询问，需要将结果离线值存储。此处通过存**二维前缀和**：


$$
s[i][j]=s[i-1][j]+s[i][j-1]-s[i-1][j-1];\\
if(c[i][j]==0)\ \  s[i][j]+=1;\\
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
      	s[i][i+1]=s[i][i]
    }
}

int main(){
    int t;
    scanf("%d%d",&t,&g);
    initc();
    inits();
    while(t--){
        scanf("%d%d",&n,&m);
      	if(n<m) m=n;
        printf("%d\n",s[n][m]);
    }
}

```



### Problem H

[Problem Link](http://acm.upc.edu.cn/OnlineJudge/problem.php?cid=1008&pid=7)

~~得，现在oj关了没法交题了明天再改~~



# Part II

这部分是过掉的题，做一个存档记录。

### Problem B

[Problem Link](http://acm.upc.edu.cn/OnlineJudge/problem.php?cid=1007&pid=1)

傻逼签到题，但更傻逼的是我wa了五发（人间迷惑行为）。就是一个贪心问题，价值密度越大越前面。

当然我是直接在sort的cmp函数部分设为：
$$
return\ \ a.v*a.t+b.v*(a.t+b.t)<b.v*b.t+a.v*(b.t+a.t)
$$
，也就是两两比较让取结果好的排法。



### Problem D

[Problem Link](http://acm.upc.edu.cn/OnlineJudge/problem.php?cid=1007&pid=3)

字符串kmp签到题。是我B题wa了五发后转战的题，然后这题a了以后回去把B题a掉了……就是套了个kmp的板子然后主函数部分稍微写写就过。不过要注意对“00000”字符串的特判，由于题目给出条件是小于该大数的。

存一下自己这场用的板子：

```c++
char T[1e6+10],P[1e6+10];
int f[1e6+10];
int cnt;		//主串中的子串个数

void find(char *T,char *P,int *f)
{
    int n=strlen(T);
    int m=strlen(P);
    int j=0;
    for(int i=0; i<n; i++)
    {
        if(j && T[i]!=P[j]) j=f[j];
        if(T[i]==P[j]) j++;
        if(j==m) {cnt++;j=f[j];}		//注意这里子串不能重叠，需要j=f[j]
    }
}
void getFail(char *P,int *f)
{
    int m =strlen(P);
    f[0]=f[1]=0;
    for(int i=1; i<m; i++)
    {
        int j=f[i];
        while(j && P[i]!=P[j]) j=f[j];
        f[i+1] = P[i]==P[j]?j+1:0;
    }
}
int main()
{
  	cin>>T;
  	getFail(P,f);
		find(T,P,f);
		cout<<cnt;
}

```



### Problem F

[Problem Link](http://acm.upc.edu.cn/OnlineJudge/problem.php?cid=1007&pid=5)

比完发现是POJ原题。不过也算是一道非常基础的矩阵快速幂求和问题。

```c++
#include<bits/stdc++.h>
#define INF 0x3f3f3f
using namespace std;
typedef long long ll;

int n;
ll m,mod=1e9+7;
struct Mat{
    long long m[40][40];
};
Mat a,per;

void init(){
    for(int i=0; i<n; i++)
        for(int j=0;j<n; j++){
            scanf("%lld",&a.m[i][j]);
            a.m[i][j]%=mod;
            per.m[i][j]=(i==j);
        }
}

Mat mul(Mat A,Mat B){
    Mat ans;
    for(int i=0; i<n; i++)
        for(int j=0; j<n; j++){
            ans.m[i][j]=0;
            for(int k=0; k<n; k++)
                ans.m[i][j]=(ans.m[i][j]+A.m[i][k]*B.m[k][j]%mod)%mod;
            ans.m[i][j]%=mod;
        }
    return ans;
}

Mat power(ll k){
    Mat p,ans=per;
    p=a;
    while(k){
        if(k&1){
            ans=mul(ans,p);
            k--;
        }
        else{
            k/=2;
            p=mul(p,p);
        }
    }
    return ans;
}

Mat add(Mat a,Mat b){
    Mat c;
    for(int i=0; i<n; i++)
        for(int j=0; j<n; j++)
            c.m[i][j]=(a.m[i][j]+b.m[i][j])%mod;
    return c;
}

Mat sum(ll k){
    if(k==1) return a;
    Mat temp,b;
    temp=sum(k/2);
    if(k&1){
        b=power(k/2+1);
        temp=add(temp,mul(temp,b));
        temp=add(temp,b);
    }
    else{
        b=power(k/2);
        temp=add(temp,mul(temp,b));
    }
    return temp;
}

int main(){
    while(cin>>n>>m){
        init();
        Mat ans=sum(m);
        for(int i=0;i<n;i++){
            for(int j=0;j<n-1;j++)
                printf("%lld ",(ans.m[i][j]+mod)%mod);
            printf("%lld\n",(ans.m[i][n-1]+mod)%mod);
        }
    }
    return 0;
}

```

另外贴一下郑老师@Edwiv的构造qwq学到了学到了

![](img/ACM-UCP-F.jpg)

### Problem I

[Problem Link](http://acm.upc.edu.cn/OnlineJudge/problem.php?cid=1007&pid=8)

并查集裸题。本来看到题目描述感觉这BFS我稳了，然后看到坐标范围1e9，哦那没事了。

不过点的数量范围仅在1e3，就非常容易想到n^2的枚举方法，并用并查集维护星区的分类信息。
