---
layout:    post   				    # 使用的布局（不需要改）
title:     「Machine Learning-5」Exponential Family Distribution   # 标题 
subtitle:  Definition of ExpFamily Dist & Thought of Maximum Entropy  #副标
date:      2020-08-05 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img12.jpg      #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Machine Learning

---

指数族是一类分布，包括高斯分布、伯努利分布、二项分布、泊松分布、Beta分布、Dirichlet分布、Gamma分布等一系列分布。指数族分布可以写为统一形式：


$$
p(x|\eta)=h(x)exp(\eta^T\phi(x)-A(\eta))
$$


其中$$\eta$$为（规范化）参数，一般为向量形式$$\in R^p$$；$$A(\eta)$$为对数分配函数（log partition function），可见$$A(\eta)=log\ z$$，所以$$A(\eta)$$被称为对数分配函数：

$$
p(x|\eta)=\frac{1}{exp(A(\eta))}h(x)exp(\eta^T\phi(x))=\frac{1}{z}\hat p(x|\eta) z为归一化因子
$$


指数族分布也涉及到了三个非常重要的概念，以及对三个ML领域非常重要的算法/模型。

【三大概念】

1. 充分统计量$$\phi(x)$$。统计量指能反映一个样本集合信息的量，如E、Var等；充分统计量即包含了所有能够完整表示该样本集的量，根据充分统计量，我们可以知道该集合的整体特性、即其统计意义，而不需要知道集合中每个样本的具体信息。利用这一点，我们可以对样本进行简化，从而优化计算。

2. 共轭。我们通过既得样本求后验：
   $$
   p(z|x)=\frac{p(x|z)p(z)}{\int_zp(x|z)p(z)dz}
   $$
   。但是，可以看到，在该式子中问题①在于分母项的积分非常难求，②在于其形式过于复杂。一般解决有两种方法，一种是通过近似推断进行简化优化，即MCMC采样，或变分推断；一种即是通过共轭。

   给出性质：若先验与似然共轭，则先验与后验相同。如令
   $$P(x|z)$$
   ~二项分布，P(z)~Beta，则
   $$P(z|x)$$
   ~Beta。通过共轭，我们可以非常取巧的避免求积分项，从而直接得到结果。

3. 最大熵。指数族分布满足最大熵思想（无信息先验），也就是说，对于经验分布，利用最大熵原理推导出的分布就是指数族分布。这一点会在下面的最大熵思想部分详细给出。

【三大算法模型】

1. 广义线性模型。根据线性模型定义$$y=f(w^Tx)$$，我们很容易可以窥见指数族分布与线性模型的关系：对于用于不同用途的线性模型，有
$$y|x~ExpFamily Dist$$
，eg. 对于线性回归问题，有
$$y|x~N(\mu,\epsilon)$$
。
2. 概率图模型。如在无向图-受限玻尔兹曼机（RBM）中，指数族分布也扮演着重要的作用。
3. 变分推断算法。利用指数族分布可以大大简化计算。



### Gaussian Distribution

我们以一维高斯分布为例，将其改写为指数族分布的形式。对于一维高斯分布：


$$
p(x|\theta)=\frac{1}{\sqrt{2\pi}\sigma}exp(-\frac{(x-\mu)^2}{2\sigma^2})\\
\theta=(\mu,\sigma^2)
$$


将其改写为$$p(x|\eta)=h(x)exp(\eta^T\phi(x)-A(\eta))$$。则有：
$$
原式\Rightarrow \frac{1}{\sqrt{2\pi\sigma^2}}exp(-\frac{(x^2-2\mu x+\mu^2)}{2\sigma^2})\\
=exp-\frac{1}{2}log(2\pi\sigma^2)*exp(-\frac{1}{2\sigma^2}(-2\mu,1)\binom{x}{x^2}-\frac{\mu^2}{2\sigma^2})\\
=exp((\frac{\mu}{\sigma^2},-\frac{1}{2\sigma^2})\binom{x}{x^2}-(\frac{\mu}{2\sigma^2}+\frac{1}{2}log2\pi\sigma^2))
$$


