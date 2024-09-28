<<<<<<< HEAD
---
tags:
  - FSS
  - Prototypes
---

=======
>>>>>>> 516cbd4495f625d0ca6b3b21239e3652645bffc2
# Few-Shot Medical Image Segmentation with High-Fidelity Prototypes

Owner: hikari

[https://github.com/cheng-01037/Self-supervised-Fewshot-Medical-Image-Segmentation](https://github.com/cheng-01037/Self-supervised-Fewshot-Medical-Image-Segmentation)

[https://github.com/tntek/DSPNet](https://github.com/tntek/DSPNet)

### 数据集

https://zenodo.org/records/5903037#.YfDOl-pBybh

https://github.com/JunMa11/AbdomenCT-1K?tab=readme-ov-file

依赖兼容性问题

训练集处理代码**SSL_ALPNet**

模型训练

测试集数据处理

### **数据集任务设置**

训练集和测试集的选择动机: 一个在完全监督的器官中，通常需要大量的训练集分割。因此，我们选择 MSD Pan. Plus (281)作为训练集中的基础数据集，因为它拥有最多的训练案例。在MSD Pan. Plus (281)基础上，将不同的案例添加到训练集，构建两个子任务，如表5所示。

1. 子任务1。训练集由MSD Pan. Plus (281)和NIH Pan. Plus (80) 组成，所有CT扫描都来自 portal phase。我们使用5.1.2节中的基线模型来预测LiTS Plus, KiTS Plus, and Spleen Plus中所有剩余的case。然后选取DSC和NSD平均值最低的50个case作为测试集。这些cases通常异构性病变，边界不清，分割难度大，但在临床实践中也很重要。
2. 子任务2。 NIH Pan. Plus (80)被LiTS Plus的40个案例和KiTS Plus的40个案例所取代，这些案例与测试集具有相似的phases。通过这种方式，我们可以评估在训练和测试集中包含 shared contrast phases是否能够提高性能。

我们使用5.1.2节中的基线模型来推断所有剩余的案例，并选择100个案例作为最终的测试集，其中包括平均DSC和NSD得分最低的50个具有挑战性的案例，以及50个随机选取的案例。最重要的是子任务上的训练集和测试集是没有交集的。

## 主要工作

(1)一种新的原型FSS方法DSPNet，增强原型的自我表示的复杂的细节，完全不同于以往的增量模式，构造新的细节原型。

(2)提出了一种类原型的自精化方法FSPA，它集成了类簇原型，所挖掘的语义细节以类似注意力的方式转化为增强的语义细节，并且指示融合基于聚类的局部细节以用于完整前景表示的潜力。

(3)一种用于背景原型的自细化方法BCMA，其通过多头通道注意与稀疏通道感知正则化来合并通道特定的结构信息，并为背景细节建模提供概念上不同的视图。

## 总结

这篇论文的标题是《Few-Shot Medical Image Segmentation with High-Fidelity Prototypes》，作者是Song Tang等人，发表在《Medical Image Analysis》期刊上。论文主要研究的是如何在只有少量标注样本的情况下，对医学图像进行精确的语义分割。这个问题在医学图像分析领域尤其重要，因为获取大量精确标注的医学图像样本既困难又耗时。

**摘要和介绍：**
论文指出，尽管现有的基于原型的方法在一些场景中取得了成功，但它们在处理医学图像时存在局限性，因为医学图像通常包含高度复杂的背景和难以区分的对象。为了解决这个问题，作者提出了一种新的Detail Self-refined Prototype Network (DSPNet)，用于构建高保真原型，更全面地表示前景对象和背景。

**相关工作：**
论文回顾了医学图像分割和少样本语义分割（Few-shot Semantic Segmentation, FSS）的相关研究，包括基于支持图像的指导、注意力模块和原型网络等方法。

**问题陈述：**
在少样本分割的情况下，数据集包含训练子集和测试子集，它们不共享类别。目标是在训练子集上训练一个分割模型，以便在只给定少量标注样本的情况下，对测试子集中的未见类别进行分割。

**方法论：**
这是论文的核心部分，作者提出了DSPNet，它包括三个主要模块：

1. **特征提取器**：使用CNN基于支持图像和查询图像提取特征。
2. **细节自精炼块（Detail Self-refining Block, DSR）**：通过自监督框架生成高保真前景原型和背景原型。
3. **基于余弦相似度的分割块**：通过计算查询特征和获得的原型之间的余弦相似度来进行分割。

**细节自精炼（Detail Self-refining）**：

- **前景语义原型注意力（Foreground Semantic Prototype Attention, FSPA）**：通过超像素聚类挖掘前景的语义原型，然后通过一维卷积的方式将它们融合成单一的类别原型，同时保持全局和局部语义。
- **背景通道结构多头注意力（Background Channel-structural Multi-head Attention, BCMA）**：利用多头通道注意力和稀疏通道感知正则化，将通道特定的结构信息整合到背景原型中。

**实验：**
作者在三个具有挑战性的医学图像基准数据集上进行了广泛的实验，展示了DSPNet相较于之前最先进方法的优越性。

**结论：**
论文最后总结了DSPNet的主要贡献，并指出这是首次尝试通过细节自精炼来增强现成原型的细节表示能力。

整体而言，这篇论文提出了一个新的网络架构，通过自精炼机制改进了医学图像少样本分割的性能，特别是在处理复杂背景和前景时。作者通过一系列实验验证了所提方法的有效性，并与现有技术进行了比较。

## FSPA工作原理

1. **超像素聚类**：
    - FSPA首先使用超像素聚类方法来挖掘图像中的局部区域。超像素是图像中具有相似颜色、亮度或纹理的像素集合，它们作为图像的基本构建块，有助于捕捉图像的局部结构信息。
    - 通过这种方法，模型能够识别出图像中具有清晰语义的区域，如不同的组织或器官部分。
2. **语义原型挖掘**：
    - 在识别出超像素后，模型进一步从这些区域中挖掘语义原型。这些原型是代表特定类别（如肿瘤、血管等）的特征向量，它们是从支持图像中提取的，用于描述和识别相似的前景对象。
3. **注意力机制**：
    - FSPA利用注意力机制来整合从超像素聚类中得到的多个语义原型。这种注意力机制类似于查询-键-值（Query-Key-Value）模型，其中查询（Query）是融合后的支持特征，键（Key）和值（Value）是聚类得到的原型。
    - 通过计算查询与所有键的相似度，模型能够为每个原型分配一个权重，这些权重表明了原型对当前查询图像的相关性。
4. **通道融合**：
    - 计算得到的权重用于将各个原型融合到一个单一的前景语义原型中。这一步骤通过一维卷积实现，确保了在整合全局语义信息的同时，也保留了局部的细节信息。

### Cluster 机制

1. **超像素生成**：
    - 利用超像素算法（如SLIC、SEEDS等）将图像分割成多个区域，这些区域在视觉上具有相似的特征（如颜色、纹理）。
    - 每个超像素可以视为图像中的一个小块，包含了相对一致的视觉属性。
2. **特征提取**：
    - 对于每个超像素，提取其特征向量，这些向量通常通过卷积神经网络（CNN）获得，能够捕捉到局部区域的视觉和语义信息。
3. **聚类分析**：
    - 使用聚类算法（如K-means、DBSCAN等）对提取的超像素特征进行聚类，以识别具有相似特征的超像素组。
    - 每个聚类代表一组具有相似特征的超像素，这些聚类有助于识别图像中的特定结构或模式。

### Cluster Prototypes 的生成

1. **原型定义**：
    - 每个聚类生成一个或多个原型（cluster prototype），这些原型是该聚类中所有超像素特征的统计表示（如均值、中位数或加权平均）。
    - 原型本质上是特征向量，它们编码了属于同一聚类的超像素的共同视觉和语义属性。
2. **与掩码的结合**：
    - 在医学图像分割中，如果有可用的标注掩码（标注了特定类别的图像区域），可以使用这些掩码来指导聚类过程或后处理步骤，确保聚类结果与实际的解剖结构对齐。
    - 掩码可以用来加权聚类过程中的超像素特征，或者在聚类后用于调整原型，以更好地匹配目标结构。

### Cluster Prototypes 的性质

- **形式**：Cluster prototypes 是特征向量的集合，通常表示为矩阵或张量，其中每一行代表一个原型。
- **信息内容**：
    - **视觉信息**：包括颜色、纹理、形状等视觉特征。
    - **语义信息**：如果聚类过程中考虑了标注信息，原型还可以包含关于图像中特定结构（如器官、病变）的语义信息。
    - **空间信息**：原型中可能包含关于超像素在图像中位置的信息，有助于理解图像的局部结构。

### 应用

在医学图像分割中，cluster prototypes 用于增强模型对前景对象（如器官、病变区域）的理解，通过将这些原型集成到分割模型中，可以提高模型对复杂医学图像的分割精度和鲁棒性。

## 损失函数

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%201.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%202.png)

## 自监督框架

在这篇论文中，自监督框架是DSPNet的核心组成部分之一，它在医学图像分割中扮演了至关重要的角色。自监督学习是一种强大的技术，它允许模型从未标注的数据中学习有用的特征表示，这在标注数据稀缺的医学图像领域尤其有价值。

**自监督框架的工作原理：**

1. **特征提取**：自监督框架首先使用卷积神经网络（CNN）作为特征提取器，从输入的医学图像中提取深层特征。这些特征捕获了图像中的重要视觉信息，为后续的分割任务奠定了基础。
2. **原型生成**：在特征提取之后，自监督框架通过细节自精炼块（DSR）生成高保真的前景和背景原型。这些原型代表了图像中不同区域的语义信息，是分割模型进行像素级分类的关键。
3. **原型自监督**：自监督框架通过设计原型自监督机制，使得模型能够从未标注的数据中学习。这通常涉及到一些自监督任务，如预测图像的不同部分之间的关系，或者利用图像的内在结构特性。
4. **细节自精炼**：自监督框架中的FSPA和BCMA模块负责对前景和背景原型进行细节自精炼。FSPA通过聚类和通道注意力机制增强前景的语义细节，而BCMA则利用多头注意力和稀疏通道感知正则化来提升背景的细节表示。

**自监督框架在医学图像分割中的角色：**

1. **数据标注成本降低**：在医学图像领域，获取大量精确标注的数据是非常昂贵和耗时的。自监督框架允许模型在少量标注或甚至无标注数据的情况下进行训练，显著降低了数据标注的成本。
2. **提高模型泛化能力**：通过从未标注数据中学习，自监督框架有助于模型学习到更加泛化的特征表示，这些特征不仅适用于训练数据，也能更好地推广到新的、未见过的数据上。
3. **增强模型鲁棒性**：自监督学习通过引入额外的自监督任务，增加了模型训练的难度，这通常会导致模型学习到更加鲁棒的特征表示，从而提高模型在面对噪声和变化时的稳定性。
4. **提高分割精度**：在医学图像分割中，精确地区分不同的组织和器官是非常关键的。自监督框架通过提升原型的细节表示能力，使得模型能够更准确地进行像素级的分类，从而提高了分割的精度。

总的来说，自监督框架在医学图像分割中起到了至关重要的作用，它不仅降低了对大量标注数据的依赖，还提高了模型的泛化能力和分割精度。

## 性能评估

评估自监督学习框架在医学图像分割中的性能通常涉及以下几个步骤和指标：

1. **数据集选择**：选择合适的医学图像数据集进行评估，这些数据集通常包含多种医学成像方式（如CT、MRI等）和不同的病理类型。数据集应该具有代表性，能够涵盖模型需要处理的各种情况。
2. **分割精度**：使用标准的分割精度指标来评估模型性能，常见的指标包括：
    - **Dice系数（Dice Similarity Coefficient, DSC）**：衡量两个样本集合的相似度，通常用于评估分割结果和真实标注之间的一致性。
    - **交并比（Intersection over Union, IoU）**：衡量预测分割区域和真实分割区域之间的重叠程度。
    - **Hausdorff距离**：衡量两个点集之间的最大距离，用于评估分割的误差。
3. **交叉验证**：为了确保模型的稳健性，通常采用交叉验证的方法来评估模型性能。这涉及到将数据集分成多个部分，轮流使用其中一部分作为测试集，其余作为训练集。
4. **比较研究**：将自监督学习框架的性能与现有的监督学习方法或其他自监督方法进行比较。这有助于展示自监督框架相对于其他技术的优劣。
5. **案例研究**：通过可视化的方式展示分割结果，包括预测的分割图和真实标注的对比，以及模型在处理复杂或难以分割区域时的表现。
6. **鲁棒性测试**：评估模型在不同成像条件、不同病理类型和存在噪声的情况下的表现，以测试其鲁棒性。
7. **计算效率**：除了分割精度，还需要考虑模型的计算效率，包括训练时间和推理时间。在实际应用中，模型需要在合理的时间内完成训练和推理。
8. **泛化能力**：评估模型在未见过的数据上的表现，这可以通过在不同的数据集上进行测试来实现，以确保模型不仅在训练数据上表现良好，也能泛化到新数据上。
9. **临床验证**：在可能的情况下，与临床医生合作，通过临床案例来验证模型的实用性和准确性。
10. **统计显著性测试**：通过统计测试（如t-test或ANOVA）来确定模型性能提升是否具有统计显著性。

通过上述方法，可以全面评估自监督学习框架在医学图像分割中的性能，并确定其在实际应用中的潜力和局限性。

## 架构图

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%203.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%204.png)

