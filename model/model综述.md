- [当Transformer遇见U-Net！ - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/389880194)
- **[GitHub：深度学习最全资料集锦](https://zhuanlan.zhihu.com/p/64361703)**
- **[GitHub：图像分类最全资料集锦](https://zhuanlan.zhihu.com/p/57006140)**
- **[GitHub：目标检测最全论文集锦](https://zhuanlan.zhihu.com/p/36818086)**
- **[GitHub：图像分割最全资料集锦](https://zhuanlan.zhihu.com/p/58599382)**
- **[GitHub：目标跟踪最全资料集锦](https://link.zhihu.com/?target=https%3A//cloud.tencent.com/developer/article/1420878)**
- **[GitHub：人群密度估计最全资料集锦](https://link.zhihu.com/?target=http%3A//mp.weixin.qq.com/s%3F__biz%3DMzUxNjcxMjQxNg%3D%3D%26mid%3D2247488595%26idx%3D1%26sn%3Dd55e01393ea37ad4c1cc7a36cda31cb3%26chksm%3Df9a266dcced5efca087cc4e790cbb47bb395f03d7a2c385f3c5852a2a3528858c3bda4c18fb0%26scene%3D21%23wechat_redirect)**
- **[GitHub：车道线检测最全资料集锦](https://zhuanlan.zhihu.com/p/53431081)**
- **[GitHub：TensorFlow最全资料集锦](https://zhuanlan.zhihu.com/p/55438552)**
- **[GitHub：Anchor-free目标检测最全资料集锦](https://zhuanlan.zhihu.com/p/217537617)**
- **[GitHub：数据增广最全资料集锦](https://zhuanlan.zhihu.com/p/265021958)**
- **[GitHub：语义分割最全资料集锦](https://zhuanlan.zhihu.com/p/265873321)**
- **[GitHub：人群计数最全资料集锦](https://zhuanlan.zhihu.com/p/266434424)**

[Object Detection - handong1587](https://handong1587.github.io/deep_learning/2015/10/09/object-detection.html)


[三个最新屠榜的Backbone！](https://zhuanlan.zhihu.com/p/387567246)

[Transformer一脚踹进医学图像分割！看5篇MICCAI 2021有感](https://zhuanlan.zhihu.com/p/386846596)

[华人占据大半壁江山！CVPR 2021 目标检测论文大盘点（65篇论文）](https://zhuanlan.zhihu.com/p/385639375)

[最新！CVPR 2021 视觉Transformer论文大盘点（43篇）](https://zhuanlan.zhihu.com/p/376740599)

[大盘点 | 十大即插即用的涨点神器！](https://zhuanlan.zhihu.com/p/180102609)

[CVPR 引用量最高的10篇论文！何恺明ResNet登顶，YOLO占据两席！](https://zhuanlan.zhihu.com/p/165097702)

[ICCV 引用量最高的10篇论文！何恺明两篇一作论文：Mask R-CNN和PReLU](https://zhuanlan.zhihu.com/p/173821022)

[ECCV 引用量最高的10篇论文！SSD登顶！何恺明ResNet改进版位居第二](https://zhuanlan.zhihu.com/p/183180323)

[目标检测四大开源神器Detectron2/mmDetection/darknet/SimpleDet](https://zhuanlan.zhihu.com/p/162850566)

[有人手写实现李航《统计学习方法》书中全部算法](https://zhuanlan.zhihu.com/p/142220983)

[周志华教授：如何做研究与写论文](https://zhuanlan.zhihu.com/p/98747105)



# 网络架构
### 1. **CoAtNet**

CoAtNet结合了卷积神经网络（CNN）和Transformer的优势，通过混合这两种架构的特点，实现了在图像识别任务上的显著性能提升。CoAtNet通过调整卷积层和Transformer层的结合方式，优化了模型的表示能力和效率。

### 2. **Perceiver IO**

Perceiver IO是一种通用架构，旨在处理多种不同形式的输入数据，包括图像、音频和文本。它通过一个灵活的注意力机制，允许模型从大量输入数据中提取有用信息，而不需要对输入数据的结构进行特定的假设。这种设计使得Perceiver IO在多任务学习和多模态学习领域表现出色。

### 3. **MAE (Masked Autoencoder for Vision Transformers)**

MAE是一种自监督学习方法，主要用于视觉Transformer。它通过遮蔽输入图像的一部分，然后训练模型重建被遮蔽的部分，从而学习到图像的有效表示。这种方法减少了对大量标注数据的依赖，同时提高了模型对图像特征的理解能力。

### 4. **MViT (Multiscale Vision Transformers)**

MViT通过在不同尺度上应用Transformer，有效地捕获了图像的多尺度特征。这种架构特别适合于视频处理和三维图像处理任务，因为它能够处理时间或空间上的多尺度信息。

### 5. **EfficientNetV2**

EfficientNetV2是EfficientNet系列的最新进展，它对原有架构进行了改进，特别是在模型效率和缩放策略方面。通过使用更高效的训练过程和优化的网络结构，EfficientNetV2在多个图像识别任务上取得了更好的性能，同时保持了较低的计算成本。

### 6. **Swin Transformer V2**

作为Swin Transformer的进一步改进，Swin Transformer V2在原有模型的基础上引入了新的设计和优化，以提高模型的性能和效率。这些改进包括更有效的层次化结构、改进的注意力机制和更灵活的模型缩放策略。

### 基于U-Net的改进网络
![[attachments/bdf6f6e7e71587a5bb22950427e3a7cb_MD5.png]]

1. **Attention U-Net**：在U-Net的基础上增加了注意力机制，特别是在解码器部分。通过这种方式，模型能够更加关注于图像的关键部分，从而提高图像分割的准确性。

2. **3D U-Net**：针对三维图像数据，3D U-Net通过使用三维卷积来处理体积数据，使得模型更适合处理医学成像等三维图像分割任务。

3. **V-Net**：专为体积图像分割设计的一个变体，V-Net通过引入体积卷积和一个特殊的损失函数来改进U-Net，适用于三维医学图像分割。

### 基于ViT的改进网络

1. **DeiT (Data-efficient Image Transformers)**：DeiT通过引入一种新的训练策略，使得ViT模型能够在数据量相对较少的情况下也能达到良好的性能。这主要通过使用知识蒸馏（knowledge distillation）从一个预训练的CNN模型中学习。

2. **Swin Transformer**：改进ViT。Swin Transformer通过引入层次化的Transformer结构，使得模型能够更高效地处理图像。它将图像分割成多尺度的窗口，并在这些窗口上应用Transformer，从而允许模型捕获不同尺度上的特征。

3. **CvT (Convolutional vision Transformers)**：CvT结合了卷积神经网络和Transformer的优点，通过在Transformer结构中引入卷积操作，改进了ViT的性能，尤其是在处理局部特征时。

### 结合U-Net和ViT的网络

1. **TransUNet**：结合了Transformer和U-Net的优点，TransUNet首先使用ViT作为编码器来捕获全局依赖关系，然后使用U-Net结构进行精确的局部特征重建和图像分割。这种结合利用了Transformer在全局信息处理上的能力和U-Net在图像分割上的高效性。

2. **UNETR (U-Net Transformer)**：专为医学图像分割设计，UNETR使用Transformer作为其主要的特征提取器，以处理三维医学图像数据，展示了在处理高维数据时Transformer架构的潜力。

![[attachments/ddb4adb8c7184b57b60f0cdc3a98201c_MD5.png]]

# Swin Transformer
![[attachments/da9ef09d850467f1784b72e1c6c59ded_MD5.png]]
