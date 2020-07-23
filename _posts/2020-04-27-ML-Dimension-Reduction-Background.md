---
layout:    post   				    # 使用的布局（不需要改）
title:     「Machine Learning-3」Dimension Reduction Background # 标题 
subtitle:  Dimension Reduction Background for ML(PCA & PCoA) #副标
date:      2020-04-27 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img12.jpg       #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Machine Learning
---

在做线性回归任务时，经常会有过拟合的问题。在机器学习中，相比起训练误差，我们更希望能降低泛化误差。缓解过拟合的方法大致有三种，即增加数据量、正则化、降维（源于维度灾难）。

# 维度灾难理解（几何角度）

假设我们现有一个二维的边长为2R的正方形，和半径为R的圆,易得:

$$
V_正=2^2R^2\\
V_圆=\pi R^2
$$


那么n维球的体积为：$CR^n$，则球体积与边长为2R的超立方体相比则有：


$$
lim_{n \rightarrow 0}\frac {CR^n}{2^nR^n}=0
$$


这就是所谓的维度灾难：当维度趋近于正无穷时样本实际上几乎都散落在超正方体的角落，而不会落在中心的超球体中。这种样本是很难进行分类等操作的。

降维的算法可分为：

- 直接降维，如特征选择
- 线性降维，如PCA、MDS(Multiple Dimensional Scaling，多维标度分析)等
- 非线性降维，即流形学习，包括Isomap、LLE等



# 样本均值&样本方差矩阵

为了方便，我们首先将协方差矩阵（数据集）写成中心化的形式：
$$
S=\frac {1}{N}\sum_{i=1}^N(x_i-\overline x)(x-\overline x)^T\\
=\frac{1}{N}(x_1-\overline x,x_2-\overline x……x_N-\overline x)(x_1-\overline x,x_2-\overline x……x_N-\overline x)^T\\
=\frac {1}{N}(X^T-\frac{1}{N}X^T·I_{N1}·I_{N1}^T)(X^T-\frac{1}{N}X^T·I_{N1}·I_{N1}^T)^T\\
=\frac{1}{N}X^T(E_N-\frac{1}{N}·I_{N1}·I_{1N})(E_N-\frac{1}{N}·I_{N1}·I_{1N})^TX\\
=\frac{1}{N}X^TH_NH_N^TX\\
=\frac{1}{N}X^THX
$$


其中H为中心矩阵（即投影矩阵），上述式子利用了其对称性，证明如下：
$$
H=E_N-\frac{1}{N}I_{N1}·I_{N1}^T=H^T\\
故有：H^2=(E_N-\frac{1}{N}I_{N1}·I_{N1}^T)(E_N-\frac{1}{N}I_{N1}·I_{N1}^T)^T\\
=E_N-\frac{2}{N}I_{N1}·I_{N1}+\frac{1}{N^2}I_{N1}I_{1N}I_{N1}I_{1N}\\
=E_N-(\frac{2}{N}-\frac{1}{N})I_{N1}·I_{N1}\\
=E_N-\frac{1}{N}I_{N1}·I_{N1}\\
=H
$$




# 线性降维-主成分分析PCA

主成分分析中，我们的基本想法是将所有数据投影到一个子空间中，从而达到降维的目的。为了寻找这个子空间，我们的基本想法是：

1. 所有数据在子空间中更为分散，即投影方差最大化
2. 损失的信息最小，即在补空间的分量最小化

实际上，这两点的本质是相同的。在二维情况下其可以用勾股定理进行解释：

![](/img/DRB_img1.gif)

#### 最大投影方差

原来的数据很有可能是各个维度间是相关的，于是我们希望找到一组p个新的线性无关的单位基$u_i$，降维就是取其中的q个基，实现从原本p维到现在q维(p>q)的降维。于是对于一个样本，经过这个坐标变换后：


$$
\hat x=\sum_{i=1}^p (u_i^Tx_i)u_i=\sum_{i=1}^q(u_i^Tx_i)u_i+\sum_{i=q+1}^p(u_i^Tx_i)u_i
$$


对于数据集，我们将其中心化后的结果对主成分做投影方差，并使方差最大化（对应上述第一点）：


$$
J_1=\frac{1}{N}\sum_{i=1}^N\sum_{j=1}^q((x_i-\overline x)^Tu_j)^2\\
=\sum_{j=1}^qu_j^T(\frac{1}{N}\sum_{i=1}^N(x_i-\overline x)(x_i-\overline x)^T)u_j\\
=\sum_{j=1}^qu_i^TSu_j,\ \ \ \ s.t. u_j^Tu_j=1
$$


由于每个基都是线性无关的，于是每个$u_j$的求解可以分别进行，使用拉格朗日乘子法：


$$
arg\,\max_{u_j} L(u_j,\lambda)=arg\,\max_{u_j}u_j^TSu_j+\lambda(1-u_j^Tu_j)\\
\frac{\Delta L}{\Delta u_j}=2Su_j-2\lambda u_j=0\\
\Rightarrow Su_j=\lambda u_j
$$