## Method

(i) The CNN-based feature extractor f(·); (ii) the detail selfrefining block DSR(·); and (iii) the segmentation block based on the cosine similarity. Suppose the support and query images are denoted by Is and Iq, respectively.

 The segmentation begins with feature extraction Fs = fθ(Is) and Fq = fθ(Iq). Furthermore, high-fidelity foreground prototype and background prototypes are produced by the detail self-refining block, denoted by Pk = {Pf, Pb} = DSR(Fq, Fs, Ms) where Ms is the support masking label. Finally, we obtain the query prediction of segmentation SEG(Fq, Pk), computing cosine similarity between Fq and obtained prototypes Pk in a convolution fashion.

### RAN(Resemblance Attention Network)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%205.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%206.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%207.png)

其中ϕ（·）是softmax计算;

运算符C和D分别表示余弦相似性测量和逐通道原型融合的计算

### FSPA (Foreground Semantic Prototype Attention)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%208.png)

### Background Channel-structural Multi-head Attention

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%209.png)

### **Self-supervision with Superpixels: Training Few-shot Medical Image Segmentation without Annotation**

Cheng Ouyang1(), Carlo Biffi1∗, Chen Chen1∗, Turkay Kart1∗,Huaqi Qiu1, and Daniel Rueckert1 BioMedIA Group, Department of Computing, Imperial College London, UK c.ouyang@imperial.ac.uk

