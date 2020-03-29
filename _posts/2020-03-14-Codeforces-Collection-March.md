---
layout:    post   				    # 使用的布局（不需要改）
title:    「ACM-XCPC」Codeforces-Collection for March，2020(Updating)  # 标题 
subtitle:  Solution for Codeforces Round#627~629  #副标题
date:      2020-03-14 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img9.png        #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - ACM-XCPC
    - Codeforces
    - 题解
---



# Round#627 div3

我怀疑我被系统针对了但我没有证据：

![](/img/ACM_627.png)

~~（特地为了这事换了个头像 害）~~ 前1h一路a下来，后来cf官网和4个镜像全挂了再也没有上去过……大概是我的报应。这场最后两题都是dp……唔姆，我懂了我滚了（洛谷专题刷起来刷起来qwq

**[Contest Link](http://codeforces.com/contest/1324)**

**[Code Link](https://github.com/BBBoundary/ACM-XCPC_Wsystem9350/tree/master/Codeforces/Round%23627%20div3)**

### Problem E

> Vova had a pretty weird sleeping schedule. There are h hours in a day. Vova will sleep exactly n times. The i-th time he will sleep exactly after ai hours from the time he woke up. You can assume that Vova woke up exactly at the beginning of this story (the initial time is 0). Each time Vova sleeps **exactly one day** (in other words, h hours).
>
> Vova thinks that the 𝑖i-th sleeping time is **good** if he starts to sleep between hours l and r inclusive.
>
> Vova can control himself and before the i-th time can choose between two options: go to sleep after ai hours or after ai−1 hours.
>
> Your task is to say the maximum number of **good** sleeping times Vova can obtain if he acts optimally.

题意：一天共h小时，共有n个时刻且第零时刻为0h，每个时刻给出一个时间a[i]，表示当可以在当前醒来后的a[i]小时以后睡觉（每次都睡一天，即下一次醒来的时间即为上一次睡觉的时间），或a[i]-1小时后睡觉。现给出区间[l,r]，若睡觉时间在该区间内，则称为“优质睡眠”。求给出信息下优质睡眠可以达到的最大次数。

dp。利用dp[i][j]来存储转移状态，i表示第i个时刻，j表示第i时刻的开始时间，dp[i][j]即表示第i时刻第j小时睡觉时，第1时刻到第i时刻间的最佳答案。

需要进行初始化，对不合法的时间状态全部赋-INF（起始时唯一合法状态为dp[0][0]=0）。再对每个i的每个可能时刻j(0~h-1)进行遍历，其中对(j+a[i])%h和(j+a[i]-1+h)%h分别进行两次转移（注意j分开循环）。

### Problem F

> You are given a tree consisting of n vertices. A tree is a connected undirected graph with n−1 edges. Each vertex v of this tree has a color assigned to it (av=1 if the vertex v is white and 0 if the vertex v is black).
>
> You have to solve the following problem for each vertex v: what is the maximum difference between the number of white and the number of black vertices you can obtain if you choose some subtree of the given tree that **contains** the vertex v? The subtree of the tree is the connected subgraph of the given tree. More formally, if you choose the subtree that contains cntw white vertices and cntb black vertices, you have to maximize cntw−cntb.

题意：给出一棵树，每个结点均为白色或黑色。求对每个节点来说，在所有包括其的子树（按照题目描述为包括每个结点的子图，即不需要连到叶子节点上）中白色节点数-黑色节点数的最大值为多少。

dfs预处理+树形dp。由于为$cnt_w-cnt_b$，对于输入信息键入，并对a[i]==0（黑色）的结点令其a[i]=-1，则a[i]即可通过相加计算贡献值（结果要让贡献值越大越好）。

先令节点1为树的根节点，对树进行dfs预处理，存储该情况下每个结点【自己+子树】可以得到的最大收益值。（代码中我用fir[v]存储v结点的子树最大收益和+a[v]）

对于树形dp环节，由于此处对每个结点均需要求解，即为树换根问题。仍以节点1为原始根。此处可以脑补一下树换根的动态画面，如结点b，原本(预处理时)为a的子树，为c、d……的父结点，则b变为根节点时，b即可获得新增的a子树+原本的c、d……子树。则b结点的答案ans[b]即为ans[a]+fir[b]。**注意，dp至此处（past==a，now==b）前，需要先令ans[a]-=fir[b]，即要先除去前面a为根节点时ans[a]已经包含的b子树的答案，使此时的ans[a]完全为b所连接的子树a的情况，在b对当前原有子树dp完成后再将ans[a]加回来**。




# Round#628 div2

因为家里网的原因 比赛前一直上不去就没参加……（然后第二天把路由器搬到房间里来了果然舒服多了（x

目前先补了四题，打算先把前几场div2的题补起来，空下来以后再补上E、F两题。

**[Contest Link](http://codeforces.com/contest/1325)**

**[Code Link](https://github.com/BBBoundary/ACM-XCPC_Wsystem9350/tree/master/Codeforces/Round%23628%20div2)**

### A- EhAb AnD gCd

题目：

> You are given a positive integer *x*. Find **any** such 2 positive integers *a* and *b* such that **GCD**(*a*,*b*)+**LCM**(*a*,*b*)=*x*. It's guaranteed that the solution always exists. If there are several such pairs (*a*,*b*), you can output any of them.

思路：签到题。给出m，求两个数x,y，使gcd(x,y)+lcm(x,y）=m。

对m的奇偶性分开讨论：①当m==2时，令x=1，y=1 (特判)；②当m为奇数时，令x=1,y=m-1 ②当m为大于2的偶数时，令x=2，y=m-2；

### B- CopyCopyCopyCopyCopy

题目：

> Ehab has an array 𝑎 of length n. He has just enough free time to make a new array consisting of 𝑛 copies of the old array, written back-to-back. What will be the length of the new array's longest increasing subsequence?
>
> A sequence 𝑎a is a subsequence of an array b if a can be obtained from 𝑏 by deletion of several (possibly, zero or all) elements. The longest increasing subsequence of an array is the longest subsequence such that its elements are ordered in strictly increasing order.

思路：签到题。对于一个长度为n的数组将其重复n遍，求最长严格递增的子序列（可以不连续）。

由于重复了n遍，即原本数组中的各个元素均可以因被取于不同位置的新数组而相对位置任意。故仅需在第一轮中取最小、第二轮中取第二小……即可。考虑到严格递增，所以需要一个set过滤掉重复元素，剩下的元素个数即为答案。

### C- Ehab and Path-etic MEXs

题目：

> You are given a tree consisting of 𝑛n nodes. You want to write some labels on the tree's edges such that the following conditions hold:
>
> - Every label is an integer between 0 and n−2 inclusive. 
> - All the written labels are distinct. 
> - The largest value among 𝑀𝐸𝑋(𝑢,𝑣) over all pairs of nodes (𝑢,𝑣)  is as small as possible. 
>
> Here, 𝑀𝐸𝑋(𝑢,𝑣) denotes the smallest non-negative integer that isn't written on any edge on the unique simple path from node u to node v.

思路：数据结构思维题。要使所有对MEX(u,v）中的最大值最小，首先情感上分析，应尽量不让一条路径上聚集连续的0,1,2……，应让小数字尽量地分散到各个路径上。

该题需要对【度】有较深的理解。对于一条路径，其经过一点则会联系到该点的两条关联边，即该点的度至少为2（叶子结点除外，叶子结点必然是始末点）。又由于MEX为求不出现在集合内的最小非负数，即需要从0,1……开始考虑，故此处分两类讨论：

①若该结构为链，即度为1的结点仅有两个，则该两个叶子结点形成的路径必然决定了最终$MEX_{max}==n-1$，故可以随意对边进行赋值。

②若该结构不为链，即至少有一个结点的度大于等于三，则选取该结点，将其关联边从0,1,……开始编号。对于不经过该点的路径，其MEX==0；对于经过该点的路径，其MEX<=2（最大的为MEX==2时，此时同时经过0、1两条边）。剩下的边可以随意赋值。

### D- Ehab the Xorcist

题目：

> Given 2 integers u and v, find the shortest array such that [bitwise-xor](https://en.wikipedia.org/wiki/Bitwise_operation#XOR) of its elements is u, and the sum of its elements is v.

思路：位运算思维题。给出两个数u和v，求能满足所有元素的异或值为u、和为v的数组的最短长度为多少，并输出该数组。

①首先分析不存在的情况：根据求异或、求和的位运算规则，易得u>v或者(u^v)&1==1时无解。

②当u==v时，仅需要一个元素，即u(v)本身。注意题目中第四组样例指出需要对u==0的情况进行特判。

③从v的角度出发，利用u，由于u^0=u, 而两个相同数做异或运算的结果即为0，即令x=(v-u)>>1，则会有u^x^x==v，此种情况为3项。但由于当(v-u)为奇数时x失真，则此时考虑合并u^x为一项(有位运算分析可得u^x==(v+u)>>1)，x为另一项，则此时为2项。

——————————

另外，这只傻逼一开始看错题目以为是固定输出两项，其他情况判为无解，~~然后这个人在那里乐呵呵按照情况分类讨论，还把具体实现出来了嘤嘤嘤~~。不过我觉得还是有记录价值的orz

对u、v逐位按位分析：

![](/img/ACM_628.png)

[Code](https://pasteme.cn/30327)  Password: WorldFianl2020




# Round#629 div3

毒瘤D题我吐了调了一万年然后还是没把坑全踩遍，虽然我是傻逼，但是题目更傻逼，让我这个傻逼也做不出来（确信

好叭行了就是我菜（嘤嘤嘤

**[Contest Link](http://codeforces.com/contest/1328)**

**[Code Link](https://github.com/BBBoundary/ACM-XCPC_Wsystem9350/tree/master/Codeforces/Round%23629%20div3)**

### D-Carousel

题目：

> The round carousel consists of n figures of animals. Figures are numbered from 1 to n in order of the carousel moving. Thus, after the n-th figure the figure with the number 1 follows. Each figure has its own type — the type of the animal corresponding to this figure (the horse, the tiger and so on). The type of animal of the i-th figure equals ti.
>
> You want to color each figure in one of the colors. You think that it's boring if the carousel contains two different figures (with the distinct types of animals) going one right after another and colored in the same color.
>
> Your task is to color the figures in such a way that the number of distinct colors used is the minimum possible and there are no figures of the different types going one right after another and colored in the same color. If you use exactly 𝑘k distinct colors, then the colors of figures should be denoted with integers from 1 to k.

题意：一个旋转木马代表的从1到n的序列中，相同的数字代表同一种动物，现在要给每个动物上色，要求相邻的若是不同的动物则不能同色，求问最少需要几种颜色。

思维题思维题思维题但我人傻了想复杂了呜呜呜呜qwq

讨论情况：

①若是偶数个则直接 1 2 1 2 …… 2

②若是奇数个：

​	a. 若第一个等于最后一个：1 2 1 2 …… 1

​	b. 若第一个不等于最后一个：

​		若前面有连续的两个值相等，则：1 2 1 2 2(指相等的两个，也有可能是1 1) 1 …… 2

​		反之则：1 2 1 2 ……2 3



### E-Tree Queries

题目：

> You are given a rooted tree consisting of n vertices numbered from 1 to n. The root of the tree is a vertex number 1.
>
> A tree is a connected undirected graph with n−1 edges.
>
> You are given m queries. The i-th query consists of the set of ki distinct vertices vi[1],vi[2],…,vi[ki]. Your task is to say if there is a path from the root to some vertex 𝑢u such that each of the given k vertices is either belongs to this path or has the distance 1 to some vertex of this path.

题意：给出一棵共有n个结点的树，共有m个询问，每个询问包含q个点，求该树上是否存在一条路径，使得这q个到该条路径的距离不大于1.

倍增lca裸题。（赛后补题的时候觉得好简单啊qwq我是什么傻逼要磕D题）

1）对于给出的树先要处理好每个结点的信息，包括①每个结点的深度②每个结点的倍增父结点。

关于倍增，其实这是一个非常在算法中非常常用的思想，由于一个一个遍历太慢，所以利用每个数的二进制表示，并每次减掉2的幂次大小，使得寻找父结点的工作能够压缩至o(logn)内完成。这里令f[i][j]即表示第i个结点的第2^j个父结点，则有

$$
f[i][j]=f[f[i][j-1]][j-1]
$$

2）对于询问的一个结点集，先找出其中深度最深的结点，若路径存在，则必定是从根节点向该结点的过程路径。找出该结点las后，找出每个结点v与该节点的最小公共祖先lca，然后判断该祖先lca与结点v的深度差是否小于等于1即可。

寻找lca的方法（设寻找a与b两个结点的lca）：将a设为其中深度较大的那个，然后令a向上寻找祖先直至与b的深度相同，然后a与b一起向上找祖先（即步长相同），直至找到最近的一个。



