---
layout:    post   				    # 使用的布局（不需要改）
title:     「Machine Learning-2」Linear Regression # 标题 
subtitle:  Linear Regression from the perspective of Frequentist & Bayesians #副标
date:      2020-07-23 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img12.jpg      #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Machine Learning

---

线性回归是最简单的一种回归算法。该篇将从最基本的最小二乘法入手，包括其矩阵表达形式和几何意义；再由频率视角出发，可以推导得到LSE（Least Squre Method，即最小二乘法）与MLE（$$噪声\epsilon \in N(0,\sigma^2)$$）是等价关系；此外，对于样本过少、或维度爆炸的情况下可能出现的过拟合情况，一般有三种解决方式，一是增加数据量，二是降维（即特征提取/重构）或特征选择，三是正则化。该篇重点介绍L1、L2正则化，并分别通过频率派视角和贝叶斯视角进行阐述。

本来这篇是想线性回归和线性分类一起做的，然后想了想还是到时候专门出一篇分类方法，鸽了好几个月对不起对不起Orz

### Least Squre Method 最小二乘法

统一规定：


$$
数据集： D=\{(x_1,y_1),(x_2,y_2),……(x_N,y_N)\}\\
自变量： X=(x_1,x_2,……x_N)^T\in R^{N*p}\ \ \ \ \ 即N个变量，每个变量为p维\\
权值矩阵： w\in R^{p*1} \\
取值：Y=(y_1,y_2,……y_N)^T\in R^{N*1}\\
线性回归假设： f(w)=w^TX
$$


我们采用二范数定义的平方误差来定义损失函数：
$$
L(w)=\sum_{i=1}^N||w^Tx_i-y_i||_2^2\\
=[w^T(x_1,x_2,……x_N)-(y_1,y_2,……y_N)][w^T(x_1,x_2,……x_N)-(y_1,y_2,……y_N)]^T\\
=(w^TX-Y^T)(Xw-Y)\\
=w^TX^TXw-2w^TX^TY+Y^TY
$$


又有
$$
\hat x = argmin\ L(w)\\
\Rightarrow \frac{\Delta L(w)}{\Delta w}=2X^TXw-2X^TY\\
\Rightarrow w=(X^TX)^{-1}X^TY=X^+Y
$$


其中$$(X^TX)^{-1}X^T$$记为伪逆$$X^+$$，对于行满秩或列满秩的X，可以直接求解；而对于非满秩的样本集合，需要使用奇异值分解（SVD）的方法，对X求奇异值分解，得到$$X=U\Sigma V^T$$，于是也有$$X^+=V\Sigma^{-1}U^T$$。

在几何上，我们令$$f(w)=x\beta$$，由于$$X\in R^{N*p}$$相当于是在一个p维超平面中的N个向量。当Y恰好在这个p维超平面上时L(w)为0；当其不在这个超平面上时，由向量Y向超平面做投影，投影向量即为$$X\beta$$，差向量即为$$Y-X\beta$$。由该差向量与p维空间中的向量全部垂直可得到
$$
X^T(Y-X\beta)=0\in R^{p*1}\\
\Rightarrow X^TY=X^TX\beta\\
\Rightarrow \beta=(X^TX)^{-1}X^TY
$$


可以看到两种解释下$$w$$与$$\beta$$的解是一样的，其中$$Xw-Y$$与$$Y-X\beta$$的结构也是相同的。这也是用第二范式定义最小二乘法时，真实量Y与拟合量f(w)之间的差的代数意义和几何意义。



### LSE-probabilistic perspective 概率视角

设数据在f(w)的基础上有噪声：
$$
\epsilon \sim N(0,\sigma^2)
$$
，即有
$$
y=f(w)+\epsilon
$$
，可以得到
$$
y|x;w \sim N(w^Tx,\sigma^2)
$$


则有最大似然估计MLE：
$$
L(w)=log P(Y|X;w)=log\prod_{i=1}^N(y_i|x_i;w)\\
=\sum_{i=1}^N[log\frac{1}{\sqrt{2\pi}\sigma}-\frac{(y_i-w^Tx_i)^2}{2\sigma^2}]\\
\\
\hat w = argmax\ L(w)\\
=argmax\ \sum_{i=1}^N-\frac{1}{2\sigma^2}(y_i-w^Tx_i)^2\\
=argmin\ \sum_{i=1}^N(y_i-w^Tx_i)^2\\
$$


可以得到，通过MLE进行优化的方式与定义的最小二乘法相同，表示当通过设噪声服从去中心化的正态分布时，概率视角的MLE是与最小二乘法等价的。



### Regularization-frequentist 正则化（频率派）

引入正则化的原因：一般对$$X\in R^{N*p}$$都有N>>p，而当样本数量过少，或维度过大时，容易造成过拟合。此处我们通过正则化抑制过拟合。

正则化框架如下：


$$
J(w)=L(w)+\lambda P(w)
$$
其中L(w)是loss function，$$\lambda P(w)$$是正则化项。正则化常见有L1、L2两种形式。

