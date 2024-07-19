在医学图像分割领域，基于CNN和Transformer的模型都得到了广泛探讨。然而，CNN在长程建模能力上存在局限，而Transformers受到其二次计算复杂性的阻碍。最近，以Mamba为代表的状态空间模型（SSMs）已经成为一种有前途的方法。它们不仅擅长建模长程交互，而且保持了线性计算复杂性。在本文中，借助状态空间模型，我们提出了一种用于医学图像分割的U形架构模型，命名为Vision Mamba UNet（VM-UNet）。具体来说，引入了视觉状态空间（VSS）块作为捕捉广泛上下文信息的基础块，并构建了一个不对称的编码器-解码器结构。我们在ISIC17、ISIC18和Synapse数据集上进行了全面实验，结果表明VM-UNet在医学图像分割任务中表现出竞争力。据我们所知，这是第一个基于纯SSM模型构建的医学图像分割模型。我们旨在建立一个基准，并为未来更高效、更有效的基于SSM的分割系统的发展提供宝贵见解。我们的代码可在https://github.com/JCruan519/VM-UNet找到。
关键词：医学图像分割·状态空间模型

在医学图像分割领域，基于CNN和基于Transformer的模型都得到了广泛的研究。然而，CNN在远程建模能力方面表现出局限性，而Transformer则受到其二次计算复杂性的阻碍。最近，以Mamba为例的状态空间模型（SSM）已经成为一种很有前途的方法。它们不仅擅长于模拟长程相互作用，而且保持线性计算复杂性。在本文中，利用状态空间模型，我们提出了一个Ushape架构模型的医学图像分割，命名为视觉曼巴UNet（VM—UNet）。具体来说，视觉状态空间（VSS）块被引入作为基础块来捕获广泛的上下文信息，并构造了一个非对称的编码器—解码器结构。我们在ISIC17，ISIC18和Synapse数据集上进行了全面的实验，结果表明VM—UNet在医学图像分割任务中具有竞争力。据我们所知，这是第一个基于纯粹的SSM模型构建的医学图像分割模型。我们的目标是建立一个基线，并为未来开发更高效和更有效的基于SSM的细分系统提供有价值的见解。我们的代码可以在www.example.com上找到。

# 1 Introduction

Automated medical image segmentation techniques assist physicians in faster
pathological diagnosis, thereby improving the efficiency of patient care. Recently,
CNN-based and Transformer-based models have demonstrated remarkable per-
formance in various visual tasks, particularly in medical image segmentation.
UNet [27], as a representative of CNN-based models, is known for its simplicity
of structure and strong scalability, and many subsequent improvements are based
on this U-shaped architecture [11,37,28,29,30]. TransUnet [10], a pioneer among
Transformer-based models, is the first to employ Vision Transformer (ViT) [13]
for feature extraction during the encoding phase and utilizes CNN in the decod-
ing phase, demonstrating the significant capability for global information acqui-
sition. Subsequently, TransFuse [36] incorporates a parallel architecture of ViTand CNN, capturing both local and global features simultaneously. Furthermore,
Swin-UNet [9] combines Swin Transformer [21] with the U-shaped architecture,
introducing a pure Transformer-based U-shaped model for the first time.
Nevertheless, both CNN-based models and Transformer-based models have
inherent limitations. CNN-based models are constrained by their local recep-
tive field, considerably hindering their ability to capture long-range information.
This often leads to the extraction of inadequate features, resulting in subopti-
mal segmentation outcomes. Although Transformer-based models demonstrate
superior performance for global modeling, the self-attention mechanism demands
quadratic complexity in terms of image sizes, leading to a high computational
burden [31,13], particularly for tasks requiring dense predictions like medical
image segmentation. The current shortcomings in these models compel us to de-
velop a novel architecture for medical image segmentation, capable of capturing
strong long-range information and maintaining linear computational complexity.
Recently, State Space Models (SSMs) have attracted considerable interest
among researchers. Building on the foundation of classical SSM [18] research,
the modern SSMs (e.g., Mamba [16]) not only establish long-distance dependen-
cies but also exhibit linear complexity with respect to input size. Additionally,
SSM-based models have received substantial research across many fields, in-
cluding language understanding [17,16], general vision [38,20], etc. Particularly,
U-Mamba [24] has recently introduced a novel SSM-CNN hybrid model, mark-
ing its first application in medical image segmentation tasks. SegMamba [35]
incorporates SSM in the encoder part, while still using CNN in the decoder
part, suggesting a SSM-CNN hybrid model for 3D brain tumor segmentation
tasks. Although aforementioned works have utilized SSM for medical image seg-
mentation tasks, the performance of the pure SSM-based model has yet to be
explored.
Influenced by the success of VMamba [20] in image classification tasks, this
paper introduces the Vision Mamba UNet (VM-UNet) for the first time, a pure
SSM-based model designed to showcase the potential in medical image segmenta-
tion tasks. Specifically, VM-UNet is composed of three main parts: the encoder,
the decoder, and the skip connection. The encoder consists of VSS blocks from
VMamba for feature extraction, along with patch merging operations for down-
sampling. Conversely, the decoder comprises VSS blocks and patch expanding
operations to restore the size of the segmentation results. For the skip connection
component, to highlight the segmentation performance of the most original pure
SSM-based model, we adopt the simplest form of additive operation.
Comprehensive experiments are conducted on organ segmentation and skin
lesion segmentation tasks to demonstrate the potential of pure SSM-based mod-
els in medical image segmentation. Specifically, we conduct extensive experi-
ments on the Synapse [19], ISIC17 [8], and ISIC18 [12] datasets, the results of
which indicate that VM-UNet can achieve competitive performance. Moreover,
it is important to note that VM-UNet represents the most basic form of a pure
SSM-based segmentation model, as it does not include any specially designed
modules.
The main contributions of this paper can be summarized as follows: 1) We
propose VM-UNet, marking the first occasion of exploring the potential appli-
cations of purely SSM-based models in medical image segmentation. 2) Com-
prehensive experiments are conducted on three datasets, with results indicating
that VM-UNet exhibits considerable competitiveness. 3) We establish a base-
line for pure SSM-based models in medical image segmentation tasks, providing
valuable insights that pave the way for the development of more efficient and
effective SSM-based segmentation methods.