可以得到：
$$
\eta=\binom{\eta_1}{\eta_2}=\binom{\frac{\mu}{\sigma^2}}{-\frac{1}{2\sigma^2}}\\
\Rightarrow \begin{cases}\mu=-\frac{\eta_1}{2\eta_2}\\\sigma^2=-\frac{1}{2\eta_2} \end{cases}\\
A(\eta)=-\frac{\eta_1^2}{4\eta_2}+\frac{1}{2}log(-\frac{\pi}{\eta_2})
$$


即可将一维高斯分布改写成：


$$
p(x|\theta)=exp(\eta^T\phi(x)-A(\eta))\\
s.t.
\begin{cases}
\eta=\binom{\eta_1}{\eta_2}=\binom{\frac{\mu}{\sigma^2}}{-\frac{1}{2\sigma^2}}\\
\phi(x)=\binom{x}{x^2}\\
A(\eta)=-\frac{\eta_1^2}{4\eta_2}+\frac{1}{2}log(-\frac{\pi}{\eta_2})
\end{cases}
$$


### Log partition Function性质

对于概率密度函数：$$p(x|\eta)=\frac{1}{exp(A(\eta))}h(x)exp(\eta^T\phi(x))$$，对其归一性质，我们队两边进行积分，可得到：


$$
1=\int p(x|y)dx=\frac{1}{exp(A(\eta))}\int h(x)exp(\eta^T\phi(x))dx\\
\Rightarrow exp(A(\eta))=\int h(x)exp(\eta^T\phi(x))dx\ \ \ \ ①
$$


对①式两边对$$\eta$$进行求导：
$$
exp(A(\eta))A'(\eta)=\int h(x)exp(\eta^T\phi(x))\phi(x)dx\\
\Rightarrow A'(\eta)=\frac{\int h(x)exp(\eta^T\phi(x))\phi(x)dx}{exp(A(\eta))}\\
=\int h(x)exp(\eta^T\phi(x)-A(\eta))\phi(x)dx\ \ \ s.t. p(x|y)=h(x)exp(\eta^T\phi(x)-A(\eta))\\
=\int p(x|y)\phi(x)dx\\
=E_{p(x|y)}[\phi(x)]
$$


同理也可以求得：

$$
A''(\eta)=Var_{p(x|y)}[\phi(x)]>0\\
$$

即$$A(\eta)$$的二阶导数恒大于零，故$$A(\eta)$$为凸函数。



### MLE & Sufficient Statistics

对于独立同分布采样的样本集合：$$D=\{x_1,x_2……,x_N\}$$，我们通过MLE计算参数：
$$
\mu_{MLE}=argmax\ \sum_{i=1}^Nlog\ p(x_i|y)\\
=argmax \sum_{i=1}^Nlog[h(x_i)exp(\eta^T\phi(x_i)-A(\eta))]\\
=argmax \sum_{i=1}^N[log\ h(x_i)+\eta^T\phi(x_i)-A(\eta)]\\
\Rightarrow argmax\ \sum_{i=1}^N[\eta^T\phi(x_i)-A(\eta)]
$$