其中，$\lambda$即为线性基的特征值，$u_j$即为特征向量，J1最大取在本征值为最大的q个。

#### 最小重构代价

假设对于二维情况，x1、x2为原始的基底，先将其用新的一组基底表示，则有：
$$
x_i=x_{i1}+x_{i2}=(x_i^Tu_1)u_1+(x_i^Tu_2)u_2
$$


一般的，在p维时坐标表示为
$$
x_i=\sum_{j=1}^p(x_i^Tu_j)u_j
$$
的点降维至q维后，其表示变为：
$$
x_i=\sum_{j=1}^q(x_i^Tu_j)u_j
$$


则重构代价有：
$$
J_2=\frac{1}{N}\sum_{i=1}^N||x_i-\hat x||^2\\
=\frac{1}{N}\sum_{i=1}^N\sum_{j=q+1}^p||(x_i^Tu_j)u_j||^2\\
=\frac{1}{N}\sum_{i=1}^N\sum_{j=q+1}^p(x_i^Tu_j)^2\\
=\frac{1}{N}\sum_{i=1}^N\sum_{j=q+1}^p((x_i-\overline x)^2u_j)^2\\
=\sum_{j=q+1}^p(\frac{1}{N}\sum_{i=1}^N(x_i-\overline x)^T(x_i-\overline x)u_j)\\
=\sum_{j=q+1}^pu_j^TSu_j
$$


可以发现，重构代价J2与投影代价J1唯一不同之处就在于J1从1加到q维，J2从q+1加到p维；而算式本身完全相同，则同样的，对重构代价求最小：
$$
arg\,\min_{u_j} L(u_j,\lambda)=arg\,\min_{u_j}u_j^TSu_j+\lambda(1-u_j^Tu_j)\\
\frac{\Delta L}{\Delta u_j}=2Su_j-2\lambda u_j=0\\
\Rightarrow Su_j=\lambda u_j
$$


故J2最小取在本征值位剩下的最小的p-q个。



# SVD与PCoA

对中心化后的数据进行奇异值分解：


$$
HX=U\Sigma V^T,U^TU=E_N,V^TV=E_p,\Sigma:N\ast p(对称)
$$


于是：


$$
S=\frac{1}{N}X^THX=\frac{1}{N}X^TH^THX=\frac{1}{N}V\Sigma^T\Sigma V^T
$$


因此，我们直接对中心化后的数据集进行SVD，就可以得到特征值和特征向量V，则用PCA方法对数据集进行降维后在新坐标系中的坐标就是：


$$
HX·V
$$


由上面的推导，我们也可以得到另一种方法PCoA主坐标分析，定义并进行特征值分解：


$$
T=HX(HX)^T=U\Sigma^2U^T
$$


于是有：
$$
\begin{cases}
T=U\Sigma^2U^T\\
T(U\Sigma)=(U\Sigma)\Sigma^2
\end{cases}
$$


对于PCA得到的坐标$HX·V=U\Sigma V^TV=U\Sigma$。易发现$U\Sigma$即为坐标矩阵，$\Sigma^2$即为特征值矩阵。

**总结：**

$S \in R^{p\ast p}$为PCA角度，其通过分解方差矩阵，得到方向（即主成分V），再通过$HX·V$得到坐标（新表示），从而实现重构。

$T \in R^{N\ast N}$为PCoA角度，直接对数据（内积矩阵）进行分解，即可直接得到坐标。相较于PCA方法，当样量较少的时候可以采用PCoA方法。



# p-PCA

下面从概率的角度对PCA进行分析，即Probabilistic-PCA。我们使用线性模型，对原数据$x\in R^p$，降维后的数据为$z\in R^q$，有q<p。降维通过一个矩阵变换（投影）进行：


$$
\begin{cases}
z\sim N(O_{q1},I_{qq})\\
x=Wz+\mu+\epsilon\\
\epsilon \sim N(0,\sigma^2I_{pp})
\end{cases}
$$

对于这个模型，我们可以使用期望-最大（EM）的算法进行学习，在进行推断的时候需要求得
$$
p(z|x)
$$
，推断的求解过程和线性高斯模型类似。

$$
p(z|x)=\frac{p(x|z)p(z)}{p(x)}\\
E[x]=E[Wz+\mu+\epsilon]=\mu\\
Var[x]=WW^T+\sigma^2I_{pp}\\
\Rightarrow p(z|x)\sim N(W^T(WW^T+\sigma^2I)^{-1}(x-\mu),I-W^T(WW^T+\sigma^2I)^{-1}W)
$$




# 总结

降维是解决维度灾难和过拟合的重要方法，除了直接的特征选择外，我们还可以采用算法的途径对特征进行筛选。

线性的降维方法以PCA为代表，在PCA中，我们只要直接对数据矩阵进行中新华然后求奇异值分解，或者对数据的协方差矩阵进行分解就可以得到其主要维度。
