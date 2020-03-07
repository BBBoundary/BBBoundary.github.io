---
layout:    post   				    # 使用的布局（不需要改）
title:    【CV-Visual Caption】Grounded-Description Model Explanation  # 标题 
subtitle:  2019_Luowei Zhou_Grounded Video Description   #副标题
date:      2020-03-07 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img7.PNG        #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - CV
    - paper reading
    - Visual Caption
    - Object localization
---

##### [Paper Download Address](https://arxiv.org/abs/1812.06587)

## Abstract

#### ①Question

当前已有模型大多简化了识别对象的难度，使caption生成的过程会更多依赖于先验而非真正在视频帧中存在的特征信息。

#### ②Method

将句子中的**noun phrase**（作者给出了定义，见下面Dataset部分~）在视频中注释出，即找到caption中名词生成的证据：

> In this work, we explicitly link the sentence to the evidence in the video by annotating each noun phrase in a sentence with the corresponding bounding box in one of the frames of a video.

为了达到这个效果，作者在数据集ActivityNet Captions上增加了158k个带注释的边界框，得到数据集ActivityNet-Entities。该数据集不仅可以用来训练模型，也可以用来评估描述的准确性。

<img src="https://upload-images.jianshu.io/upload_images/21878773-cc22bda2fa0e3005.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200" />

在该篇论文中，作者提出了一种能够利用该数据集训练的模型”novel video description model“，其包括三个部分：grounding、region attention、language generation。

#### ③Answer

在该篇论文中，作者为我们展示了该模型利用ActivityNet-Entities进行训练的有效性，以及将该模型应用到Image Caption时在Flickr30k-Entities(一个包括了对**图片**中对象进行注释的数据集)数据集上的有效性，并声称在video description、video paragraph description、image description应用上达到了state-of-the-art。



##  Dataset

**”ActivityNet-Entities Dataset“**

视频描述领域不像在图片描述领域有Flickr30k-Entities那样大范围的数据集，而数据集ActivityNet Captions仅包含了对20k视频的注释但缺乏框定目标的注释（grounding annotations），故作者在ActivityNet Captions的基础上新增了15k个视频以及158k个带注释边框，建立了ActivityNet-Entities数据集。

<img src="https://upload-images.jianshu.io/upload_images/21878773-696089e1fb481c5f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200" />

对于注释边框，作者给出了两点说明：

**a.**  不像图片，视频增加了时间维，i.e.即使是一个视频帧，其包含反映的信息也比一张图片要多。而视频描述的对象可能会出现在不相关(不相交)的视频帧中，为了得到更加精确细致的描述，作者提出了**noun phrase**的定义，并对其进行注释：

[Noun Phrase]短的、非递归的短语，其指定于图片中的特定区域，并能被一个bounding box包围。Noun Phrase可以是一个或一组示例，也可以包含形容词、限定词、代词或介词。为了更加精确，鼓励注释器将复杂的NPs分解为多个简单的NP，e.g. “the man in a white shirt with a heart” 可以分解为三个NP：“the man”, “a white shirt”, 以及 “a heart”。

**b. ** 理想状态下每一视频帧都会包含很多的区域注释,但对于训练开销而言，过多的注释会造成过大的负担，故ANet-Entities数据集主要是对视频帧进行了稀疏标注。



## Model

**"Description with Grounding Supervision"**

对于模型总的loss function有：
$$
L=L_{sent}+\lambda_{\alpha}L_{attn}+\lambda_cL_{cls}+\lambda_{\beta}L_{grd}
$$
其中$L_{sent}$表示组织语言时的损失,$L_{attn}$表示region attention模块编码时的损失，$L_{cls}$表示定位模块对对象进行分类时的损失，$L_{grd}$表示对对象进行定位时的损失（以上损失均用交叉熵）。$\lambda$均从验证集的结果中选取。

**该模型中region attention和language generation都是传统方法，具体可以见我的上一篇博客《【CV-Visual Caption】CAM-RNN……》，此处仅解释grounding的部分**

**-Grounding Module-**

grounding module为region attention的前置模块，其将区域特征信息加工，使其富有类别信息和时空信息，并将作为新的区域特征输入至region attention中。令原始区域特征为R $\in R^{d\*N}$，区域分类相似矩阵为$M_s(R)$，时空信息嵌入矩阵为$M_l$，则最终得到的新区域特征矩阵为$\hat R=W_g[R|M_s(R)|M_l]$,（p.s. [·|·]代表矩阵连接），作者将其命名为”the grounding-aware region encoding“。

