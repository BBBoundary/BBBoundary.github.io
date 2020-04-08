---
layout:    post   				    # 使用的布局（不需要改）
title:    「Machine Learning」Math Basics # 标题 
subtitle:  Math basics(probability and mathematical statistics) for machine learning #副标
date:      2020-04-08 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img15.JPG        #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Machine Learning
    - Probability theory
---

这里是机器学习基础知识总结的开篇，是ML及相关领域所必要的数学基础部分（主要是概率论）。

在这一系列里，我将会总结上一个学期所铺垫下的ML基础知识（以西瓜书涉及的内容为主），以及课题方向研究过程中接触到的一些当前较为热门、较有学术价值的模型。

# 频率派 vs 贝叶斯派

 频率派和贝叶斯派之间的区别，可以从其对「事件不确定性」这件事的出发点和立足点不同说起。

频率派摈弃了「观测者」的因素，从「自然」的角度出发，试图直接对「事件」建模，即事件A在独立重复试验中发生的频率趋于极限p，那么该极限就是改事件的概率，即频率派所认为的「事件随机性」。例如抛硬币问题，只要我们不断抛掷硬币，当实验次数趋于正无穷时正面朝上的频率即为正面朝上的概率。在课程《概率论与数理统计》中大部分采用了频率派的观点。Machine Learning中频率派对应的方法为最大似然(对数)估计：


$$
\theta_{MLE}=\mathop {argmax}_{\theta}\ logP(x|\theta)
$$


由MLE方法可看出，频率派是用似然（即对已知$\theta$求x的条件分布）进行参数的最优拟合，即频率派认为，某一事件其背后的模型/参数是固定的，而基于该事件的一系列样本也一定能够表现出事件本身的特性。

而贝叶斯派并不试图刻画「事件」本身，绕开了「世界本体」，而是从「观测者」的角度，从「观测者知识不完备」这个出发点开始。即不同于频率派认为的「事件本身具有某种客观随机性」，贝叶斯派认为的是事件结果之所以是未知的，只是因为「观测者不知道事件的结果」，而对知情者而言该事件就是「确定事件」。

所以，相对于频率派对「事件」建模，贝叶斯派对观测者的「知识状态」建模，试图描述观察者知识状态在新的观测发生之后如何更新。为了描述这种更新过程，贝叶斯概率论假设观察者对某事件处于某个知识状态，即「前置信念(prior belief)」中（以下仍以抛硬币为例，如：小明先验地相信一枚硬币是均匀的，这是出于对硬币最基本的常识），之后观察者开始新的观测或实验（小明开始不断抛硬币，抛了100次，发现只有20次正面朝上），经过中间的独立重复试验，观察者获得了新的观测结果，这些新的结果以含有不确定性逻辑推断方式影响观察者原有的信念（小明开始怀疑这枚硬币并不是均匀的），得到「后置信念（posterior belief）」。在这一过程中，观察者无法用简单的逻辑来阐述其信念的依据，只能是基于实验结果进行推断，对各种各样可能的结果赋予一个「合理性(plausibility)」，也即「贝叶斯概率」(如经过刚刚的实验，小明认为抛硬币这个事件，「下一次得到正面」有1/5的可信度，「得到反面」有4/5的可信度，则1/5和4/5这些实数即为每种结果对应的合理性)，这个过程被称作「似真推断(plausible reasoning)」。在Machine Learning中贝叶斯派对应的方法为最大后验估计：


$$
p(\theta|x)=\frac {p(\theta*x)}{p(x)}=\frac {p(x|\theta)*p(\theta)}{p(x)}
$$

其中，由于x为观测样本，即p(x)是固定的。故有


$$
p(\theta|x)∝p(x|\theta)*p(\theta)
$$

$$
\theta_{MAP}=\mathop {argmax}_{\theta}\ P(\theta|x)\\
=\mathop {argmax}_{\theta}\ P(x|\theta)*P(\theta)
$$



由MAP可看出，贝叶斯派是通过后验概率
$$
P(\theta|x)
$$
进行迭代优化，又经过贝叶斯公式的转换，$$P(\theta)$$即为观察者当前状态下的「知识状态」,
$$
P(x|\theta)
$$
即为在该种知识状态下的似然，即体现出“用观察者的知识状态去judge这种推断是否恰当，然后不断迭代至最优”，贝叶斯派所认为的「参数是随机变量」也体现在此处。

贝叶斯派的这种设计思路在于解决一个核心问题——如何构建一个满足特定条件的逻辑体系，使得其能通过一个实数对观测者论断进行合理性判断，从而允许观测者在不完全信息的状态下进行推断。设已有已知样本X，现在要推断新事件$\hat x$，则有：


$$
P(\hat x | X)=\int_\theta P(\hat x,\theta|X)d\theta\\=\int_\theta P(\hat x|\theta)*P(\theta|X)d\theta
$$


