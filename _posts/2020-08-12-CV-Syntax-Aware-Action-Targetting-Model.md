---
layout:    post   				    # 使用的布局（不需要改） 
title:    「CV-Visual Caption」Syntax-Aware Action Targetting Model   # 标题 
subtitle:  2020_Qi Zheng_Syntax-Aware Action Targetting for Video Captioning   #副标题
date:      2020-08-12 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img7.PNG        #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - CV
    - Visual Caption
    - paper reading

---

**[Paper download Address](https://arxiv.org/pdf/1906.04375v1.pdf)**

### Abstract

#### ①Question

目前的方法对物体检测已经在物体检测方面有了较好的表现，而很少有方法强调对象之间的相互作用，即句子中的动作（谓词）。谓语通常需要静态语义和动态语义共同决定，所以现有方法的谓语通常取决于相关目标的同时出现，而这往往不能生成理想的动作描述。

#### ②Method

作者提出了一种SAAT（Syntax-Aware Action Targetting）模块，用于生成句子的各个成分（eg. subject、predicate、object）和动态语义：

1. 先通过各对象间的全局相关性来确定主语
2. 将主语、视频的时序信息嵌入到一个空间中
3. 从该空间中解码出句子的谓语、宾语成分，并结合caption生成器生成当前单词

#### ③Answer

该模型在MSVD上的CIDEr指标较state-of-the-art提高了2.7%，在MSR-VTT上提高了5.9%，说明该方法提高了动作识别的准确信，使得视频与动态语义有了较高的一致性。





### Introduction

#### ①Motivation

存在的video caption方法中，多数方法对如何进行识别对象（object）进行了优化，很少有方法关联到对动作的识别（即句子中的谓语成分）；而比起静态的对象，动作需要同时考虑动态时序信息和静态语义。这种缺陷也无法仅依靠最小化交叉熵得以解决，因为存在的单词可以匹配已有的句子（humen-annotated sentence），会因此生成对应的句子，尽管其action是错误的。

#### ②SAAT Model

两点Contribution：

- 形成了一个关于该视频片段的场景描述表示（self-Attended Scene Representation），用于对object间关系的建模；再通过设置不同的查询，对各种不同语法成分进行解码（eg. subject，object，predicate）
- action-guided captioner：不同于一般的方法，该模型在解码阶段除了使用前一步生成的单词进行引导，还额外利用从场景描述中提取出的**动作（action）**作为guidence。





### Model

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghohue6ct0j30oe08madq.jpg)

#### ①Self-Attended Scene Representation