①区域分类相似矩阵$M_s(R)$

定义一个对象类别标签${c_1,c_2,……c_k}$，其中k为类别总数。grounding module会评估每个区域从属的类别的概率分布：

定义一个对象分类矩阵$W_c=[w_1,w_2,……w_k]\ \in R^{d\*k}$,以及偏移项$B=[b_1,b_2……b_k]\ \in R^{d\*k}$, 则对于原始区域特征矩阵R(embedding结果)，可以定义区域分类相似矩阵：
$$
M_s(R)=softmax(W_c^TR+B)
$$
其中,使用RuLU函数对$W_c^TR$进行激活，Dropout机制防止过拟合，softmax函数用以该矩阵最终的规一化。

而对于$W_C^T$以及$B$的初始化，论文中提到使用了pre-trained测试器（detector）——Visual Genome（VG）。通过在嵌入空间中，从VG中分别找到k个分类对应的最近邻对象，即利用VG来规定Wc所含的内容，并由迭代过程最后的线性层对Wc和B进行初始化。

②时空信息嵌入矩阵$M_l$

对于每个region先用五元元组(5-D tuple)进行表示，其中，四维用于表示该区域的空间信息（由于每个区域是一个box范围，需要一个左下点和右上点，即两个平面坐标信息），一维用于表示其所在帧的序列信息（即时间信息）。然后将该元组映射到300元元组（300-D tuple）中，作为该区域的特征嵌入值，用以区分于其他所有区域。

最后将所有区域的特征值合并，即可得到时空信息嵌入矩阵$M_l\ \in R^{300*N}$

——————

由此，得到的$\hat R$即为在不知道上下文语义信息的前提下，带有对象分类信息的矩阵。以下介绍对于该模块的两个loss function：$\lambda_{cls} \ \ \lambda_{grd}$的计算方法：

①Region Classification：

这里以IoU(交并比)作为评价指标，利用已有的任意的ground-truth(GT) box（GT box：在监督学习中已经被确认为检测正确的区域），根据region与GT box之间的IoU，若大于0.5则认为该区域分类正确；若有多个GT box使其满足分类正确的条件，则取使IoU最大的box作为其分类，这里我们令该分类为$c_j$。根据前面对Ms(R)矩阵的定义，行代表类别，列代表对应的region，则该(第i个)区域提供的交叉熵loss为：
$$
L_{cls,i}=-logM_s[j,i]
$$
最终的$L_{cls}$为所有postive region所提供的loss之和的平均值（要除去个数带来的影响）。

此处用IoU指标对每个region进行判断时需要将分类成功与否记录到矩阵$\gamma$中

②Object Grounding

对于第t+1步需要生成的单词$s_{t+1}$，我们希望能够在原视频帧中定位它。此处假设$s_{t+1}$分类到了第j类，则我们需要用第t步时得到的分类相似矩阵$M_s^t(R)$和其对应的分类判定矩阵$\gamma^t$对$s_{t+1}$是否定位成功进行loss估计：
$$
L_{grd}=-\sum_{i=1}^N\gamma_i^tlogM_s^t[j,:]
$$
最终的$L_{grd}$也是需要对所有visual-grounded words进行求平均。



## Talk

​		以上就是这篇论文的核心内容啦~讲真虽然是自己看的第二篇paper，这篇真的是看得我飞起了……看了一眼Abstract里面提到“简化模型的弊端”、“定位”就决定看这篇，甚至就直接跳到Grounding Model的地方了……（该打）好歹也是一篇CVPR2019还看的这么浪，吃枣药丸（狗头）。

​		但是硬要说的话观感还非常不错。选择这篇的初衷也是因为小组系统的应用场景是辅助视障人士观影，而电影的话是需要长时间不断输入视频信息的。现有的数据集中视频时长就过短，往往就会忽视描述会过度依赖先验的问题。而电影中往往场景多、内容杂，当电影内容发生改变（如故事发生的地点场景、人物变化等）时，原先的模型可能就会因为先验而生成与视觉内容不相关的描述（即无中生有），所以选择在我们原有模型的前面加入这篇paper中的Grounding Model，虽然感觉复现的时候头会没掉——

​		那么这就是第二篇paper总结博客。关于这篇涉及到的Dropout（当然这个很常用），IoU指标，我会之后单独写博客233（躺
