---
layout:    post   				    # 使用的布局（不需要改）
title:     「Machine Learning-4」Support Vector Machine # 标题 
subtitle:  Hard / Soft / Kernel margin SVM in ML#副标
date:      2020-07-30 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img12.jpg      #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Machine Learning


---

支持向量机（Support Vector Machine，SVM）是机器学习分类算法中非常重要的一种，在神经网络还未盛行时被学术界广泛研究应用。对于分散在样本空间中的样本点，我们通常使用一个超平面将其进行分类，其中，SVM通过最大化两类之间的间隔，使分类方法的鲁棒性更高；而两类间的间隔通常定义为距离超平面最近的点到超平面的距离，故而问题转化为寻找最优的超平面划分方法使得该距离能够最大。

根据数据集的特点：

- 线性可分问题，如感知计算法处理的问题
- 线性可分，只有一点点错误点，如感知机算法发展而来的Pocket算法处理的问题
- 非线性问题，完全不可分，如在感知机算法发展出来的多层感知机和深度学习

这三种情况对于SVM分别有三种处理手段

1. hard-margin SVM
2. soft-margin SVM
3. kernel Method

### Hard-margin SVM

#### 模型定义

对于用于划分的超平面，我们将其定义为
$$
0=w^Tx+b
$$
，及其判别模型为
$$
f(x)=w^Tx+b\\
s.t.\ \ f(x)=
\begin{cases}
1\ \ \ ,w^Tx+b>0\\
-1\ ,w^Tx+b<0
\end{cases}
$$
，margin即表示两类中距离超平面最近的点，则按照margin的定义，有：


$$
margin(w,b)=min\ dis(w,b,x_i)=min\ \frac{1}{||w||}|w^Tx_i+b|
$$


我们想要最大化margin，所以有：
$$
max_{w,b}\ min_{x_i}\ \frac{1}{||w||}y_i(w^Tx_i+b)\\
\Rightarrow\ max_{w,b}\frac{1}{||w||}min_{x_i}y_i(w^Tx_i+b)\\
s.t.\ \ \ y_i(w^Tx_i+b)>0\ \ \ \ \Rightarrow\ \exists\gamma>0\ s.t.\ min\ y_i(w^Tx_i+b)=\gamma\\
原式\Rightarrow\ max\ min\ margin=max_{w,b}\frac{1}{||w||}\gamma
$$

由于该算法为分类算法，我们仅考虑$$w^Tx_i+b$$的结果是大于0还是小于0，故可$$2w^Tx_i+2b$$这类进行等比例缩放，即当找到一中可行划分后即可衍伸出无数对，故我们可以令$$\gamma=1$$，然后将原式转化为：

$$
max_{w,b}\frac{1}{||w||}\ \Rightarrow min_{w,b}||w||\ \Rightarrow  min_{w,b}\frac{1}{2}w^Tw \\
s.t.\ y_i(w^Tx_i+b)>=1,\ i=1……N
$$

这就是一个包含N个约束的凸优化问题。

#### 模型求解

当样本数量或维度非常高时，直接求解会非常困难甚至不可解，而带约束的优化问题也不利于机器快速处理，于是需要对这个问题进一步处理。引入Lagrange函数：


$$
L(w,b,\lambda)=\frac{1}{2}w^Tw+\sum_{i=1}^N\lambda_i(1-y_i(w^Tx_i+b))
$$


于是问题可以转化为：
$$
min_{w,b}max_{\lambda}L(w,b,\lambda)
\ \ \ \ \ s.t.\ \lambda_i>=0
$$

我们交换最小和最大值的符号即可得到强对偶形式：
$$
max_{\lambda_i}min_{w,b}L(w,b,\lambda)\ \ \ \ s.t.\ \lambda_i>=0
$$

引入拉格朗日函数并设置$$\lambda>=0$$的妙处在于，当$$1-y_i(w^Tx_i+b)>0$$，即分类错误时，$$max->\infty$$，即无法求出max值；当分类正确时，$$1-y_i(w^Tx_i+b)<=0$$,只要取$$\lambda=0$$则一定可以有max=0。即这种方法可以自动过滤掉分类错误的样本点。