此处可以看到，贝叶斯派利用已知数据对新数据进行预测时，利用在$\hat x$与$X$间的桥梁$\theta$：$X$—>$\theta$—>$\hat x$，但又同时避免了直接对$\theta$具体值的讨论，而是直接对整个$\theta$空间进行积分。当然由于直接对整个参数空间积分不现实，贝叶斯派后序也发展出了概率图模型及相应的算法（如MCMC采样）进行优化。

——————

频率派和贝叶斯派是统计推断领域的两种主张想法。概括来说，频率派主张将需要推断的参数$\theta$视作固定且未知的常数，而样本X是随机的，其着眼点在「样本空间」，有关的概率计算都是针对X的条件分布；贝叶斯派主张把参数$\theta$作为随机变量，而样本X是固定的，其着眼点在「参数空间」，更在意参数$\theta$的分布。

就我个人而言，我认为频率派更偏向于“事件未发生”的情景，将结果不确定性直接归结为事件不确定性；而贝叶斯派更偏向于“事件已发生”、即已有确定结果，将结果不确定性归结于观测者的认知局限性。贝叶斯派的学者所持的观点与经典力学科学家所拥有的的方法论类似，他们认定万物之理的确定性，并通过大量实验推断总结理论；频率派的学者所持有的观点像是量子力学科学家的世界观，他们承认事物本身的「不确定性」这一特点，并在此基础上对“不确定性”本身进行研究。



# 概率论补充

#### 1)高斯分布（正态分布）

这里以线性高斯分布(Linear Gaussian Model)为例：

![](/img/ML_MB1.png)

**ps:**这种线性高斯模型在P-PCA降维中非常常用，具体可以见后序博客。

统一规定：$$X(data)=(x_1,x_2,……x_N)^T\ \ \ \in R^{N*p}$$，其中，$x_i$独立同分布，有$$x_i\sim N(\mu,\sigma^2)$$。

则有：


$$
\mu_{MLE}=\frac {1}{N}\sum_{i=1}^Nx_i\\
\sigma^2_{MLE}=\frac {1}{N}\sum_{i=1}^N(x_i-\mu_{MLE})^2=\frac {1}{N}\sum_{i=1}^Nx_i^2-\mu_{MLE}^2
$$


其中，$$\mu_{MLE}$$为无偏估计，$$\sigma^2_{MLE}$$为有偏估计：


$$
E[\sigma^2_{MLE}]=E[\frac {1}{N}\sum_{i=1}^Nx_i^2-\mu_{MLE}^2]\\
=E[\frac {1}{N}\sum_{i=1}^Nx_i^2-\mu^2]-(E[\mu_{MLE}^2]-E[\mu^2])\\
=\frac {1}{N}\sum_{i=1}^NE[x_i^2-\mu^2]-(E[\mu_{MLE}^2]-\mu^2)\\
=\frac {1}{N}\sum_{i=1}^NE[x_i^2-E^2[x_i]]-(E[\mu_{MLE}^2]-E^2[\mu_{MLE}])\\
=\frac {1}{N}\sum_{i=1}^N(E[x_i^2]-E^2[x_i])-(E[\mu_{MLE}^2]-E^2[\mu_{MLE}])\\
=\frac {1}{N}\sum_{i=1}^NVar(x_i)-\frac {1}{N^2}\sum_{i=1}^NVar(x_i)\\
=\sigma^2-\frac {1}{N}\sigma^2\\
=\frac {N-1}{N}\sigma^2
$$

对于多维情况，可由以下步骤进行计算优化:


$$
P(x)=\frac {1}{(2\pi)^{p/2}\sigma}exp(-\frac {1}{2}(x-\mu)^T\sigma^{-1}(x-\mu))\\
x\in R^{p*1}\ \ \mu\in R^{p*1}\ \ \sigma^2\in R^{p*p} (\sigma 半正定对称)
$$



对$\sigma$可进行特征分解：

$$
\sigma=U·\Lambda·U^T\\
=(U_1,U_2……U_p)
\begin{pmatrix}
\lambda_1&0&……&0\\
0&\lambda_2&……&0\\
\vdots&\vdots&\ddots&\vdots\\
0&0&……&\lambda_p
\end{pmatrix}(U_1,U_2,……,U_p)^T\\
=\sum_{i=1}^pU_i\lambda_iU_i^T\\
\\
\\
\sigma^{-1}=\sum_{i=1}^pU_i\frac{1}{\lambda_i}U_i^T\\
$$


故原式有：


$$
(x-\mu)^T\sigma^{-1}(s-\mu)\\
=\sum_{i=1}^p(x-\mu)^TU_i\frac{1}{\lambda_i}U_i^T(x-\mu)\\
=\sum_{i=1}^py_i\frac{1}{\lambda_i}y_i^T\\
=\sum_{i=1}^p\frac{y_i^2}{\lambda_i}=\Delta\\
其中y_i=(x-\mu)^TU_i\\
$$


