好的，以下是这些公式用 LaTeX 格式列出的表示：

1. **InfoNCE 损失函数 \(\ell(h_m, h_p)\)**：
   $$
   \ell(h_m, h_p) = -\log \frac{\exp\left( \frac{h_m \odot h_p}{\tau} \right)}{\sum_{q=1, q \neq m}^{2K} \mathbf{F}(h_m, h_q) \cdot \exp\left( \frac{h_m \odot h_q}{\tau} \right)}
   $$
   这里，\(\odot\) 表示余弦相似度：
   $$
   a \odot b = \frac{\langle a, b \rangle}{\|a\|_2 \|b\|_2}
   $$
   其中，\(\mathbf{F}(h_m, h_q)\) 是一个指示函数，当 \(h_m\) 和 \(h_q\) 是正样本对时为1，负样本对时为0。参数 \(\tau\) 是温度参数，用于调节相似度的分布。


2. **边界损失函数 \(\mathcal{L}_{\text{boundary}}\)**：
   $$
   \mathcal{L}_{\text{boundary}} = \sum_{m=1}^{2K} \sum_{p=m+1}^{2K} \frac{(1 - \mathbf{F}(h_m, h_p)) \cdot \ell(h_m, h_p)}{B(K, 2) \times 2}
   $$
   这里，\(B(K, 2)\) 是组合数，用于计算样本对的数量。

3. **整体局部学习目标 \(\mathcal{L}_{\text{meta}}\)**：
   $$
   \mathcal{L}_{\text{meta}} = \mathcal{L}_{\text{seg}}(t_i^k; \hat{\theta}^k) + \gamma \mathcal{L}_{\text{boundary}}(x_i^k, t_i^k; \hat{\theta}^k)
   $$
   这里，\(\mathcal{L}_{\text{seg}}\) 是分割的 dice 损失，\(\mathcal{L}_{\text{boundary}}\) 是边界损失，\(\hat{\theta}^k\) 是根据公式4更新的参数，\(\gamma\) 是平衡超参数。

1. **优化目标**：
   $$
   \theta^k = \arg \min_{\theta^k} \mathcal{L}_{\text{seg}}(x_i^k; \theta^k) + \mathcal{L}_{\text{meta}}(x_i^k, t_i^k; \hat{\theta}^k)
   $$
   这是针对参数 \(\theta^k\) 的最终优化目标，结合了分割损失和元学习目标。
   


### InfoNCE 损失函数简介

InfoNCE（Information Noise-Contrastive Estimation）损失函数是一种用于对比学习（contrastive learning）的方法。它通过最大化正样本对之间的相似性，同时最小化负样本对之间的相似性，从而学习到有意义的表示（representations）。

### 损失函数公式

InfoNCE 损失函数的公式如下：

$$
\ell(h_m, h_p) = -\log \frac{\exp\left( \frac{h_m \odot h_p}{\tau} \right)}{\sum_{q=1, q \neq m}^{2K} \mathbf{F}(h_m, h_q) \cdot \exp\left( \frac{h_m \odot h_q}{\tau} \right)}
$$

### 变量含义

- \( h_m \)：表示锚点（anchor）样本的特征向量。
- \( h_p \)：表示与锚点样本 \( h_m \) 相关的正样本的特征向量。
- \( h_q \)：表示一个样本的特征向量，它可以是正样本或负样本。
- \( \odot \)：表示余弦相似度。
- \( \tau \)：温度参数，用于调节相似度的分布。
- \( \mathbf{F}(h_m, h_q) \)：一个指示函数，当 \( h_q \) 是 \( h_m \) 的正样本时为 1，负样本时为 0。

### 余弦相似度

余弦相似度用于衡量两个向量之间的相似性，其公式为：

$$
a \odot b = \frac{\langle a, b \rangle}{\|a\|_2 \|b\|_2}
$$

其中 \( \langle a, b \rangle \) 表示向量 \( a \) 和 \( b \) 的内积， \( \|a\|_2 \) 和 \( \|b\|_2 \) 分别表示向量 \( a \) 和 \( b \) 的 \( \ell_2 \) 范数。

### 分子与分母

- **分子**：
  $$
  \exp\left( \frac{h_m \odot h_p}{\tau} \right)
  $$

  表示锚点样本 \( h_m \) 与其正样本 \( h_p \) 之间相似度的指数形式。温度参数 \( \tau \) 调节相似度的敏感度。

- **分母**：

  $$
  \sum_{q=1, q \neq m}^{2K} \mathbf{F}(h_m, h_q) \cdot \exp\left( \frac{h_m \odot h_q}{\tau} \right)
  $$

  是所有样本对（包括正样本和负样本）与锚点样本 \( h_m \) 之间相似度的指数和。指示函数 \( \mathbf{F}(h_m, h_q) \) 确保只考虑正样本对。

### 损失函数的意义

InfoNCE 损失函数通过最大化正样本对 \( h_m \) 和 \( h_p \) 的相似度（即使分子尽量大），同时最小化负样本对与锚点样本的相似度（即使分母尽量大），从而实现对比学习的目标。这种方法帮助模型学习到能够有效区分不同样本的特征表示。