**Abstract.** Few-shot semantic segmentation (FSS) has great potential for medical imaging applications. Most of the existing FSS techniques re-quire abundant annotated semantic classes for training. However, these methods may not be applicable for medical images due to the lack of annotations. To address this problem we make several contributions: (1) A novel self-supervised FSS framework for medical images in order to eliminate the requirement for annotations during training. Additionally, superpixel-based pseudo-labels are generated to provide supervision; (2) An adaptive local prototype pooling module plugged into prototypical networks, to solve the common challenging foreground-background im-balance problem in medical image segmentation; (3) We demonstrate the general applicability of the proposed approach for medical images using three different tasks: abdominal organ segmentation for CT and MRI, as well as cardiac segmentation for MRI. Our results show that, for medi-cal image segmentation, the proposed method outperforms conventional FSS methods which require manual annotations for training.

**1 Introduction**

Automated medical image segmentation is a key step for a vast number of clinical procedures and medical imaging studies, including disease diagnosis and follow-up [1,2,3], treatment planning [4,5] and population studies [6,7]. Fully supervised deep learning based segmentation models can achieve good results when trained on abundant labeled data. However, the training of these networks in medical imaging is often impractical due to the following two reasons: there is often a lack of sufficiently large amount of expert-annotated data for training due the considerable clinical expertise, cost and time associated with annotation; This problem is further exacerbated by differences in image acquisition procedures across medical devices and hospitals, often resulting in datasets containing few manually labeled images; Moreover, the number of possible segmentation targets (different anatomical structures, different types of lesions, etc.) are countless. It is impractical to cover every single unseen class by training a new, specific model.