- L1：又称为Lasso，采用第一范式：
$$P(w)=||w||$$
- L2(即权值衰减)：又称Ridge Regression，即岭回归，采用第二范式：
$$P(w)=||w||_2^2=w^Tw$$

#### L1 Lasso 

L1正则化可以引起稀疏解。从最小化损失的角度看，由于L1项求导在0附近的左右导数都不是0，因此更容易取到0解；从另一方面看，L1正则化相当于
$$\mathop {argmin}_{w}L(w)，s.t.||w||_1<C$$。
我们已经看到平方误差损失函数在w空间是一个椭球，因此上式求解就是椭球和
$$||w||_1=C$$
的切点，因此更容易相切在坐标轴上。

#### L2 Ridge Regression
在最小二乘误差的基础上添加一个正则化项：
$$
J(w)=L(w)+\lambda P(w)\\=\sum_{i=1}^N||w^Tx_i-y_i||^2+\lambda w^Tw\\=w^TX^TXw-2w^TX^TY+Y^TY+\lambda w^Tw\\=w^T(X^TX+\lambda I)w-2w^TX^TY+Y^TY\\\\\Rightarrow \hat w =argmax\ J(w)\\\Rightarrow \frac{\Delta J(w)}{\Delta w}=2(X^TX+\lambda I)w-2X^TY=0\\\hat w = (X^TX+\lambda I)^{-1}X^TY\\
$$


对比可见加上正则化项后w的结果比无正则化时多了一项$$\lambda I$$。由于$$X^TX$$为半正定的，而$$\lambda I$$为对角矩阵，故$$\lambda I+X^TX$$为正定矩阵，即一定是可逆的。



### Regularization-Bayesians 正则化（贝叶斯派）

按照贝叶斯派的理论，假设有先验
$$
w\sim N(0,\sigma_0^2)
$$
，则有后验概率：
$$
P(w|y)=\frac {P(y|w)P(w)}{P(y)}
$$
，其中P(y)是固定的，而似然P(y|w）根据高斯分布(其噪声为上述我们假设的去中心化的高斯分布)有
$$
P(y|w)=\frac {1}{\sqrt{2\pi}\sigma}exp(-\frac{(y_i-w^Tx)^2}{2\sigma^2})
$$


则有最大后验概率MAP：
$$
\hat w = argmax\ P(w|y)\\
=argmax\ P(y|w)P(w)\\
\Rightarrow argmax\ log(\frac{1}{\sqrt{2\pi}\sigma}\frac{1}{\sqrt{2\pi}\sigma_0})+\sum_{i=1}^Nlog\ exp(-\frac{(y_i-w^Tx)^2}{2\sigma^2}-\frac{||w||^2}{2\sigma_0^2})\\
=argmin\sum_{i=1}^N(\frac{(y_i-w^Tx)^2}{2\sigma^2}+\frac{||w||^2}{2\sigma_0^2})\\
=argmin\sum_{i=1}^N(y_i-w^Tx)^2+\frac{\sigma^2}{\sigma_0^2}||w||^2
$$


将贝叶斯角度求得的w与频率派求得的w进行对比有：
$$
\hat w_{MAP}=argmin\sum_{i=1}^N(y_i-w^Tx)^2+\frac{\sigma^2}{\sigma_0^2}||w||^2\\
\hat w_{L2}=argmin\ J(w)=argmin\sum_{i=1}^N||w^Tx_i-y_i||^2+\lambda w^Tw
$$

可以发现两个最后求得的w是等价的，说明Reqularized LSE（正则化的LSE）是等价于MAP（noise&prior为Gaussian Dist）的。只不过贝叶斯派是基于其学派理论，借助先验概率和实验结果(即似然)来得到后验概率，通过改变w使得后验概率尽可能最大；而频率派的正则化法是在最小二乘法的基础上加上了一项正则化项得到此时，在前面我们也证明了最小二乘法是等价于Gaussian MLE（$$\epsilon \sim N$$）的，即最小二乘法符合频率派的观点。



### 小结

线性回归模型是最简单的模型，但麻雀虽小，五脏俱全。在这里，我们利用最小二乘法得到了闭式解，同时也发现在噪声为高斯分布的时候，MLE的解等价于最小二乘误差；在加入正则化项以后，最小二乘误差加上L2正则项等价于高斯噪声先验下的MAP解，加上L1正则项后，等价于Laplace噪声先验。

传统的机器学习方法或多或少都有线性回归模型的影子：

- 线性回归模型往往不能很好地拟合数据，因此有三种方案克服这一劣势：①对特征的维度进行变换 ②在线性方程后加入一个非线性变换，即引入一个非线性的激活函数 ③对于一致的线性系数，我们进行多次变换，使同一个特征不仅仅被单个系数影响
- 线性回归在整个样本空间都是线性的，我们可以对这个限制进行修改，在不同区域引入不同的线性或非线性，例如线性样条回归和决策树模型
- 线性回归中使用了所有的样本，但是对数据预先进行加工学习的效果可能更好，例如在有维度灾难的情况下，高纬度数据时非常难学习的，则可以使用PCA和流形学习等方法。
