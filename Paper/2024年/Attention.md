---
tags:
  - Method
title: Attention
date: 2023-10-4
NotionID-Paper: 10ee05f0-ba91-81ae-a619-d210d029e1b2
link-Paper: https://hikari-note.notion.site/Attention-10ee05f0ba9181aea619d210d029e1b2
---

高效非局部注意力机制（Efficient Non-Local Attention Mechanism, ENLA）的计算公式是改进自传统的非局部注意力机制，以减少计算复杂度并提高效率。ENLA通过引入投影矩阵来降低维度，并采用改进的注意力计算方法。

### 传统非局部注意力机制

在传统的非局部注意力机制中，计算公式如下：

1. **输入特征**：假设输入特征矩阵为 \(X \in \mathbb{R}^{N \times C}\)，其中 \(N\) 表示特征图的空间位置数（如 \(H \times W\)），\(C\) 表示通道数。

2. **生成 Q、K、V**：
    $$
    Q = XW_Q, \quad K = XW_K, \quad V = XW_V
    $$
    其中 \(W_Q\)、\(W_K\)、\(W_V\) 是权重矩阵，生成的 \(Q\)、\(K\)、\(V\) 的维度均为 \(\mathbb{R}^{N \times C}\)。

3. **计算注意力分数**：
    $$
    \text{Attention Scores} = QK^T
    $$

4. **归一化注意力分数**：
    $$
    \text{Attention Weights} = \text{Softmax}(QK^T / \sqrt{d_k})
    $$
    其中 \(d_k\) 是Key向量的维度。

5. **计算最终输出**：
    $$
    \text{Output} = \text{Attention Weights} \cdot V
    $$

### 高效非局部注意力机制（ENLA）

高效非局部注意力机制通过引入投影矩阵 \(\Phi\) 来减少计算复杂度。以下是其计算公式：

1. **输入特征**：与传统方法相同，输入特征矩阵为 \(X \in \mathbb{R}^{N \times C}\)。

2. **生成 Q、K、V**：
    $$
    Q = XW_Q, \quad K = XW_K, \quad V = XW_V
    $$

3. **投影矩阵 \(\Phi\)**：
    $$
    \Phi \in \mathbb{R}^{C \times m}
    $$
    其中 \(m\) 是投影后的维度，通常远小于 \(C\)。

4. **投影和计算变换后的 Q、K**：
    $$
    \tilde{Q} = Q\Phi, \quad \tilde{K} = K\Phi
    $$
    其中$$ \(\tilde{Q} \in \mathbb{R}^{N \times m}\) 和 \(\tilde{K} \in \mathbb{R}^{N \times m}\)$$

5. **计算注意力分数**：
    $$
    \text{Attention Scores} = \tilde{Q}\tilde{K}^T
    $$

6. **归一化注意力分数**：
    $$
    \text{Attention Weights} = \text{Softmax}(\tilde{Q}\tilde{K}^T / \sqrt{m})
    $$

7. **计算最终输出**：
    $$
    \text{Output} = \text{Attention Weights} \cdot V
    $$

### 总结

高效非局部注意力机制的核心思想是通过投影矩阵 \(\Phi\) 将 Q 和 K 的维度从 \(C\) 降低到 \(m\)，从而显著减少计算复杂度。最终计算得到的注意力权重与传统方法类似，但由于维度的减少，计算效率得到了显著提升。