对于每一个video clip，取其中间的一帧（center frame），利用object detector生成对应的$$V^b$$（区域特征，regional feature），作为**Cxe**(Component extractor-encoder)的输入特征，通过训练使得$$V^b$$能够被映射成为一个连续的表达$$V^{b'}$$，该表达即为习得的场景表达(scene representation)，通过**Cxd**（Component extractor-decoder）生成对应的句子成分（主谓宾等）。

该部分有两个输出：①场景表达$$V^{b'}$$ ②标量化的attention得分计算函数 $$f_{att}(Q, K, V)$$

其中，对于询问、key、value序对的attention得分函数为：


$$
f_{att}(Q,K,V)=softmax(\frac{QK^TV}{\sqrt{d_k}})\ \ \ \ d_k为Q的维度\\
(Q,K,V)=(R_cW^Q,R_cW^k,R_cW^V)\\
其中，W^Q、W^k、W^V均为学习得到的参数矩阵\\
R_c=ReLU([W_l^TR_l;W_b^TV^b])\\
R_l=[X,Y,W,H]=
\begin{bmatrix}
x_1'&x_2'&……&x_k'\\
y_1'&y_2'&……&y_k'\\
w_1'&w_2'&……&w_k'\\
h_1'&h_2'&……&h_k'
\end{bmatrix}=[r_{l1},r_{l2},……,r_{lk}]\\
其中\ \ r_{li}=[\frac{x_i}{w_f},\frac{y_i}{h_f},\frac{w_i}{w_f},\frac{h_i}{h_f}],w_f、h_f为视频帧的宽度和高度
$$






#### ②SAAT Captioning

SAAT部分有两个任务：①通过前置操作得到的场景表示（Vb'）、全局特征（Vr’，RGB feature，由2D CNN给出）、时序动态特征（Vm‘，temporal feature，由3D CNN给出），解码出主语（subject）、动作谓语（predicate）、宾语（object）②辅助LSTM，解码出最终的caption。

对于第一个任务，具体流程为**a.解码主语s（Vb'，Vr'）——>b.解码动作谓语a（s，Vm'）——>c.解码宾语o（a，Vb'）**:


$$
主语s解码以场景表示V^{b'}和全局特征V^{r'}为指导：\\
s=arg\,\max_{w\in vocab}p_\theta(w|V^{b'},V^r)\\
p_{\theta}(w|V^{b'},V^r)=softmax(w_s^Tf_{att}(V^{r'},V^{b'},V^{b'}))\\=softmax(w_s^Tsoftmax(\frac{V^{r'}V^{b'T}V^{b'}}{\sqrt{d_k}}))\\
动作谓语a解码以主语s和时序动态特征V^{m'}作为指导:\\
a=arg\,\max_{w\in vocab}p_\theta(w|s,V^{m'})\\
p_{\theta}(w|s,V^{m'})=softmax(w_a^TReLU([E_s;V^{m'}]))\\
宾语o解码以谓语a和全局特征V^{b'}为指导：\\
o=arg\,\max_{w\in vocab}p_\theta(w|a,V^{b'})\\
p_{\theta}(w|V^{b'},V^r)=softmax(w_s^Tf_{att}(E^o,V^{b'},V^{b'}))\\=softmax(w_s^Tsoftmax(\frac{E_oV^{b'T}V^{b'}}{\sqrt{d_k}}))\ \ ，其中E表示对下标单词的embedding
$$


![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghohvg1dysj30hl08ngnw.jpg)

可以看到，SAAT在解码主谓宾时有严谨的先后次序，使解码时技能充分利用视频特征，如场景表示、全局2D特征在确认对象物时足够，所以利用全局特征来引导主语和宾语的产生，而动作需要时序动态信息，所以利用时序动态特征对动作的解码进行引导；又使得主谓宾之间的衔接更为流畅，即主语引导动作，动作引导宾语。

对于第二个任务，模型同样采用attention机制，先生成一个attention分布：


$$
\beta_{t,j}=softmax(v_B^Ttanh(W_\beta^TE_{yj}+W_hh_{t-1}+b_\beta))\\
s.t.
\begin{cases}
y_j\in \{y_a,y_{t-1}\}\\
\sum_j\beta_{t,j}=1
\end{cases}
$$


其中$$y_j$$即表示用于引导的单词，可以看见用于引导的单词为SAAT生成的谓语单词和句子的前一个单词；当然同时注意力得分分布也需要满足归一化条件。

LSTM解码部分：


$$
p_\theta(y_t|y_j)=LSTM(\sum_j\beta_{t,j}E_{y_j},W_V\overline x,h_{t-1})\\
y_0:given\ by\ bos\ token\ \ \ \ \ \ \  h_0:zero\ vector
$$




#### ③Training & Inference

通过该模型的编码、解码流程和优化逻辑，可以得到损失函数分为两个部分：由caption生成器产生的loss、由SAAT在生成主谓宾时产生的loss：


$$
L(\theta)=L_c+\lambda L_{SAAT}\\
L_c=-\sum_{i=1}^N\sum_{t=1}^{T_i}log\ p_\theta(y_t=y_t^*|y_{1:t-1},y_a)\\
L_{SAAT}=-\sum_{i=1}^Nlog\ p_\theta((s,a,o)=(s^*,a^*,o^*)|V^{[b,r,m]})
$$


其中Lc处表示用于训练的共有N句，每一句分别对应Ti个时间结点，每一句话都有一个ya用于引导；Lssat处表示对于统一提供的场景表示、全局特征、时序动态特征下对生成主谓宾的概率进行计算，其中用于参考的$$(s^*,a^*,o^*)$$为NLP tool（www.nltk.org）产生的结果。



### Tables

#### on MSR-VTT

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghohzmledej30n90cldjs.jpg)

#### on MSVD

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghoi0ok1byj30bd0bpq4p.jpg)



### Talk

以上便是SAAT Model的介绍~这是一篇CVPR2020中较少Visual Caption方向的其中一篇，也是我个人第四篇有关该课题的论文。文章动机非常清晰，当下模型较多关注对象识别的正确性，而鲜有对动作识别的优化，所以设计了该Action Targetting的模型。根据上一学期的分析结果，在动作正确性上我们的模型同样还需要进一步改进，这篇paper就为我们提供了改进思路。

整体模型非常流畅，由各类卷积网络先生成各个特征，再交由解码器解码各种信息量，每个输出量所需要的引导量，及其路径清晰可见，各个步骤间体现的量与量间的因果关系也非常易懂，全流程紧扣action，用于训练优化的loss function也能很好地体现SAAT对于模型的贡献。

正如作者所说，该模型通过提供给caption生成器除了语义先验（linguistic piror）以外的guidence，即动作谓语a，使模型提高了对动作识别的正确性，从而提高了caption的生成质量；但相对的，3D CNN提供的全局时序信息并不是一直都是足够的，需要改进方法以提高卷积网络对动态信息语义提取的精确性；此外，在我阅读时，可以看到该模型涉及到的参数矩阵非常非常多，提取出的场景、全局、时序特征不能直接使用，需要进行mapping使其成为连续的表达后才能使用，而softmax、attention Dist、LSTM卷积中涉及到的learned parameter更是数不甚数。

而对于我们大创项目，可以说该模型是不能直接使用的，因为根据完整的一句话需要同一个action进行引导这一点，注定了该模型不能原模原样地在实时任务中使用，只能用于离线任务。但该模型向我们提供了一种最大程度利用特征信息的模型设计思维，这一点是非常值得学习的。