两边对$$\eta$$求导：
$$
\frac{\Delta \sum_{i=1}^N[\eta^T\phi(x_i)-A(\eta)]}{\Delta \eta}\\
=\sum_{i=1}^N(\frac{\Delta[\eta^T\phi(x_i)-A(\eta)]}{\Delta \eta})\\
=\sum_{i=1}^N(\phi(x_i)-A'(\eta))\\
=\sum_{i=1}^N\phi(x_i)-nA'(\eta)=0\\
\\
\Rightarrow\ A'(\mu)_{MLE}=\frac{\sum_{i=1}^N\phi(x_i)}{n}
$$

又$$A(\eta)=\int A'(\eta)d\eta$$，可以得到$$A(\eta)$$的表达式，又可以根据$$A(\eta)$$的反函数$$\eta=A^{-1}(\eta)$$得到参数$$\eta$$。综上，当我们知道了充分统计量，就可以以岁数分配函数为桥梁，估算出参数$$\eta$$。



### Maximum Entropy Perspective

在介绍最大熵思想之前，需要了解两个人为定义的量【信息量】以及【熵】。定义【信息量】：$$-log\ P$$，即与概率负相关，当P越大（即可能性约大），样本确定性越大，其信息量越小；【熵】即【信息熵】，与化学中的熵含义类似，即体现整体样本的混乱度，即其样本集合信息量的期望值：

$$
Extropy=E_{p(x)}[-lop\ p]\\
=\int -p(x)log\ p(x)dx\ （连续的样本空间）\\
=\sum_x p(x)log\ p(x)\ （离散的样本空间）
$$


而我们希望我们手中的似然能够涵盖更多的样本分布情况，即样本的信息量能够更大、即其熵能够更大，故提出了**最大熵**的思想。我们令熵为$$H[p]$$，则最大熵即为$$max(H[p])$$。

#### 对于未知情况

结论：当变量完全未知时，最大熵取到时，变量完全随机，即等可能。

证明：
$$
max(H[p])=max(-\sum_xp(x)log\ p(x))\\
=min(\sum_xp(x)log\ p(x))\ \ \ \ s.t.\ \sum_xp_i=1
$$

通过拉格朗日乘子法：

$$
L(p,\lambda)=\sum_xp(x)log\ p(x)+\lambda(1-\sum_xp_i)\\
\frac{\Delta L}{\Delta p_i}=log\ p_i+p_i·\frac{1}{p_i}-\lambda=0\\
\Rightarrow \hat p_i=exp(\lambda-1) \ \ \ \ \ 即p_i为常数\\
\Rightarrow \hat p_1=\hat p_2=……=\hat p_k=\frac{1}{k}
$$


可以得到p(x)是均匀分布的，所以在没有已知样本的情况下，均匀分布的熵最大。

#### 对于有既定样本集合的情况

设已有既定样本$$D=\{x_1,x_2……,x_N\}$$，我们可以得到经验分布：


$$
\hat p(x)=\frac{Const(x)}{N}\\
eg.\ \hat p(x_1)=\frac{Const(x_1)}{N}, \hat p(x_2)=\frac{Const(x_2)}{N}\\
若x_1=x_2,则\hat p(x_1)=\hat p(x_2)
$$


我们在这种情况下利用最大熵思想时，需要以这些已有样本作为依据，即将这些样本作为约束条件。假设根据该样本得到的经验分布可以求得关于x的某个函数f(x)的期望值为：$$E_{\hat p}[f(x)]=\Delta$$，则有：


$$
max(H[p])=min\sum_xp_ilog\ p_i\ \ \ s.t. \sum_xp_i=1\\
E_p[f(x)]=E_{\hat p}[f(x)]=\Delta
$$


同样利用拉格朗日乘子法，可以得到：


$$
L(p,\lambda_0,\lambda)=\sum_xp_ilog\ p_i+\lambda_0(1-\sum_xp_i)+\lambda^T(\Delta-E_p[f(x)])
$$

两边对p(x)求导：

$$
\frac{\Delta L}{\Delta p(x)}=\sum_x(log\ p(x)+p_i·\frac{1}{p_i})-\sum_x\lambda_0+\lambda^T-\sum_x\lambda^Tf(x)=0\\
\Rightarrow \sum_x(log\ p(x)+1-\lambda_0-\lambda^Tf(x))=0
$$


又由于每个样本都是任意的，故有$$log\ p(x)+1-\lambda_0-\lambda^Tf(x)=0$$

$$
\Rightarrow p(x)=exp(\lambda_0+\lambda^Tf(x)-1)\\
=exp(\lambda^Tf(x)-(1-\lambda_0))\\
\Rightarrow \begin{cases}\eta^T=\lambda^T\\ \phi(x)=f(x)\\ A(\eta)=1-\lambda_0 \end{cases}
$$


即可得到，该p(x)即为指数族分布。