# 2 Preliminaries

In modern SSM-based models, i.e., Structured State Space Sequence Models
(S4) and Mamba, both rely on a classical continuous system that maps a one-
dimensional input function or sequence, denoted as x(t) ∈ R, through inter-
mediate implicit states h(t) ∈ RN to an output y(t) ∈ R. The aforementioned
process can be represented as a linear Ordinary Differential Equation (ODE):
```
h′(t) = Ah(t) +Bx(t)
y(t) = Ch(t)
(1)
```
where A ∈ RN×N represents the state matrix, while B ∈ RN×1 and C ∈ RN×1
denote the projection parameters.
S4 and Mamba discretize this continuous system to make it more suitable for
deep learning scenarios. Specifically, they introduce a timescale parameter ∆ and
transform A and B into discrete parameters A and B using a fixed discretization
rule. Typically, the zero-order hold (ZOH) is employed as the discretization rule
and can be defined as follows:
```
A = exp(∆A)
B = (∆A)−1(exp(∆A) − I) · ∆B
(2)
```
After discretization, SSM-based models can be computed in two ways: linear
recurrence or global convolution, defined as equations 3 and 4, respectively.
```
h′(t) = Ah(t) +Bx(t)
y(t) = Ch(t)
(3)
K = (CB,CAB, . . . ,CA
L−1
B)
y = x ∗ K
(4)
```
where K ∈ RL represents a structured convolutional kernel, and L denotes the
length of the input sequence x.

# 3 Methods

In this section, we initially introduce the overall structure of VM-UNet. Subse-
quently, we elaborate on the core component, the VSS block. Finally, we describe
the loss function utilized during the training process.

### 3.1 Vision Mamba UNet (VM-UNet)

As depicted in Figure 1 (a), the overall architecture of VM-UNet is presented.
Specifically, VM-UNet comprises a Patch Embedding layer, an encoder, a de-
coder, a Final Projection layer, and skip connections. Unlike previous methods
[9], we have not adopted a symmetrical structure but instead utilized an asym-
metric design.
The Patch Embedding layer divides the input image x ∈ RH×W×3 into non-
overlapping patches of size 4 × 4, subsequently mapping the dimensions of the
image to C, with C defaulting to 96. This process results in the embedded image
x′ ∈ R
H
4 ×W
4 ×C. Finally, we normalize x′ using Layer Normalization [7] before
feeding it into the encoder for feature extraction. The encoder is composed of
four stages, with a patch merging operation applied at the end of first three
stages to reduce the height and width of the input features while increasing the
number of channels. We employ [2, 2, 2, 2] VSS blocks across four stages, with
the channel counts for each stage being [C, 2C, 4C, 8C].

Similarly, the decoder is organized into four stages. At the beginning of last
three stages, a patch expanding operation is utilized to decrease the number
of feature channels and increase the height and width. Across the four stages,
we utilize [2, 2, 2, 1] VSS blocks, with the channel counts for each stage being
[8C, 4C, 2C, C]. Following the decoder, a Final Projection layer is employed to
restore the size of the features to match the segmentation target. Specifically, a
4-times upsampling is conducted via patch expanding to recover the height and
width of the features, followed by a projection layer to restore the number of
channels.
For the skip connections, a straightforward addition operation is adopted
without bells and whistles, thereby not introducing any additional parameters.

### 3.2 VSS block

The VSS block derived from VMamaba [20] is the core module of VM-UNet,
as depicted in Figure 1 (b). After undergoing Layer Normalization, the input
is split into two branches. In the first branch, the input passes through a lin-
ear layer followed by an activation function. In the second branch, the input
undergoes processing through a linear layer, depthwise separable convolution,
and an activation function, before being fed into the 2D-Selective-Scan (SS2D)
module for further feature extraction. Subsequently, the features are normalized
using Layer Normalization, and then an element-wise production is performed
with the output from the first branch to merge the two pathways. Finally, the
features are mixed using a linear layer, and this outcome is combined with a
residual connection to form the VSS block’s output. In this paper, SiLU [14] is
employed as the activation function by default.
The SS2D consists of three components: a scan expanding operation, an S6
block, and a scan merging operation. As shown in Figure 2(a), the scan expanding
operation unfolds the input image along four different directions (top-left to
bottom-right, bottom-right to top-left, top-right to bottom-left, and bottom-
left to top-right) into sequences. These sequences are then processed by the S6
block for feature extraction, ensuring that information from various directions is
thoroughly scanned, thus capturing diverse features. Subsequently, as illustrated
in Figure 2(b), the scan merging operation sums and merges the sequences from the four directions, restoring the output image to the same size as the input. The
S6 block, derived from Mamba [16], introduces a selective mechanism on top of
S4 [17] by adjusting the SSM’s parameters based on the input. This enables
the model to distinguish and retain pertinent information while filtering out the
irrelevant. The pseudo-code for the S6 block is presented in Algorithm 1.