As a potential solution to these two challenges, few-shot learning has been proposed [8,9,10,11,12,13]. During *inference,* a few-shot learning model distills a discriminative representation of an unseen class from only a few labeled ex-amples (usually denoted as *support)* to make predictions for unlabeled examples (usually denoted as *query)* without the need for re-training the model. If apply-ing few-shot learning to medical images, segmenting a rare or novel lesion can be potentially efficiently achieved using only a few labeled examples.

However, *training* an existing few-shot semantic segmentation (FSS) model for medical imaging has not had much success in the past, as most of FSS methods rely on a large training dataset with many annotated training classes to avoid overfitting [14,15,16,17,18,19,20,21,17,22,23,24,25]. In order to bypass this unmet need of annotation, we propose to train an FSS model on unla-beled images instead via self-supervised learning, an unsupervised technique that learns generalizable image representations by solving a carefully designed task[26,27,28,29,30,31,32,33]. Another challenge for a lot of state-of-the-art FSS net-work architectures is the loss of local information within a spatially variant class in their learned representations. This problem is in particular magnified in med-ical images since extreme foreground-background imbalance commonly exists in medical images. As shown in Fig. 1 (b)., the background class is large and spatially inhomogeneous whereas the foreground class (in purple) is small and homogeneous. Under this scenario, an ambiguity in prediction on foreground-background boundary might happen if the distinct appearance information of different local regions (or saying, parts) in the background is unreasonably av-eraged out. Unfortunately, this loss of intra-class local information exists in a lot of recent works, where each class is spatially averaged into a 1-D representa-tion prototype [16,18,19,34] or weight vectors of a linear classifier [17]. In adjust to this problem, we instead encourage the network to preserve intra-class local information, by extracting an ensemble of local representations for each class.

