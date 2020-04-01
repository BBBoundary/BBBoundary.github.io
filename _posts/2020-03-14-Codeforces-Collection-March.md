---
layout:    post   				    # 使用的布局（不需要改）
title:    「ACM-XCPC」Codeforces-Collection for March,2020  # 标题 
subtitle:  Solution for Codeforces Round#627~630  #副标题
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

## E

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

## F

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

## A

题目：

> You are given a positive integer *x*. Find **any** such 2 positive integers *a* and *b* such that **GCD**(*a*,*b*)+**LCM**(*a*,*b*)=*x*. It's guaranteed that the solution always exists. If there are several such pairs (*a*,*b*), you can output any of them.

思路：签到题。给出m，求两个数x,y，使gcd(x,y)+lcm(x,y）=m。

对m的奇偶性分开讨论：①当m==2时，令x=1，y=1 (特判)；②当m为奇数时，令x=1,y=m-1 ②当m为大于2的偶数时，令x=2，y=m-2；

## B

题目：

> Ehab has an array 𝑎 of length n. He has just enough free time to make a new array consisting of 𝑛 copies of the old array, written back-to-back. What will be the length of the new array's longest increasing subsequence?
>
> A sequence 𝑎a is a subsequence of an array b if a can be obtained from 𝑏 by deletion of several (possibly, zero or all) elements. The longest increasing subsequence of an array is the longest subsequence such that its elements are ordered in strictly increasing order.

思路：签到题。对于一个长度为n的数组将其重复n遍，求最长严格递增的子序列（可以不连续）。

由于重复了n遍，即原本数组中的各个元素均可以因被取于不同位置的新数组而相对位置任意。故仅需在第一轮中取最小、第二轮中取第二小……即可。考虑到严格递增，所以需要一个set过滤掉重复元素，剩下的元素个数即为答案。

## C

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

## D

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

## D

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

## E

题目：

> You are given a rooted tree consisting of n vertices numbered from 1 to n. The root of the tree is a vertex number 1.
>
> A tree is a connected undirected graph with n−1 edges.
>
> You are given m queries. The i-th query consists of the set of ki distinct vertices vi[1],vi[2],…,vi[ki]. Your task is to say if there is a path from the root to some vertex u such that each of the given k vertices is either belongs to this path or has the distance 1 to some vertex of this path.

题意：给出一棵共有n个结点的树，共有m个询问，每个询问包含q个点，求该树上是否存在一条路径，使得这q个到该条路径的距离不大于1.

倍增lca裸题。（赛后补题的时候觉得好简单啊qwq我是什么傻逼要磕D题）

1）对于给出的树先要处理好每个结点的信息，包括①每个结点的深度②每个结点的倍增父结点。

关于倍增，其实这是一个非常在算法中非常常用的思想，由于一个一个遍历太慢，所以利用每个数的二进制表示，并每次减掉2的幂次大小，使得寻找父结点的工作能够压缩至o(logn)内完成。这里令f[i][j]即表示第i个结点的第2^j个父结点，则有
$$
f[i][j]=f[f[i][j-1]][j-1]
$$


2）对于询问的一个结点集，先找出其中深度最深的结点，若路径存在，则必定是从根节点向该结点的过程路径。找出该结点las后，找出每个结点v与该节点的最小公共祖先lca，然后判断该祖先lca与结点v的深度差是否小于等于1即可。

寻找lca的方法（设寻找a与b两个结点的lca）：将a设为其中深度较大的那个，然后令a向上寻找祖先直至与b的深度相同，然后a与b一起向上找祖先（即步长相同），直至找到最近的一个。



## F

题目：

> You are given the array a consisting of n elements and the integer k≤n.
>
> You want to obtain **at least** k equal elements in the array a. In one move, you can make one of the following two operations:
>
> - Take **one** of the minimum elements of the array and increase its value by one (more formally, if the minimum value of a is mn then you choose such index i that ai=mn and set ai:=ai+1); 
> - take **one** of the maximum elements of the array and decrease its value by one (more formally, if the maximum value of a is mx then you choose such index i that ai=mx and set ai:=ai−1). 
>
> Your task is to calculate the minimum number of moves required to obtain **at least** k equal elements in the array.

题意：给出n个数，需要使其中至少k个数相等，每次的操作为将最大的数-1，或将最小的数+1，问最少操作几次。

思路：易证最后的k个数必定是原数组中存在的数（此处不作证明），此题实际上是模拟数组两头均向某数靠拢的过程，对每个数进行操作，并最终取最小。先对数组升序排序，存下前缀和（即可得到后缀和）。

对于结果是0的情况：先对每个数进行计数，若某个数的个数超过k则直接输出0，return；

假设对于第i个数$$a[i]$$:

1）将其前面的a[1]、a[2]……a[i-1]转化为a[i],则有：


$$
SumLeft=a[i]-a[1]+a[i]-a[2]……a[i]-a[i-1]\\=i*a[i]-\sum_{j=1}^ia[j]
$$


①若i>=k，对于前i-1个中仅需k-1个即可成立，即当a[1]……a[i-1]全部转化为a[i]后，仅需要k-1个再变成a[i]即可，故将n-k个退化为a[i-1]。②若i<k，则该种情况(k个全部由左半部分提供)不成立，不计入最终情况。

2）将其后面的a[i+1]、a[i+2]……a[n]转化为a[i],则有：


$$
SumRight=a[n]-a[i]+a[n-1]-a[i]+……a[i+1]-a[i]\\=\sum_{j=i}^na[j]-(n-i+1)*a[i]
$$


①若n-i+1>=k，对于后n-i中仅需k-1个即可成立，即当a[i+1]……a[n]全部转化为a[i]，后，仅需k-1个再变成a[i],故将n-i-k+1个退化为a[i+1]。②若n-i+1<k，则该种情况(k个全部由右半部分提供)不成立，不计入最终情况。

3）对于两边都取的情况，即将上述左右两边SumLeft、SumRight合并，且因为左边取了包括a[i]的k个，右边取了包括a[i]的k个，故两边共取k个的情况为


$$
Sum=SumLeft+SumRight-k+1\\=(2i+n-1)*a[i]-\sum_{j=1}^ia[j]+\sum_{j=i}^na[j]-(n-k)
$$


# Round#630 div2

交了A题以后再也没有流畅的交过题 / 果然雨天晚上不适合打cf / 难得的两个小时半的比赛 被家里网演了俩小时是真的难受。

**[Contest Link](http://codeforces.com/contest/1332)**

**[Code Link](https://github.com/BBBoundary/ACM-XCPC_Wsystem9350/tree/master/Codeforces/Round%23630%20div2)**



## A

题目：

> Alice has a cute cat. To keep her cat fit, Alice wants to design an exercising walk for her cat! 
>
> Initially, Alice's cat is located in a cell (𝑥,𝑦)(x,y) of an infinite grid. According to Alice's theory, cat needs to move: 
>
> - exactly a steps left: from (u,v) to (u−1,v); 
> - exactly b steps right: from (u,v) to (u+1,v); 
> - exactly c steps down: from (u,v) to (u,v−1); 
> - exactly d steps up: from (u,v) to (u,v+1). 
>
> Note that the moves can be performed in an **arbitrary order**. For example, if the cat has to move 1 step left, 3 steps right and 2 steps down, then the walk right, down, left, right, right, down is valid.
>
> Alice, however, is worrying that her cat might get lost if it moves far away from her. So she hopes that her cat is **always** in the area [𝑥1,𝑥2]×[y1,y2], i.e. for every cat's position (u,v) of a walk x1≤u≤x2 and y1≤v≤y2 holds.
>
> Also, note that the cat can visit the same cell multiple times.
>
> Can you help Alice find out if there exists a walk satisfying her wishes?
>
> Formally, the walk should contain exactly a+b+c+d unit moves (a to the left, b to the right, c to the down, d to the up). Alice can do the moves in **any** order. Her current position (u,v) should **always** satisfy the constraints: x1≤u≤x2, y1≤v≤y2. The staring point is (x,y).
>
> You are required to answer t test cases **independently**.

题意：给定小猫的初始坐标和可以活动的范围，以及所有的动作指令，可以任意搭配动作顺序，求这些是否有一种解法可以令小猫的活动不会超出给定范围。

签到题。先对活动范围的x轴、y轴方向上能否有活动进行判断，再消除【上下左右】、【上下】、【左右】的抵消影响，再进行判断。



## B

题目：

> A positive integer is called *composite* if it can be represented as a product of two positive integers, both greater than 1. For example, the following numbers are composite: 6, 4, 120, 27. The following numbers aren't: 1, 2, 3, 17, 97.
>
> Alice is given a sequence of n composite numbers a1,a2,…,an.
>
> She wants to choose an integer m≤11 and color each element one of m colors from 11 to m so that:
>
> - for each color from 1 to m there is at least one element of this color; 
> - each element is colored and colored exactly one color; 
> - the greatest common divisor of any two elements that are colored the same color is greater than 1, i.e. gcd(ai,aj)>1 for each pair i,j if these elements are colored the same color. 
>
> Note that equal elements can be colored different colors — you just have to choose one of m colors for each of the indices from 1 to n.
>
> Alice showed already that if all ai≤1000 then she can always solve the task by choosing some m≤11.
>
> Help Alice to find the required coloring. Note that you don't have to minimize or maximize the number of colors, you just have to find the solution with some 𝑚m from 1 to 11.

题意：给出一串数字，现在要给这些数字上色（从1到m）。若两个数字被涂成了相同的颜色，则这两个数字必有公因数。已知给出的一串数字必能用11种颜色完成，求其中一种可解的方法。

我人傻了，当时已知以为上限11种是定理证明出来的而不是题目数据保证的，然后敲了100+行wa3心态直接崩了qwq然后看了一眼榜还在想你们怎么都秒了？？？

既然是数据保证那就太简单了，直接给出前11个质数，对输入每个去判断，对于可以整除的就归到此类，全部输出即可。



## C

题目：

> Word s of length n is called 𝑘k-complete if 
>
> - s is a palindrome, i.e. si=sn+1−i for all 1≤i≤n; 
> - s has a period of k, i.e. si=sk+i for all 1≤i≤n−k. 
>
> For example, "abaaba" is a 3-complete word, while "abccba" is not.
>
> Bob is given a word s of length n consisting of only lowercase Latin letters and an integer k, such that n is divisible by k. He wants to convert s to any k-complete word.
>
> To do this Bob can choose some i (1≤i≤n) and replace the letter at position i with some other lowercase Latin letter.
>
> So now Bob wants to know the minimum number of letters he has to replace to convert s to any k-complete word.
>
> Note that Bob can do zero changes if the word s is already k-complete.
>
> You are required to answer t test cases **independently**.

题意：定义k-completed数：对称且符合周期为k。现给出一个字符串，可以对其元素进行修改，求最少改几次可以满足该字符串为k-completed数。数据范围：

>  t (1≤t≤1e5) — the number of test cases.
>
> The first line of each test case contains two integers n and k (1≤k<n≤2e5, n is divisible by k).
>
> The second line of each test case contains a word s of length n.
>
> It is guaranteed that word s only contains lowercase Latin letters. And it is guaranteed that the sum of n over all test cases will not exceed 2e5.

之前周末PTA训练赛有做过类似的题，就是对于等价类中最后全部更新为数量最多的字母，即该类贡献=总数-maxn，再等价类间求贡献和。根据题意，回文串需要另每个i有$$s_i$$与$$s_{n-i+1}$$等价，对于周期性，需要$$s_i$$与$$s_{i+k}$$等价。

至于等价类的存储，我比赛结束后马上敲了一份类合并时直接二维数组逐个更新的做法，然后T2了qwq 第二天用了并查集存下等价类整个等价的结构，最后一起另做计数。



## D

题目：

> 
> Bob is playing a game named "Walk on Matrix".
>
> In this game, player is given an n×m matrix A=(ai,j), i.e. the element in the i-th row in the j-th column is ai,j. Initially, player is located at position (1,1) with score a1,1. 
>
> To reach the goal, position (n,m), player can move right or down, i.e. move from (x,y) to (x,y+1) or (x+1,y), as long as player is still on the matrix.
>
> However, each move changes player's score to the [bitwise AND](https://en.wikipedia.org/wiki/Bitwise_operation#AND) of the current score and the value at the position he moves to.
>
> Bob can't wait to find out the maximum score he can get using the tool he recently learnt  — **dynamic programming**. Here is his algorithm for this problem.
>
> However, he suddenly realize that the algorithm above fails to output the maximum score for some matrix A. Thus, for any given non-negative integer k, he wants to find out an n×m matrix A=(ai,j) such that 
>
> - 1≤n,m≤500 (as Bob hates large matrix); 
> - 0≤ai,j≤3e5  for all 1≤i≤n,1≤j≤m (as Bob hates large numbers); 
> - the difference between the maximum score he can get and the output of his algorithm is **exactly** k. 
>
> It can be shown that for any given integer k such that 0≤k≤1e5, there exists a matrix satisfying the above constraints.
>
> Please help him with it!

题意：现有一个n*m的矩阵，Bob现在在(1,1)的位置，目标是（n,m)，到达每一步的得分为之前得分与当前格的数字的位与运算结果。Bob他想用DP算法来计算最优解，但由于是位运算,d[i][j]=max(dp[i-1][j]&dp[i][j],d[i][j-1]&dp[i][j])的转移方程其实是假的，并不一定得到最优解。

现给出整数k，让我们自己定义一个矩阵，使得最优解和Bob’s DP的结果正好为k，且要注意定义的矩阵的规格和元素范围的限制。

思路：对于最终结果相差k直接构造：k&k==k, INF&k==0（这里可以INF=1<<17，只需即不超过3e5又比1e5大即可）；又需要构造出相应能够满足两种算法需求的前提元素，即可得到最终矩阵如下：


$$
INF+k\ ,\ k\ ,\ 0\\
INF,INF+k, k
$$

(先去学编译了E和F回来再补qwq)
