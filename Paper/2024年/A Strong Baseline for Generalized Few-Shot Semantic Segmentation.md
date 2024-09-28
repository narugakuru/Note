---
tags:
  - Segmentation
  - FSS
  - Baseline
NotionID-Paper: 10fe05f0-ba91-81e4-b6d2-fd4707b7034e
link-Paper: https://hikari-note.notion.site/A-Strong-Baseline-for-Generalized-Few-Shot-Semantic-Segmentation-10fe05f0ba9181e4b6d2fd4707b7034e
---
[[2211.14126] 广义少样本语义分割的强基线 --- [2211.14126] A Strong Baseline for Generalized Few-Shot Semantic Segmentation](file:///E:/Paper/[2211.14126]%20%E5%B9%BF%E4%B9%89%E5%B0%91%E6%A0%B7%E6%9C%AC%E8%AF%AD%E4%B9%89%E5%88%86%E5%89%B2%E7%9A%84%E5%BC%BA%E5%9F%BA%E7%BA%BF%20---%20[2211.14126]%20A%20Strong%20Baseline%20for%20Generalized%20Few-Shot%20Semantic%20Segmentation.html)

在论文中提到的 \( \max I(X; P) \) 函数是基于互信息（Mutual Information，MI）的概念来构建的，用于衡量模型的输入 \( X \)（即图像的像素分布）和输出 \( P \)（即模型的预测）之间的相互依赖性。互信息可以被理解为输出 \( P \) 中有多少信息是可以由输入 \( X \) 预测的。互信息的计算公式如下：

$$ I(X; P) = H(P) - H(P | X) $$

这里，\( H(P) \) 是边际熵（Marginal Entropy），而 \( H(P | X) \) 是条件熵（Conditional Entropy）。两者的计算方法如下：

1. **边际熵 \( H(P) \)**：
   边际熵表示模型输出 \( P \) 的整体分布的熵，它衡量模型输出的分散程度。在语义分割的上下文中，\( P \) 通常指的是所有像素预测类别的概率分布。边际熵的计算公式为：$$ H(P) = -\sum_{c} p(c) \log p(c) $$这里 \( p(c) \) 是模型对于类别 \( c \) 的预测的边缘概率，即模型预测图像中某个像素属于类别 \( c \) 的概率。

2. **条件熵 \( H(P | X) \)**：
   条件熵表示在已知输入 \( X \) 的情况下，输出 \( P \) 的熵。它衡量了模型输出的不确定性。在训练过程中，我们希望模型在给定输入 \( X \) 后能做出尽可能确定的预测，即条件熵尽可能低。条件熵的计算公式为：
$$ H(P | X) = -\sum_{x} p(x) \sum_{c} p(c | x) \log p(c | x) $$
这里 \( p(c | x) \) 是在给定输入 \( x \) 的条件下，模型预测像素属于类别 \( c \) 的条件概率。
在训练过程中，通过最大化互信息 \( I(X; P) \)，相当于同时最小化条件熵 \( H(P | X) \) 并适当调整边际熵 \( H(P) \)。这使得模型在给定输入的情况下能够做出更有信心的预测（低条件熵），同时保证输出的整体分布不过于集中或偏离（适当的边际熵）。
论文中的方法通过优化这个基于互信息的目标函数，鼓励模型学习到的特征表示尽可能地保留类别预测的有用信息，从而提高模型在少样本语义分割任务中的性能。