对对偶问题求最优，对$$min\ L(w,b,\lambda)$$：
$$
\frac{\Delta L}{\Delta b}=0\ \ \Rightarrow \sum_{i=1}^N\lambda_iy_i=0\\
\Rightarrow\ L(w,b,\lambda)=\frac{1}{2}w^Tw+\sum_{i=1}^N\lambda_i-\sum_{i=1}^N\lambda_iy_iw^Tx_i\\
\frac{\Delta L}{\Delta w}=0\ \ \Rightarrow\ w=\sum_{i=1}^N\lambda_iy_ix_i\\
\Rightarrow\ L(w,b,\lambda)=\frac{1}{2}(\sum_{i=1}^N\lambda_iy_ix_i)^T(\sum_{j=1}^N\lambda_jy_jx_j)+\sum_{i=1}^N\lambda_i-\sum_{i=1}^N\lambda_iy_i(\sum_{j=1}^N\lambda_jy_jx_j)^Tx_i\\
=-\frac{1}{2}\sum_{i=1}^N\sum_{j=1}^N(\lambda_i\lambda_jy_iy_j)x^T_ix_j+\sum_{i=1}^N\lambda_i
$$


即对偶问题可以转化为：

$$
max_{\lambda}-\frac{1}{2}\sum_{i=1}^N\sum_{j=1}^N(\lambda_i\lambda_jy_iy_j)x^T_ix_j+\sum_{i=1}^N\lambda_i\\
s.t.\ \ \ \lambda_i>=0
$$

#### 引出KKT条件

原问题和对偶问题满足强对偶关系的充要条件为其满足KKT条件：
$$
\frac{\Delta L}{\Delta b}=0\ \ \ \frac{\Delta L}{\Delta w}=0\\
\lambda_i(1-y_i(w^Tx_i+b))=0(slackness\ complementary,松弛互补)\\
\lambda_i>=0\\
1-y_i(w^Tx_i+b)<=0
$$


根据这个条件就可以得到对应的最佳参数：
$$
\hat w=\sum_{i=1}^N\lambda_iy_ix_i\\
\hat b=y_k-w^Tx_k=y_k-\sum_{i=1}^N\lambda_iy_ix_i^T·x_k
$$


其中$$(x_k,y_k)$$为最近点，即有$$1-y_k(w^Tx_k+b)=0$$。由松弛互补条件可以看出，此时只有最近点的$$\lambda$$可以不需要取0，其他样本点均需要取零，即不起作用。我们称起作用的最近点为支持向量，其对应的$$\lambda$$一般可以取1或其他数。



### Soft-margin SVM

Hard-margin SVM只对可分数据可解，但是大部分情况下数据集是会存在噪声（即少数分类错误的点），或者数据集本身线性不可分，这种情况下我们的基本想法是，在损失函数汇总加入错误分类的可能性。其错误分类的个数可以写成：
$$
error=\sum_{i=1}^N1(y_i(w^Tx_i+b)<1)
$$

若令$$y_i(w^Tx_i+b)=z$$,可以看见loss-z图在z上不连续，可以将其改为：
$$
error=\sum_{i=1}^Nmax\{0,1-y_i(w^Tx_i+b)\}
$$

将该error加入Hard-margin SVM的loss function中：

$$
argmin_{w,b}\frac{1}{2}w^Tw+C\sum_{i=1}^N\{0,1-y_i(w^Tx_i+b)\}\\
s.t.\ \ y_i(w^Tx_i+b)>=1-\xi_i,\ \ i=1……N
$$

这个式子中，常数C可以看做允许的错误水平，同时上式为了进一步消除max符号，对数据集中的每一个观测，我们可以认为其大部分满足约束，但其中部分违反约束，因此这部分约束变成$$y_i(w^Tx_i+b)>=1-\xi_i$$，其中$$\xi_i=1-y_i(w^Tx_i+b)$$,进一步化简可得：

$$
argmin_{w,b}\frac{1}{2}w^Tw+C\sum_{i=1}^N\xi_i\\
s.t.\ \ y_i(w^Tx_i+b)>=1-\xi_i,\ \ i=1……N
$$

### Kernel Method

核方法可以应用在很多问题上，在分类问题中，对于严格不可分问题，我们引入一个特征转换函数将原来的不可分的数据集，然后再来应用已有的模型。往往将低维空间的数据集变为高维空间的数据集后，数据会变得可分（数据变得更为稀疏）：

> Cover TH：高维空间比低维空间更易线性可分

应用在SVM中时，观察上面的SVM对偶问题：