带入原式即可得到：
$$
P(x)=\frac {1}{(2\pi)^{p/2}\sigma}exp(-\frac {1}{2}\Delta)
$$



#### 2)求边缘概率及条件概率

将样本X分为$X_a$、$X_b$两个部分：


$$
X=\begin{pmatrix}X_a\\X_b\end{pmatrix}\ \ \ \ \ X_a\in R^m\ \\
\mu=\begin{pmatrix}\mu_a\\\mu_b\end{pmatrix}\ \ \ 
\sigma=\begin{pmatrix}\sigma_{aa}&\sigma_{ab}\\\sigma_{ba}&\sigma_{bb}\end{pmatrix}
$$


求：
$$
P(X_a)、P(X_b|X_a)
$$


①求$P(X_a)$：
$$
X_a=(I_m,0)\begin{pmatrix}X_a\\X_b\end{pmatrix}\\
\Rightarrow E[X_a]=(I_m,0)\begin{pmatrix}\mu_a\\\mu_b\end{pmatrix}=\mu_a\\
Var[X_a]=(I_m,0)\begin{pmatrix}\sigma_{aa}&\sigma_{ab}\\\sigma_{ab}&\sigma_{bb}\end{pmatrix}\begin{pmatrix}I_m\\0\end{pmatrix}=\sigma_{aa}
$$


故
$$
X_a\sim N(\mu_a,\sigma_{aa})
$$


②求
$$
P(X_b|X_a):
$$


构造：


$$
X_{b·a}=X_b-\sigma_{ba}\sigma_{aa}^{-1}X_a\ \ \ ①\\
\mu_{b·a}=\mu_b-\sigma_{ba}\sigma_{aa}^{-1}\mu_a\ \ \ \ \ ②\\
\sigma_{bb·a}=\sigma_{bb}-\sigma_{ba}\sigma_{aa}^{-1}\sigma_{ab}\ \ ③
$$


则有：
$$
X_{b·a}=(-\sigma_{ba}\sigma_{aa}^{-1},I_m)\begin{pmatrix}X_a\\X_b\end{pmatrix}
$$
带入可有：
$$
E(X_{b·a})=\mu_{b·a}\ \ \ \ \ \ Var(X_{b·a})=\sigma_{bb·a}
$$


又由①得$$X_b=X_{b·a}+\sigma_{ba}\sigma_{aa}^{-1}X_a$$。因为当求
$$
X_b|X_a
$$
时$X_a$被视为已知常数，故有：


$$
E[X_b|X_a]=\mu_{b·a}+\sigma_{ba}\sigma_{aa}^{-1}x_a\\
Var[X_b|X_a]=Var[X_{b·a}]=\sigma_{bb·a}\\
\Rightarrow X_b|X_a\sim N(\mu_{b·a}+\sigma_{ba}\sigma_{aa}^{-1}x_a\ ,\ \sigma_{bb·a})
$$


#### 3)求联合概率分布

 已知有：
$$
P(x)\sim N(x|\mu\ ,\ \Lambda^{-1})\\
P(y|x)\sim N(y|Ax+b\ ,\ L^{-1})
$$
求
$$
P(y)、P(x|y)
$$


①求$P(y)$:

对应于已知条件，令$y=Ax+b+\epsilon,\ \ \ \epsilon \sim N(0,L^{-1})$


$$
\Rightarrow E[y]=E[Ax+b+\epsilon]=A\mu+b\\
Var[y]=Var[Ax+b+\epsilon]=A^2\Lambda^{-1}+L^{-1}\\
\Rightarrow y\sim N(A\mu+b\ ,A^2\Lambda^{-1}+L^{-1})
$$


②求
$$
P(x|y):
$$


令
$$
z=\begin{pmatrix}x\\y\end{pmatrix}\sim N(\begin{pmatrix}\mu\\A\mu+b\end{pmatrix},\begin{pmatrix}\Lambda^{-1}&\Delta\\\Delta&A^2\Lambda^{-1}+L^{-1}\end{pmatrix})
$$


其中$\Delta=Cov(x,y)$:
$$
\Delta=E[(x-\mu)(y-A\mu-b)^T]\\
=E[(x-\mu)(Ax-A\mu+\epsilon)^T]\\
=E[(x-\mu)(Ax-A\mu)^T]+E[(x-\mu)\epsilon^T]\\
=E[(x-\mu)(x-\mu)^T]A^T+0\\
=Var(X)A^T\\
=\Lambda^{-1}A^T
$$


故此时可以得到$z$的分布，求P(x|y）可以根据前面求条件概率的方法，最终可得到：
$$
x|y\sim N(\mu_{xy}+\Delta·\Lambda·y\ ,\sigma_{xx·y})
$$