In order to break the deadlock of training data scarcity and to boost segmen-tation accuracy, we propose SSL-ALPNet, a self-supervised few-shot semantic segmentation framework for medical imaging. The proposed framework exploits *superpixel-based self-supervised learning* (SSL), using superpixels for eliminat-ing the need for manual annotations, and an *adaptive local prototype pooling* enpowered prototypical network (ALPNet), improving segmentation accuracy by preserving local information in learned representations. As shown in Fig. 1 (a), to ensure image representations learned through self-supervision are well-generalizable to real semantic classes, we generate pseudo-semantic labels using superpixels, which are compact building blocks for semantic objects [35,36,37].In addition, to improve the discriminative ability of learned image representa-tions, we formulate the self-supervision task as one-superpixel-against-the-rest

### 超像素

**Felzenszwalb & Huttenlocher (FH) 方法：**

基于图的分割方法，通过最小化区域间的差异来生成超像素。
适用于快速生成超像素，但可能不够精细。
**Simple Linear Iterative Clustering (SLIC)：**

基于K-means聚类的方法，通过迭代优化像素的颜色和位置来生成超像素。
生成的超像素边界更清晰，适用于精细分割任务。
**Quickshift：**

基于图像像素的密度估计，通过寻找密度峰值来生成超像素。
适用于生成具有良好边界的超像素。

# 运行

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%2010.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%2011.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%2012.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%2013.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%2014.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%2015.png)

![image.png](Paper/attachments/Few-Shot%20Medical%20Image%20Segmentation%20with%20High-Fide%201254204245de4f96b8cbd95c63cded25/image%2016.png)