[【pytorch】Vision Transformer实现图像分类+可视化+训练数据保存_vision transformer训练自己的数据-CSDN博客](https://blog.csdn.net/weixin_51331359/article/details/124514770)  

[11.2 使用pytorch搭建Vision Transformer(vit)模型](https://www.bilibili.com/video/BV1AL411W7dT)

[Transformers VisionTransformer | Towards Data Science](https://towardsdatascience.com/implementing-visualttransformer-in-pytorch-184f9f16f632)
[在PyTorch中实现Vision Transformer - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/348849092)

[详解Transformer中Self-Attention以及Multi-Head Attention_transformer multi head-CSDN博客](https://blog.csdn.net/qq_37541097/article/details/117691873)


Vision Transformer 是计算机视觉领域中具有最大影响力的工作之一。
Vision Transformer 挑战了自2012年Alexnet以来卷积神经网络在计算机视觉领域的统治地位。
Vision Transformer 被评为今年ICCV 21的最佳论文。
对于检测和分割任务，Vision Transformer 将特征图尺寸拆分成两个1D序列。
Vision Transformer 的序列长度需要通过有监督的训练方式进行训练。
当在中等规模的数据集上训练时，优先选择较少的训练资源。
DETR是去年非常受关注的工作，其未来的工作方向可能涉及CV和NLP的整合、小窗口任务和迁移学习效果的研究。
Vision Transformer在维度上不如ResNet，尤其在小数据集上。
为了使Transformer适应图片输入，使用局部性和混合网络的方法，将图片分割成较小的patches进行处理。
Vision Transformer在多个数据集上进行了测试，一共有三种模型，序列程度增加。
期待下一个改进版的Vision Transformer出现，成为一个简洁高效且通用的视觉骨干网络，无需标注信息。

# VisionTransformer论文
## 结构
![[attachments/014684b083859720ae9ccc13101e4b87_MD5.png]]
![[attachments/000f96c28d79ca4bbf5957d4ca8eb7c2_MD5.png]]
### 预处理
- image2embedding，将224x224图像分割成16x16个16x16的图像块patch，每个patch拉成一维向量做成embedding
- 生成一个cls token（batch_size , 1 , model_dim），和embedding拼接维度1得到token_embedding。序列长度196+1，块的大小768（16x16x3）不变但序列长度可变，因此可以处理任意分辨率的图片（但会导致性能下降） 
- 生成postion_embedding_table，取postion_embedding和token_embedding相加
### encoders
然后送入encoder获得output，取output的cls token做一个mlp，得到分类结果
### self attention
![[attachments/5d13210100c61c70c3390ed73b6b4fd3_MD5.png]]
Multi-head attention是通过mlp得到qkv，然后将dim均分给每个head
每个head做attention计算最后concat再乘W

![[attachments/5d2da6474a2c5bee949c94d868ac1e9b_MD5.png]]

![[attachments/ed83fbfb70432386f396511bd8c44b43_MD5.png]]

## 模型
### ViT-B/16
![[attachments/675cecff6a2581af88a786122642571a_MD5.png]]
### ViT-B/16 hybrid
![[attachments/3a6ae5f07318293cf642fb7f6d14399d_MD5.png]]
## 模型性能分析

![[attachments/1f84255766163d7b9e9e3e484c002319_MD5.png]]

![[attachments/f31fe5e68da4eec34df0d191270782a9_MD5.png]]
使用CNN进行图像预处理，将图像缩小至16\*16的Hybrid，在中小规模数据集表现非常好，可以尝试实现，单纯ViT必须在大规模数据集表现才会比较好，不具备实验条件

![[attachments/fafe22bd1c5fa908f567e7dcf008be69_MD5.png]]


# 训练
tensorboard --logdir=runs

- 为什么训练n轮后再重新训练acc会从较低开始变回上次最终的训练结果？
	- 因为head在载入时被剔除了，每次都是重新train
- 尝试两种预训练模型微调方法，比较效果
	- 完全冻结权重
	- 给予很小的权重
- load的模型本身就是没有pre_logits.fc层的，所以不需要

## animals151
- 50轮，训练精度0.6，验证0.3，产生严重过拟合
- 150轮训练一晚上，精度0.3，0.15，也是过拟合，而且精度都很差

## pc_parts
使用了pre-logits
![[attachments/485fea6b0d8e7a7c1dbbfce100bda612_MD5.png]]
![[attachments/ad5dad14e9fe385e2018df48abf57e31_MD5.png]]
学习率太大，后面acc开始震荡
删掉pre-logits.fc 降低学习率重新跑，acc停在0.2
![[attachments/7a72ac523f0ebdd381709b2c775eeb17_MD5.png]]
再降学习率，
![[attachments/374ac3177fdf46049a5953a08b08cf42_MD5.png]]
紫色，几乎没有提升
![[attachments/d47a6b0dfa9ec254b3e38c6ad93ebed1_MD5.png]]

换ReduceLROnPlateau，差分学习率
完全没有用，可能得解冻底层网络才行

## ImageNet10
图片大小不规律，得处理
```
cfg = {  
    'num_classes': 10,  
    'epochs': 50,  
    'batch_size': 128,  
    'lr': 0.0003,  
    # 'lrf': 0.001,  
    'factor': 0.5,  
    'momentum': 0.9,  
    'weight_decay': 5E-5,  
    'step_size': 10,  # 每10epochs降低lr  
    'best_acc': 0.2,  # 初始最佳acc  
    'first_train': False,  # 第一次训练不加载head  
    'data_path': r"E:\CodeAchieve\Data\FastAI_ImageNet_v2",  # 数据集所在根目录  
    # 预训练权重路径，如果不想载入就设置为空字符 vit_b_16.pth best_model_head.pth    'weights': './weights/best_model_head_logits.pth',  
    'weights_save_path': './weights/best_model_head_logits.pth',  
    'freeze_layers': True,  # True冻结权重，False差分学习率  
    'has_logits': True,  
    'device': 'cuda:0'  
}
```
## ImageNet5
删掉了一些不好处理的类，acc到0.4后不再提升
使用差分学习率，最高0.58
![[attachments/878bbcf17784eca42fae6c025c2fe14b_MD5.png]]

更小的权重衰退和更大学习率，很诡异，每次重新load模型精度都会下降然后再缓慢提升，按道理这是不正常的，使用更小学习率可以减轻这个情况，最终acc 0.612
```
cfg = {  
    'num_classes': 5,  
    'epochs': 100,  
    'batch_size': 32,  
    'lr': 0.00003,  
    # 'lrf': 0.001,  
    'factor': 0.5,  
    'momentum': 0.9,  
    'weight_decay': 1E-6,  
    'step_size': 10,  # 每10epochs降低lr  
    'best_acc': 0.6,  # 初始最佳acc  
    'first_train': False,  # 第一次训练不加载head  
    'data_path': r"E:\CodeAchieve\Data\FastAI_ImageNet_v2",  # 数据集所在根目录  
    # 预训练权重路径，如果不想载入就设置为空字符 vit_b_16.pth best_model_head.pth    'weights': './weights/best_model_all.pth',  
    'weights_save_path': './weights/best_model_all.pth',  
    'freeze_layers': False,  # True冻结权重，False差分学习率  
    'has_logits': True,  
    'device': 'cuda:0'  
}
```

再降低权重衰退惩罚，没有作用，改小学习率，去除图片增强

```
cfg = {  
    'num_classes': 5,  
    'epochs': 100,  
    'batch_size': 32,  
    'lr': 0.00001,  
    # 'lrf': 0.001,  
    'factor': 0.5,  
    'momentum': 0.9,  
    'weight_decay': 1E-6,  
    'step_size': 5,  # 每10epochs降低lr  
    'best_acc': 0.62,  # 初始最佳acc  
    'first_train': False,  # 第一次训练不加载head  
    'data_path': r"E:\CodeAchieve\Data\FastAI_ImageNet_v2",  # 数据集所在根目录  
    # 预训练权重路径，如果不想载入就设置为空字符 vit_b_16.pth best_model_head.pth    'weights': './weights/best_model_all.pth',  
    'weights_save_path': './weights/best_model_all.pth',  
    'freeze_layers': False,  # True冻结权重，False差分学习率  
    'has_logits': True,  
    'device': 'cuda:0'  
}
```



读数据集炸内存，提高swap
![[attachments/2dac3f84369bfbba70495d19030d93f9_MD5.png]]

## 预训练模型下载

除了`torchvision`库之外，还有一些其他流行的库可以用来下载和使用预训练模型。以下是一些常用的库：

**Hugging Face Transformers**:
   Hugging Face的Transformers库是一个流行的自然语言处理模型库，提供了各种预训练的Transformer模型（如BERT、GPT等）。
   你可以使用`transformers`库来下载和使用各种预训练模型，例如：BERT、GPT、RoBERTa等。

**PyTorch Hub**:
   PyTorch Hub是PyTorch官方提供的模型库，包含了许多预训练的PyTorch模型。
   你可以使用`torch.hub`模块来下载和使用PyTorch Hub中的模型，例如：ResNet、DenseNet等。

**Detectron2**:
   Detectron2是Facebook AI Research开发的目标检测和图像分割库，提供了许多预训练的目标检测模型。
   你可以使用Detectron2来下载和使用各种目标检测模型，如Faster R-CNN、Mask R-CNN等。

这些库提供了方便的接口和方法来下载各种预训练模型，并且通常还包含了用于微调和推理的工具和示例代码。根据你的需求和使用场景，选择合适的库来下载和使用预训练模型。
## backbone
在深度学习和计算机视觉中，"backbone"一词通常指的是用于特征提取的网络结构。它是大多数图像处理任务，如图像分类、目标检测、语义分割等，的基础组件。Backbone的目的是从输入图像中提取有用的特征，这些特征随后可以用于执行特定的任务，比如识别图像中的对象或理解场景的布局。

Backbone通常是一种预先训练的深度神经网络，如ResNet、VGG、Inception、MobileNet等。这些网络在大型数据集（如ImageNet）上进行预训练，学会识别各种图像特征。通过迁移学习，研究人员和开发人员可以利用这些预训练的网络作为自己任务的起点，这样可以加速训练过程，提高模型的性能，尤其是当可用的训练数据相对较少时。

在特定的深度学习任务中，backbone之后通常会接一些定制的层或结构来完成任务特定的目标，如分类头、检测头或分割头。这些结构利用backbone提取的特征来做出最终的预测或决策。
## 冻结权重方案

1. 冻结除了head外的权重
2. 给除了head外的权重极小的学习率

### 参数

```python
parser.add_argument('--num_classes', type=int, default=151)  
parser.add_argument('--epochs', type=int, default=100)  
parser.add_argument('--batch-size', type=int, default=48)  # 48最佳  
parser.add_argument('--lr', type=float, default=0.003)  
parser.add_argument('--lrf', type=float, default=0.001)  
parser.add_argument("--best_acc", type=float, default=0.05)
```

```
==========================================================================================
Layer (type:depth-idx)                   Output Shape              Param #
==========================================================================================
VisionTransformer                        [32, 14]                  152,064
├─PatchEmbed: 1-1                        [32, 196, 768]            --
│    └─Conv2d: 2-1                       [32, 768, 14, 14]         590,592
│    └─Identity: 2-2                     [32, 196, 768]            --
├─Dropout: 1-2                           [32, 197, 768]            --
├─Sequential: 1-3                        [32, 197, 768]            --
│    └─Block: 2-3                        [32, 197, 768]            --
│    │    └─LayerNorm: 3-1               [32, 197, 768]            1,536
│    │    └─Attention: 3-2               [32, 197, 768]            2,362,368
│    │    └─Identity: 3-3                [32, 197, 768]            --
│    │    └─LayerNorm: 3-4               [32, 197, 768]            1,536
│    │    └─Mlp: 3-5                     [32, 197, 768]            4,722,432
│    │    └─Identity: 3-6                [32, 197, 768]            --
│    └─Block: 2-4                        [32, 197, 768]            --
│    │    └─LayerNorm: 3-7               [32, 197, 768]            1,536
│    │    └─Attention: 3-8               [32, 197, 768]            2,362,368
│    │    └─Identity: 3-9                [32, 197, 768]            --
│    │    └─LayerNorm: 3-10              [32, 197, 768]            1,536
│    │    └─Mlp: 3-11                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-12               [32, 197, 768]            --
│    └─Block: 2-5                        [32, 197, 768]            --
│    │    └─LayerNorm: 3-13              [32, 197, 768]            1,536
│    │    └─Attention: 3-14              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-15               [32, 197, 768]            --
│    │    └─LayerNorm: 3-16              [32, 197, 768]            1,536
│    │    └─Mlp: 3-17                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-18               [32, 197, 768]            --
│    └─Block: 2-6                        [32, 197, 768]            --
│    │    └─LayerNorm: 3-19              [32, 197, 768]            1,536
│    │    └─Attention: 3-20              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-21               [32, 197, 768]            --
│    │    └─LayerNorm: 3-22              [32, 197, 768]            1,536
│    │    └─Mlp: 3-23                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-24               [32, 197, 768]            --
│    └─Block: 2-7                        [32, 197, 768]            --
│    │    └─LayerNorm: 3-25              [32, 197, 768]            1,536
│    │    └─Attention: 3-26              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-27               [32, 197, 768]            --
│    │    └─LayerNorm: 3-28              [32, 197, 768]            1,536
│    │    └─Mlp: 3-29                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-30               [32, 197, 768]            --
│    └─Block: 2-8                        [32, 197, 768]            --
│    │    └─LayerNorm: 3-31              [32, 197, 768]            1,536
│    │    └─Attention: 3-32              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-33               [32, 197, 768]            --
│    │    └─LayerNorm: 3-34              [32, 197, 768]            1,536
│    │    └─Mlp: 3-35                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-36               [32, 197, 768]            --
│    └─Block: 2-9                        [32, 197, 768]            --
│    │    └─LayerNorm: 3-37              [32, 197, 768]            1,536
│    │    └─Attention: 3-38              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-39               [32, 197, 768]            --
│    │    └─LayerNorm: 3-40              [32, 197, 768]            1,536
│    │    └─Mlp: 3-41                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-42               [32, 197, 768]            --
│    └─Block: 2-10                       [32, 197, 768]            --
│    │    └─LayerNorm: 3-43              [32, 197, 768]            1,536
│    │    └─Attention: 3-44              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-45               [32, 197, 768]            --
│    │    └─LayerNorm: 3-46              [32, 197, 768]            1,536
│    │    └─Mlp: 3-47                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-48               [32, 197, 768]            --
│    └─Block: 2-11                       [32, 197, 768]            --
│    │    └─LayerNorm: 3-49              [32, 197, 768]            1,536
│    │    └─Attention: 3-50              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-51               [32, 197, 768]            --
│    │    └─LayerNorm: 3-52              [32, 197, 768]            1,536
│    │    └─Mlp: 3-53                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-54               [32, 197, 768]            --
│    └─Block: 2-12                       [32, 197, 768]            --
│    │    └─LayerNorm: 3-55              [32, 197, 768]            1,536
│    │    └─Attention: 3-56              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-57               [32, 197, 768]            --
│    │    └─LayerNorm: 3-58              [32, 197, 768]            1,536
│    │    └─Mlp: 3-59                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-60               [32, 197, 768]            --
│    └─Block: 2-13                       [32, 197, 768]            --
│    │    └─LayerNorm: 3-61              [32, 197, 768]            1,536
│    │    └─Attention: 3-62              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-63               [32, 197, 768]            --
│    │    └─LayerNorm: 3-64              [32, 197, 768]            1,536
│    │    └─Mlp: 3-65                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-66               [32, 197, 768]            --
│    └─Block: 2-14                       [32, 197, 768]            --
│    │    └─LayerNorm: 3-67              [32, 197, 768]            1,536
│    │    └─Attention: 3-68              [32, 197, 768]            2,362,368
│    │    └─Identity: 3-69               [32, 197, 768]            --
│    │    └─LayerNorm: 3-70              [32, 197, 768]            1,536
│    │    └─Mlp: 3-71                    [32, 197, 768]            4,722,432
│    │    └─Identity: 3-72               [32, 197, 768]            --
├─LayerNorm: 1-4                         [32, 197, 768]            1,536
├─Sequential: 1-5                        [32, 768]                 --
│    └─Linear: 2-15                      [32, 768]                 590,592
│    └─Tanh: 2-16                        [32, 768]                 --
├─Linear: 1-6                            [32, 14]                  10,766

========================================================================================
Total params: 86,400,014
Trainable params: 86,400,014
Non-trainable params: 0
Total mult-adds (G): 6.45
========================================================================================
Input size (MB): 19.27
Forward/backward pass size (MB): 5190.06
Params size (MB): 344.99
Estimated Total Size (MB): 5554.32
========================================================================================
```

# torch的训练策略
### 1. 反向传播 (`loss.backward()`)

当你调用`loss.backward()`时，PyTorch会自动计算所有参与计算并且`requires_grad=True`的张量（通常是模型参数）的梯度。这些梯度被存储在各自张量的`.grad`属性中。这意味着此时每个模型参数张量都已经获得了它的梯度，但这个梯度并没有被“显式”传递给优化器。

### 2. 梯度清零 (`optimizer.zero_grad()`)

在每次训练迭代开始时调用`optimizer.zero_grad()`是为了清除上一轮迭代中计算的梯度，因为如果不清零，梯度会累加，这通常不是我们想要的（除非特殊情况）。这个步骤确保了每次迭代计算的梯度是基于最新的数据而不是累积的。

### 3. 更新模型参数 (`optimizer.step()`)

调用`optimizer.step()`时，优化器会遍历所有的参数（它在初始化时已经知道了哪些参数需要优化，因为你在创建优化器实例时将模型参数传递给了它），并使用存储在`.grad`属性中的梯度来更新这些参数。这个更新的具体方式取决于你选择的优化算法（如SGD, Adam等）。这个步骤是“隐式”使用梯度的，因为你不需要显式告诉优化器去哪里找梯度，优化器已经知道每个参数的`.grad`属性在哪里，并使用它来更新参数。