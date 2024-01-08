
# 计算机视觉基础

## 1. 图像增广


```python
# 水平翻转，垂直翻转，颜色色调亮度处理，随机裁剪，然后转化为tensor
train_augs = torchvision.transforms.Compose([
     torchvision.transforms.RandomHorizontalFlip(),
     torchvision.transforms.RandomVerticalFlip(),
     torchvision.transforms.ColorJitter(brightness=0.5, contrast=0.5, saturation=0.5, hue=0.5),
     torchvision.transforms.RandomResizedCrop((200, 200), scale=(0.1, 1), ratio=(0.5, 2)),
     torchvision.transforms.ToTensor()
     ])

# 测试集不做调整
test_augs = torchvision.transforms.Compose([
     torchvision.transforms.ToTensor()])
```


- 图像增广基于现有的训练数据生成随机图像，来提高模型的泛化能力。
- 为了在预测过程中得到确切的结果，我们通常对训练样本只进行图像增广，而在预测过程中不使用带随机操作的图像增广。
- 深度学习框架提供了许多不同的图像增广方法，这些方法可以被同时应用。

## 2. 微调

![](https://zh-v2.d2l.ai/_images/finetune.svg)

- 迁移学习将从源数据集中学到的知识_迁移_到目标数据集，微调是迁移学习的常见技巧。
- 除输出层外，目标模型从源模型中复制所有模型设计及其参数，并根据目标数据集对这些参数进行微调。但是，目标模型的输出层需要从头开始训练。
- 通常，微调参数使用较小的学习率，而从头开始训练输出层可以使用更大的学习率。

## 3. 目标检测

```python
def bbox_to_rect(bbox, color):
    # 将边界框(左上x,左上y,右下x,右下y)格式转换成matplotlib格式：
    # ((左上x,左上y),宽,高)
    return d2l.plt.Rectangle(
        xy=(bbox[0], bbox[1]), width=bbox[2]-bbox[0], height=bbox[3]-bbox[1],
        fill=False, edgecolor=color, linewidth=2)
```

* 目标检测不仅可以识别图像中所有感兴趣的物体，还能识别它们的位置，该位置通常由矩形边界框表示。
* 我们可以在两种常用的边界框表示（中间，宽度，高度）和（左上，右下）坐标之间进行转换。

![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/16982196630008awbtx.png)

## 4. 锚框

### 4.1. 生成多个锚框

我们以图像的每个像素为中心生成不同形状的锚框：*缩放比*为$s\in (0, 1]$，*宽高比*为$r > 0$。
要生成多个不同形状的锚框，让我们设置许多缩放比（scale）取值$s_1,\ldots, s_n$和许多宽高比（aspect ratio）取值$r_1,\ldots, r_m$。
当使用这些比例和长宽比的所有组合以每个像素为中心时，输入图像将总共有$whnm$个锚框。
尽管这些锚框可能会覆盖所有真实边界框，但计算复杂性很容易过高。

### 4.2. 分配真实框给锚框
![](https://zh-v2.d2l.ai/_images/anchor-label.svg)

### 4.3. 偏移量

给定框$A$和$B$，中心坐标分别为$(x_a, y_a)$和$(x_b, y_b)$，宽度分别为$w_a$和$w_b$，高度分别为$h_a$和$h_b$，可以将$A$的偏移量标记为：
$$\left( \frac{ \frac{x_b - x_a}{w_a} - \mu_x }{\sigma_x},

\frac{ \frac{y_b - y_a}{h_a} - \mu_y }{\sigma_y},

\frac{ \log \frac{w_b}{w_a} - \mu_w }{\sigma_w},

\frac{ \log \frac{h_b}{h_a} - \mu_h }{\sigma_h}\right),$$
其中常量的默认值为 $\mu_x = \mu_y = \mu_w = \mu_h = 0, \sigma_x=\sigma_y=0.1$ ， $\sigma_w=\sigma_h=0.2$。

### 4.4. 非极大值抑制NMS

- 我们以图像的每个像素为中心生成不同形状的锚框。
- 交并比（IoU）也被称为杰卡德系数，用于衡量两个边界框的相似性。它是相交面积与相并面积的比率。
- 在训练集中，我们需要给每个锚框两种类型的标签。一个是与锚框中目标检测的类别，另一个是锚框真实相对于边界框的偏移量。
- 预测期间可以使用非极大值抑制（NMS）来移除类似的预测边界框，从而简化输出。

## 5. 多尺度目标检测

* 在多个尺度下，我们可以生成不同尺寸的锚框来检测不同尺寸的目标。
* 通过定义特征图的形状，我们可以决定任何图像上均匀采样的锚框的中心。
* 我们使用输入图像在某个感受野区域内的信息，来预测输入图像上与该区域位置相近的锚框类别和偏移量。
* 我们可以通过深入学习，在多个层次上的图像分层表示进行多尺度目标检测。

# 目标检测算法

## 1. R-CNN网络

![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/16984780630006my3fm.png)

1. 对输入图像使用*选择性搜索*来选取多个高质量的提议区域。这些提议区域通常是在多个尺度下选取的，并具有不同的形状和大小。每个提议区域都将被标注类别和真实边界框；
2. 选择一个预训练的卷积神经网络，并将其在输出层之前截断。将每个提议区域变形为网络需要的输入尺寸，并通过前向传播输出抽取的提议区域特征；
3. 将每个提议区域的特征连同其标注的类别作为一个样本。训练多个SVM对目标分类，其中每个SVM用来判断样本是否属于某一个类别；
4. 将每个提议区域的特征连同其标注的边界框作为一个样本，训练线性回归模型来预测真实边界框。

## 2. R-CNN变种

![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/1698479122000q5zbt7.png)

![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/169847922500086f7is.png)


* R-CNN对图像选取若干提议区域，使用卷积神经网络对每个提议区域执行前向传播以抽取其特征，然后再用这些特征来预测提议区域的类别和边界框。
* Fast R-CNN对R-CNN的一个主要改进：只对整个图像做卷积神经网络的前向传播。它还引入了兴趣区域汇聚层，从而为具有不同形状的兴趣区域抽取相同形状的特征。
* Faster R-CNN将Fast R-CNN中使用的选择性搜索替换为参与训练的区域提议网络，这样后者可以在减少提议区域数量的情况下仍保证目标检测的精度。
* Mask R-CNN在Faster R-CNN的基础上引入了一个全卷积网络，从而借助目标的像素级位置进一步提升目标检测的精度。

## 3. SSD模型

![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/1698479500000kpd1ti.png)
* 单发多框检测是一种多尺度目标检测模型。基于基础网络块和各个多尺度特征块，单发多框检测生成不同数量和不同大小的锚框，并通过预测这些锚框的类别和偏移量检测不同大小的目标。
* 在训练单发多框检测模型时，损失函数是根据锚框的类别和偏移量的预测及标注值计算得出的。
### 3.1. 优化损失
能通过改进损失函数来改进单发多框检测吗？例如，将预测偏移量用到的$L_1$范数损失替换为平滑$L_1$范数损失。它在零点附近使用平方函数从而更加平滑，这是通过一个超参数$\sigma$来控制平滑区域的：
$$

f(x) =

    \begin{cases}

    (\sigma x)^2/2,& \text{if }|x| < 1/\sigma^2\\

    |x|-0.5/\sigma^2,& \text{otherwise}

    \end{cases}

$$
此外，在类别预测时，实验中使用了交叉熵损失：设真实类别$j$的预测概率是$p_j$，交叉熵损失为$-\log p_j$。我们还可以使用焦点损失 :cite:`Lin.Goyal.Girshick.ea.2017`。给定超参数$\gamma > 0$和$\alpha > 0$，此损失的定义为：
$$ - \alpha (1-p_j)^{\gamma} \log p_j.$$
可以看到，增大$\gamma$可以有效地减少正类预测概率较大时（例如$p_j > 0.5$）的相对损失，因此训练可以更集中在那些错误分类的困难示例上。

### 3.2. 细节

由于篇幅限制，我们在本节中省略了单发多框检测模型的一些实现细节。能否从以下几个方面进一步改进模型：
- 当目标比图像小得多时，模型可以将输入图像调大；
- 通常会存在大量的负锚框。为了使类别分布更加平衡，我们可以将负锚框的高和宽减半；
- 在损失函数中，给类别损失和偏移损失设置不同比重的超参数；
- 使用其他方法评估目标检测模型，例如单发多框检测论文 :cite:`Liu.Anguelov.Erhan.ea.2016`中的方法。

## 4. 语义分割

- 语义分割通过将图像划分为属于不同语义类别的区域，来识别并理解图像中像素级别的内容。
- 语义分割的一个重要的数据集叫做Pascal VOC2012。
- 由于语义分割的输入图像和标签在像素上一一对应，输入图像会被随机裁剪为固定尺寸而不是缩放。

## 5. 转置（逆）卷积

![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/1699436491000zaizfg.png)

它可以增加上采样中间层特征图的空间维度

由于语义分割的像素切割位置固定，不可以做缩放，所以CNN网络需要对图片大小做转置卷积， 用于逆转下采样导致的空间尺寸减小。

![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/16994356810008uk0k4.png)
![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/1699435701000g04cmi.png)
![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/16994358330001pfruw.png)
![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/1699435851000wd4myy.png)

![gh](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/16994361850000hkf5g.png)


* 与通过卷积核减少输入元素的常规卷积相反，转置卷积通过卷积核广播输入元素，从而产生形状大于输入的输出。
* 如果我们将$\mathsf{X}$输入卷积层$f$来获得输出$\mathsf{Y}=f(\mathsf{X})$并创造一个与$f$有相同的超参数、但输出通道数是$\mathsf{X}$中通道数的转置卷积层$g$，那么$g(Y)$的形状将与$\mathsf{X}$相同。
* 我们可以使用矩阵乘法来实现卷积。转置卷积层能够交换卷积层的正向传播函数和反向传播函数。

## 6. FCN全连接卷积

全卷积网络[**用双线性插值的上采样初始化转置卷积层。对于$1\times 1$卷积层，我们使用Xavier初始化参数。**]

```python
def bilinear_kernel(in_channels, out_channels, kernel_size):
    factor = (kernel_size + 1) // 2
    if kernel_size % 2 == 1:
        center = factor - 1
    else:
        center = factor - 0.5
    og = (torch.arange(kernel_size).reshape(-1, 1),
          torch.arange(kernel_size).reshape(1, -1))
    filt = (1 - torch.abs(og[0] - center) / factor) * \
           (1 - torch.abs(og[1] - center) / factor)
    weight = torch.zeros((in_channels, out_channels,
                          kernel_size, kernel_size))
    weight[range(in_channels), range(out_channels), :, :] = filt
    return weight
```


## 风格迁移

模型权重是不变的，或者说权重参数本身就是图像，将内容图像抽取的所有参数赋值给weight，这样只要反向传播计算就可以还原图像X，训练过程对指定的样式层，内容层计算损失，更新指定层的模型参数

1. *内容损失*使合成图像与内容图像在内容特征上接近；
2. *风格损失*使合成图像与风格图像在风格特征上接近
3. *全变分损失*则有助于减少合成图像中的噪点。

深度学习的图像风格迁移是一种通过神经网络模型将一张图像的内容与另一张图像的风格进行合成的技术。它可以将一张图像的内容转化为另一张图像的艺术风格，创造出独特的图像效果。

图像风格迁移的基本思想是将图像的内容和风格分离，并通过优化算法将它们重新组合在一起。这个过程通常使用卷积神经网络（Convolutional Neural Networks，CNN）来实现。

下面是一个常见的图像风格迁移算法的步骤：

1. 定义损失函数：首先，需要定义一个损失函数来衡量合成图像与目标图像之间的差异。通常，损失函数由两部分组成：内容损失和风格损失。
   - 内容损失：通过计算合成图像与内容图像之间的特征表示的差异来衡量。可以使用预训练的卷积神经网络提取图像的特征表示，比较合成图像和内容图像在某些层的特征表示之间的差异。
   - 风格损失：通过计算合成图像与风格图像之间的特征表示的差异来衡量。类似于内容损失，可以使用卷积神经网络提取图像的特征表示，并比较合成图像和风格图像在不同层的特征表示之间的差异。
2. 初始化合成图像：将一张噪声图像作为初始合成图像。
3. 运行优化算法：通过最小化损失函数，使用梯度下降等优化算法来更新合成图像的像素值，使得合成图像的内容与目标图像的内容相似，并且合成图像的风格与目标图像的风格相似。
4. 迭代优化：重复运行优化算法多次，逐步改进合成图像的内容和风格，直到达到满意的效果。

图像风格迁移算法的关键在于损失函数的设计和优化算法的选择。不同的算法可能采用不同的网络架构和损失函数形式，以实现不同的风格迁移效果。一些常见的图像风格迁移算法包括基于卷积神经网络的风格迁移网络（如Gatys等人提出的算法）、条件生成对抗网络（Conditional Generative Adversarial Networks，CGAN）等。

图像风格迁移技术在艺术创作、图像处理和视觉效果等领域具有广泛的应用。它可以用于创作艺术作品、生成独特的图像效果，甚至可以应用于视频、动画等多媒体内容的处理。