$$
max_\lambda \frac{1}{2}\sum_{i=1}^N\sum_{j=1}^N\lambda_i\lambda_jy_iy_jx_i^Tx_j+\sum_{i=1}^N\lambda_i\ \ \ s.t.\ \lambda_i>=0
$$


在求解的时候需要求得内积，于是不可分数据在通过特征变换后，需要求得变换后的内积。我们常常很难求得变换函数的内积。于是直接引入内积的变换函数：

$$
\forall x,x'\in X,\exists \phi\in H:x\rightarrow z\ \ \ \ s.t.\ k(x,x')=\phi(x)^T\phi(x)
$$


称k(x,x')为一个正定核函数，其中H是Hilbert空间（完备的线性内积空间），如果去掉内积这个条件我们简单地称为核函数，如k(x,x')=exp($$-\frac{(x-x')^2}{2\sigma^2}$$是一个核函数,可以简单证明。

**【正定核函数有下面的等价定义】**

如果核函数满足：①对称性  ②正定性，那么这个核函数是正定核函数。

证明：

- 对称性，$$k(x,z)==k(z,x)$$，显然满足

- 正定性，$$\forall N,x_1,x_2,……x_N \in X$$，对应的Gram Matrix $$K=[k(x_i,x_j)]$$是半正定的。

  要证$$k(x,z)=\phi(x)^T\phi(z)$$

  1. $$\Rightarrow$$：首先，对称性是显然的，对于正定性
     $$
     K=\begin{pmatrix}
     k(x_1,x_2)&\cdots&k(x_1,x_N)\\
     \vdots&\vdots&\vdots\\
     k(x_N,x_1)&\cdots&k(x_N,x_N)
     \end{pmatrix}
     $$
     
     任意取$$\alpha \in R^N$$,即需要证明$$\alpha^TK\alpha>=0$$：
     $$
     \alpha^TK\alpha=\sum_{i,j}\alpha_i\alpha_jK_{ij}=\sum_{i,j}\alpha_i\phi^T(x_i)\phi(x_j)\alpha_j=\sum_i\alpha
     $$
     
     这个式子就是内积的形式，Hilbert空间满足线性，于是正定性得证。

  2. $$\Leftarrow$$：对于K进行分解，对于对称矩阵$$K=V\Lambda V^T$$，那么令$$\phi(x_i)=\sqrt(\lambda_i)V_i$$，其中$$V_i$$是特征向量，于是就构造了$$k(x,z)=\sqrt(\lambda_i \lambda_j)V_i^TV_j$$



### 小结

分类问题在很长一段时间都以来SVM，对于严格可分的数据集，Hard-margin SVM选定一个超平面，保证所有数据到这个超平面的距离最大，对这个平面施加约束，固定$$y_i(w^Tx_i+b)=1$$，得到一个凸优化问题并且所有的约束条件都是仿射函数，于是满足Slater条件，将这个问题变换成对偶的问题，可以得到等价的解，并求出约束参数：

$$
max_{\lambda}-\frac{1}{2}\sum_{i=1}^N\sum_{j=1}^N\lambda_i\lambda_jy_iy_jx_i^Tx_j+\sum_{i=1}^N\lambda_i\ \ \ s.t.\ \lambda_i>=0
$$

对需要的超平面参数的求解采用强对偶问题的KKT条件进行：

> $$
> \frac{\Delta L}{\Delta b}=0\ \ \ \frac{\Delta L}{\Delta w}=0\\
> \lambda_i(1-y_i(w^Tx_i+b))=0(slackness\ complementary,松弛互补)\\
> \lambda_i>=0\\
> 1-y_i(w^Tx_i+b)<=0
> $$

解就是：

$$
\hat w=\sum_{i=1}^N\lambda_iy_ix_i\\
\hat b=y_k-w^Tx_k=y_k-\sum_{i=1}^N\lambda_iy_ix_i^T·x_k
$$

当允许一点错误的时候，可以在Hard-margin SVM中加入错误项。用Hinge Function表示错误项的大小，得到：

$$
argmin_{w,b}\frac{1}{2}w^Tw+C\sum_{i=1}^N\xi_i\\
s.t.\ \ y_i(w^Tx_i+b)>=1-\xi_i,\ \ i=1……N
$$

对于完全不可分的问题，我们采用特征转换的方式，在SVM中，我们引入正定核函数来直接对内积进行变换，只要这个变换满足对称性和正定性，那么就可以用做核函数。
