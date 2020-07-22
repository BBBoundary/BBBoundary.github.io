---
layout:    post   				    # 使用的布局（不需要改）
title:    「CV-Action Recognition」ECO Network for Online Video Understanding # 标题 
subtitle:  2018_Mohammadreza Zolfaghari_ECO:Efficient Convolutional Network for Online Video Understanding  #副标题
date:      2020-07-22 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img7.PNG        #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - CV
    - Action Recognition
    - Online Video Understanding
    - Visual Caption
    - paper reading

---

**[Paper Download Address](https://arxiv.org/pdf/1804.09066.pdf)**

### Abstract

#### ①Question

- 虽然现有的局部方法具有快速的逐帧处理能力，但对整个视频的处理效率不高，阻碍了对长程视频的快速检索、在线分类等工作。

#### ②Motivation

- 使用单帧的图像，在很多情况下已经可以获得一个不错的初始分类结果，而相邻帧间的很多信息都是冗余的。因此，ECO中在一个时序领域内仅使用单帧图像。
- 为了获得长时程的图像帧（即不同时段采样提取出的图像帧）间的上下关系，仅仅使用简单的分数融合（aggregation）是不足够的。因此，ECO中将先前通过2D卷积叠加得到的feature map进行3D卷积，从而获取temporal context

#### ③Model/Methods

![](/img/ECO_1.jpg)

#### ④Conclusion

在与现有state-of-the-art方法所差无几，保证良好性能的前提下，比SOTA等方法快10-80倍。该网络也可以用于视频描述生成，最快可达到每秒230个弹幕。同时，作者也将该网络用于online video understanding（网络足够快且效果足够好），同时实现了一个网络offline/online两用。



### Related work

#### ①Classification work in DL

- 传统方法：3D kernel、Resnet、C3D、CNN等
- 共同点：基于sliding window，都需要计算多个窗口的平均值，需要有额外的时间开销；此外，特征分数融合会减慢视频处理的速度，让这些方法变得无法实时处理。

#### ②Long term representation learning

为了解决局部处理方法下无法掌握全局时间信息的问题，一般方法有以下几种

- 大部分2stream类方法和所有3D卷积类型的方法都是在学习短视程的时序信息，及输入一个连续的video clip，输出一个分类结果。在video-level上，通常是对video中选取多个clip分别得到分类结果，在进行平均得到最后的分类结果，这样会带来比较大的计算开销。
- increase temporal resolution of sliding window，加长每个局部时域的长度，但计算量快速增大，且仍无法覆盖整个视频的信息
- encoding methods，通过对每一帧进行独立编码得到对视频的representation，再将所有帧的信息按时间序列平均融合。这种方法忽视了帧与帧之间的关联，即忽视了时间维度的信息，也忽视了冗余的问题。
- 与ECO最相似的是目前被广泛使用的TSN网络。【相似点】两者都是从video中均匀采样固定数量的视频帧，从而来覆盖长时程的时序结构，两者都是end2end训练的。【不同点】TSN中直接用late-fusion的方式对每个segment的结果做融合，而ECO中则是进行了feature map层面的融合（2D卷积->3D卷积）。此外ECO的速度要远远快于TSN。

#### ③Video Caption

对于视频描述任务，提取出的video feature需要同时包含视频帧的静态语义和temporal context，而很多方法都没有办法有效地提取出temporal context，而作者声称ECO可以有效提取，从而提升最终caption的效果。

#### ④Real-time and online video understanding

实时的视频理解工作目前还非常少。很多方法都需要开销非常大的分段计算工作（expensive segmentation method），而最近Singh et al. 提出了一种基于40fps帧级检测的在线检测方法，作者将会在Experiments部分详细比较几种方法的效果。



### Long-term Spatio-temporal Architecture

#### ECO Lite

2D Net采用BN-Inception（Inception-3c layer），3D Net采用3D-Resnet18中的部分层。

1. 分区。将原视频等长分为N个段，$S_i, i=1,2,……N$。ps.当然此处作者想通过更聪明的划区手段，但需要对帧进行预处理，即需要额外的开销。而实际上效果提升不大，即性价比不高。
2. 对每个分区随机抽取一帧（即共N帧），放到2D Net中处理得到96个$28*28$大小的feature map，堆叠后可得到$N*28*28*96$大小特征的volume。
3. 对于得到的特征volume，采用一个3D Net进行处理，直接输出对应动作类别数目的一维向量。

#### ECO FULL

ECO-Full在ECO-Lite的基础上新增了一条2D网络分治$2D Net_s$，$2D Net_s$采用BN-Inception网络中inception-4a到最后一个pooling层间的部分，最后采用average-pooling得到video level的表示（同为一维向量），最后与3D Net的结果concat后即可的到最后的action分类结果。



### Test time inference

在TSN或者是ARTNet等方法中，测试时通常都会对每段视频进行多次的cropping采样以及水平翻转等数据增强，从而增强测试的效果。这使得计算的效率降低，因而难以用于实用场景。

而ECO中对于一个视频，只需要计算一次前向就可以获得很好的效果，而不需要进行任何额外的融合或是增强，提高了计算速度。



### Online Video Understanding

- 一般工作：sliding window（以N帧作为一个窗口）。
- 限制：滑动窗口大小的设计、长时程情况下的语义缺失、可能造成较长的延迟。

ECO-Online：提取特征的网络结构不变，仅在采样部分进行改变。对于一般的离线工作，模型可以得到完整的视频，在将其分为N端并各取一帧后就可以进行分析了；而在线工作时模型是不能看到视频的全部内容的，视频只能以视频流的形式，由网络实时进行帧采样、分析、推断。这里作者给出了帧的采样算法：

![](/img/ECO_2.png)

简单来说，在采样部分需要维护两个队列，一个是工作记忆队列$S_N$，一个是新接收队列Q，用于存放从视频流接收得到的每一帧。两个队列的容量都是N，当Q中达到N帧后，$S_N$与Q个采样N/2帧，清空$S_N$和Q并将新的N帧放入$S_N$中；同时也将这N帧喂到卷积网络中，最后将该N帧的预测结果与平均预测结果进行平均得到最终的预测输出。

**这样的采样设计有以下几个优点：**

1. 确保可以实时预测处理

2. 通过每一轮更新，逐渐淡出局部权重，每次都以当前全局P为主(权重系数恒为1/2)。

   ![](/img/ECO_3.jpg)

3. 当k非常大时，每个时间段对最终概率的贡献趋近于相等。此处我们假设每一个时间段的贡献是通过最终概率计算中的权值，与每个时间段被抽取到的概率期望加权求和得到的。其中当当前读取到了k端N帧的video clip时，最终概率计算为：

   
   $$
   P=\frac {1}{2^{k-1}}P_1+\frac {1}{2^{k-1}}P_2+\frac {1}{2^{k-2}}P_3+……+\frac {1}{2}P_k
   $$
   
   而每次采样时，根据算法可得，对于前k-1段，每个时间段被采集到的概率为$N*\frac {1}{2(k-1)}$，由于每一段都为N帧，比较时可以忽略，即设每一段为$\frac {1}{2(k-1)}$，最后一段为$\frac {1}{2}$。即下图所示：

   ![](/img/ECO_4.jpg)

   纵向加权相加，即可得到第i段时间段的影响因子$F_i$为
   
   $$
   F_i=\sum_{j=i+1}^{k}\frac {1}{2^{k-j+1}}\frac {1}{2(j-1)}+\frac{1}{2^{k-i+1}}\frac{1}{2}
   $$
   

将其打表（取k=4，5，……30）可得到每一段的影响因子如下：

![](/img/ECO_6.png)

可以发现当k逐渐变大时，前面P1、P2这些较前面的局部概率的权重以指数级减小，即对于前面段其真正有影响的部分为最后的几个时间戳，而此时他们的权重相等、概率相等，使得最终的影响因子几乎相等。这一点使得“平均采样”的前提不会因是online工作而被破坏，即即使online工作与offline工作在采样部分不同，作者提出的采样算法，在影响因子期望的角度上，几乎也做到了与离线工作中采样的同样效果。



### Tables

①ECO在减少了大量计算开销的基础上，保持了较为良好的效果。其中，在基准数据集上进行比较的结果如下图所示（注：此处限定了RGB图像。可以看出ECO在用Kinetics预训练的前提下获得了较好的效果；此外在Kinetics和Something-Something数据集上，ECO也获得了很好的效果）：

![](/img/ECO_7.png)

②精度-速度比远远高于其他方法。ECO的主要卖点也在于其速度之快，下表就展示了ECO和其他方法的速度比较（基于单卡Tesla P100），其中ECO的下标表示输入帧数N。此处作者使用的衡量单位是VPS，即模型每秒能够处理的视频的数量。

![](/img/ECO_8.png)

很多方法为了获得video-level的结果，对于每个视频需要进行多次inference，而ECO只需要进行一次。下图则展示了速度-精度的效果，可见ECO的优势还是很大的。

![](/img/ECO_9.png)

③Video Captioning方面，作者通过连接ECO网络和Semantic Compositional Network（SCN，一种video caption模型）在MSVD数据集上测试得到以下结果：

![](/img/ECO_10.png)

此外，作者列举了六组评价效果，即用单独SCN和ECO(Lite-16F、FULL-32F、FULL-32F+resnet)。第一行为ECO提升了SCN描述效果的样例，第二行为ECO降低了SCN描述效果的样例。

![](/img/ECO_11.png)

④Online Mode下，在线视频分类问题通常以early action recognition的形式来评估。如下图所示，横轴表示可以看到视频的前百分之几，纵轴是对应的精度。基于下图的在J-HMDB数据集上的实验，可以看出ECO在精度上有明显的优势，并且只需要很小一部分视频就可以达到不错的精度。

![](/img/ECO_12.png)



### Talk

   可以说，这篇paper提及到的工作ECO网络的核心优势、及其网络结构非常明显：一是于TSN方法类似的采样方法，二是通过3D卷积来做融合，从而得到时序信息。当然，采样、特征提取&融合也正是特征提取网络本身最终要的两点。

   这篇paper的亮点非常明显。一是其直观又非常有现实意义的motivation。团队做这个工作不仅仅是为了能够让他们的模型在某个数据集上的指标提高那么一两个点，而是为了让模型本身能够在广泛运用到实际需求非常广泛的“实时性”工作中，且全文字里行间紧扣“性价比”；二是简单有效的框架设计。从网络结构上来看，该网络的参数量、计算量应该都比较小，不会是非常深的网络。而在这种情况下，ECO的效果比许多state-of-the-art相差也不是很多，但运行效率远远超出其他方法；三是Tables可视化做得非常好，对我启发很大。

   当然，作者自己在采样部分自己也提到，希望能找到更加clever的分段采样方法，当然肯定是需要对已采样的视频帧进行inference的（肯定是先有鸡再先有蛋）而能否设计出轻量化的、又不需要额外消耗时间、内存的网络就有待未来工作的开发了。

   这篇可以说是我读的第一篇有关特征提取网络的paper，在上一学期中几乎所有的学习精力都放在了学习video caption模型上，对于特征提取网络基本上就只是知道最常用的Resnet等，拿来就用，确也没有考虑过作为整个caption模型的输入，特征提取部分的重要性，更不用说实时性效果实现了。而这篇paper向我提供了一种非常非常轻量化的、甚至可以在小型计算机上实现的卷积网络，让我更充分相信这个项目最后能够更好地实现。



