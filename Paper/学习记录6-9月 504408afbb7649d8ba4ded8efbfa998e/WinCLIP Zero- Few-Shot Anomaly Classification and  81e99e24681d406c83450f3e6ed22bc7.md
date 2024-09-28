---
tags:
  - FSS
---

# WinCLIP: Zero-/Few-Shot Anomaly Classification and Segmentation

Owner: hikari

这篇论文名为《WinCLIP: Zero-/Few-Shot Anomaly Classification and Segmentation》，由 Jongheon Jeong、Yang Zou、Taewan Kim、Dongqing Zhang、Avinash Ravichandran 和 Onkar Dabeer 共同撰写。论文主要研究了在工业质量检测中的视觉异常分类和分割问题。以下是对整篇论文的总结，特别是方法（Method）部分的详细总结。

### 摘要（Abstract）

- 论文提出了一种新的方法，用于零样本（zero-shot）和少样本（few-normal-shot）异常分类和分割。
- 传统的研究依赖于为每个质量检测任务训练定制模型，这需要特定任务的图像和标注。
- 作者提出了基于窗口的 CLIP（WinCLIP），它通过组合状态词和提示模板，以及高效提取和聚合窗口/补丁/图像级特征与文本对齐。
- 还提出了 WinCLIP+，它利用正常图像的补充信息。
- 在 MVTec-AD 和 VisA 数据集上，WinCLIP 和 WinCLIP+ 在零样本和少样本异常分类和分割任务中取得了显著的性能提升。

### 引言（Introduction）

- 视觉异常分类（AC）和分割（AS）在工业制造中用于分类和定位缺陷。
- 现有方法主要关注单类或无监督异常检测，这些方法仅需要正常图像。
- 作者提出了零样本异常分类和分割的兴趣，并考虑了少样本正常样本的情况。

### 相关工作（Related Work）

- 论文回顾了视觉-语言建模、异常分类和分割、状态分类等领域的相关研究。

### 背景（Background）

- 论文介绍了异常分类和分割的基本概念，并讨论了 CLIP（Contrastive Language-Image Pre-training）的背景。

### 方法（Method）

### 4.1 语言驱动的零样本异常分类（Language-driven zero-shot AC）

- **二类设计**：通过将 CLIP 适应为具有两个类别提示的框架，定义“正常 [o]”与“异常 [o]”。
- **组合提示集合（CPE）**：通过生成标签和文本模板的所有组合，定义对象的“正常”和“异常”状态。这种方法比简单的二类设计更有效。

### 4.2 WinCLIP 用于零样本异常分割（WinCLIP for zero-shot AS）

- **窗口特征提取**：WinCLIP 通过滑动窗口提取密集的视觉特征，并应用 CPE 中的语言引导异常评分模型。
- **窗口嵌入**：每个窗口的特征通过 CLIP 图像编码器提取，并与文本嵌入进行相似度计算，然后将得分分布到每个像素上。
- **多尺度聚合**：通过聚合不同尺度的特征（如小尺度、中尺度和图像尺度）来提高分割的准确性。

### 4.3 WinCLIP+ 与少样本正常样本（WinCLIP+ with few-normal-shots）

- **参考关联**：WinCLIP+ 通过结合语言引导的预测和基于视觉的参考图像来提高异常分类和分割的准确性。
- **多尺度特征图**：使用 WinCLIP 提取的特征图在不同尺度上进行参考关联，并将这些预测与语言引导的预测相结合。

### 实验（Experiments）

- 论文在 MVTec-AD 和 VisA 数据集上进行了广泛的实验，验证了 WinCLIP 和 WinCLIP+ 在零样本和少样本异常分类和分割任务中的性能。
- 使用了 AUROC、AUPR、F1-max 等评价指标。

### 结论（Conclusion）

- 论文提出了一种新框架，通过细粒度文本定义和正常参考图像来定义正常性和异常性。
- WinCLIP 和 WinCLIP+ 在最近的基准测试中显著超越了现有的方法。

### 参考文献（References）

- 论文列出了相关的参考文献，涵盖了视觉-语言模型、异常检测和分割等领域的研究。

这篇论文的主要贡献在于提出了一种新颖的方法，利用预训练的 CLIP 模型和语言提示，实现了零样本和少样本情况下的异常分类和分割。通过组合提示集合和多尺度特征提取，WinCLIP 和 WinCLIP+ 在工业视觉检测任务中表现出色。