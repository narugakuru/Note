# FedDG: Federated Domain Generalization on Medical Image Segmentation via Episodic Learning in Continuous Frequency Space

Owner: hikari

这是一篇深度学习领域的学术论文，请你总结一下论文，做成类似学术汇报的格式

## 学术汇报：基于连续频率空间情景学习的医学图像分割联邦域泛化

### 1. 论文信息

- **标题**: FedDG: Federated Domain Generalization on Medical Image Segmentation via Episodic Learning in Continuous Frequency Space
- **作者**: Quande Liu, Cheng Chen, Jing Qin, Qi Dou, Pheng-Ann Heng
- **单位**: 香港中文大学计算机科学与工程系，香港理工大学护理学院
- **联系作者**: Qi Dou

### 2. 研究背景与动机

- **联邦学习**: 允许分布式医疗机构在保护隐私的前提下协同学习共享预测模型。
- **挑战**: 即使在联邦学习中训练的模型，在应用于联邦之外的未见过的医院时，性能仍可能下降。

### 3. 研究问题

- **联邦域泛化（FedDG）**: 学习一个可以泛化到未见过的目标域的联邦模型。

### 4. 研究方法

- **ELCFS（Episodic Learning in Continuous Frequency Space）**: 一种新颖的方法，允许每个客户端在数据分散的约束下利用多源数据分布。
- **频率空间插值机制**: 通过连续频率空间插值，以保护隐私的方式跨客户端传递分布信息。
- **情景学习范式**: 设计了面向边界的情景学习范式，以提高模型在医学图像分割场景中的泛化能力。

### 5. 技术贡献

- **隐私保护**: 提出了一种在分散数据集上学习泛化FL模型的隐私保护解决方案。
- **情景学习**: 提出了一种新颖的面向边界的情景学习方案，增强了模型在模糊边界区域的泛化能力。

### 6. 实验结果

- **性能对比**: 在两个医学图像分割任务（视网膜眼底图像分割和前列腺MRI分割）上，与现有技术相比，展现了优越的性能。
- **代码**: 代码已开源，可在GitHub上找到。

### 7. 结论与展望

- **贡献**: 首次提出并解决了联邦学习模型在完全未见过的领域上的泛化问题。
- **潜力**: 为联邦学习中遇到的其他问题（如数据异质性）提供了解决思路。

### 8. 致谢

- 研究得到了广东省重点研发计划、国家自然科学基金、香港创新科技基金的支持。

---

### 9. 问题与讨论

- **数据异质性**: 如何进一步处理不同医疗机构之间的数据异质性问题？
- **模型泛化能力**: 如何评估和提高模型在更广泛场景下的泛化能力？
- **实际应用**: 该方法在实际医疗环境中的可行性和效果如何？

### 10. 结束语

- 本研究为医学图像分割领域提供了新的视角和工具，有望推动联邦学习在医疗领域的进一步应用和发展。

## Method: 详细讲解

### 3.1. Federated Domain Generalization (FedDG)

**定义**: 在FedDG中，目标是使用K个分布式源域的数据来学习一个模型𝑓𝜃:𝑋→𝑌*fθ*:*X*→*Y*，该模型能够直接泛化到一个完全未见过的测试域T上。

**挑战**:

1. **数据分布式存储**: 每个客户端只能访问其本地数据分布，限制了充分利用多源分布来学习泛化参数的能力。
2. **数据异质性**: 不同医疗机构获取的医学图像可能在成像条件上存在显著差异，导致合作数据集之间分布不连续，不足以确保在复杂临床环境中获得良好的泛化能力。
3. **医学图像结构的边界模糊性**: 医学解剖结构的边界区域通常存在高度模糊性，这对以往的域泛化技术构成了挑战，因为它们通常无法确保在这种模糊区域的特征具有域不变性。

### 3.2. Continuous Frequency Space Interpolation

**基础**: 为了解决分布式数据集的限制，我们提出在客户端之间交换分布信息，使每个本地客户端能够获得多源数据分布，以学习泛化参数。

**步骤**:

1. **频率空间信号获取**: 通过快速傅里叶变换(FFT)将图像转换到频率空间，分解为幅度谱和相位谱，分别反映图像的低级分布（风格）和高级语义（对象）。
2. **分布银行构建**: 构建一个共享的分布银行A，包含所有客户端的幅度谱，作为共享的分布知识。
3. **连续插值机制**: 设计一个在频率空间内的连续插值机制，通过在本地数据和传输的幅度谱之间进行插值，生成具有其他客户端分布特征的新图像。

**公式**:

- 𝐹(𝑥𝑖𝑘)(𝑢,𝑣,𝑐)*F*(*xik*)(*u*,*v*,*c*): 快速傅里叶变换得到的频率空间信号。
- 𝐴𝑖𝑘*Aik* 和 *Pik*: 分别为幅度谱和相位谱。
    
    𝑃𝑖𝑘
    
- 𝐴𝑘→𝑛𝑖,𝜆*Ak*→*ni*,*λ*: 插值后的幅度谱，其中λ是插值比例，M是控制低频成分交换规模的二进制掩模。

### 3.3. Boundary-oriented Episodic Learning

**情景学习**: 在每个本地客户端建立情景元学习方案，通过模拟训练/测试域偏移来学习泛化模型参数。

**步骤**:

1. **元训练**: 使用原始输入作为元训练数据，使用分割Dice损失更新模型参数。
2. **元更新**: 使用更新后的参数在保留的元测试数据上进行虚拟评估，使用元目标函数进行优化。

**边界导向的元优化**:

- 设计了一个新的边界导向目标，通过从频率空间生成的多源分布数据中学习，增强了在模糊边界区域的域不变性。
- 提取边界相关和背景相关特征，使用InfoNCE目标函数来增强这些特征的域不变性和可区分性。

**整体目标函数**:

- 𝐿𝑚𝑒𝑡𝑎*Lmeta*: 由分割Dice损失和边界导向目标组合而成，用于优化原始参数。

### 结论

该方法通过在频率空间内进行连续插值和面向边界的情景学习，有效地解决了联邦域泛化问题。通过这种方式，模型能够在不同的医疗机构之间学习并泛化，即使在未见过的医院也能保持高性能。

## 损失函数

这篇论文讨论了联邦医学影像分割中的一些关键公式。以下是对这些公式的解释：

1. **InfoNCE 损失函数 \(\ell(h_m, h_p)\)**：
\[
\ell(h_m, h_p) = -\log \frac{\exp(h_m \odot h_p / \tau)}{\sum_{q=1, q \neq m}^{2K} \mathbf{F}(h_m, h_q) \cdot \exp(h_m \odot h_q / \tau)}
\]
这里，\(\odot\)表示余弦相似度：
\[
a \odot b = \frac{\langle a, b \rangle}{\|a\|_2 \|b\|_2}
\]
其中，\(\mathbf{F}(h_m, h_q)\) 是一个指示函数，当 \(h_m\) 和 \(h_q\) 是正样本对时为1，负样本对时为0。参数 \(\tau\) 是温度参数，用于调节相似度的分布。
2. **边界损失函数 \(\mathcal{L}_{\text{boundary}}\)**：
\[
\mathcal{L}*{\text{boundary}} = \sum*{m=1}^{2K} \sum_{p=m+1}^{2K} \frac{(1 - \mathbf{F}(h_m, h_p)) \cdot \ell(h_m, h_p)}{B(K, 2) \times 2}
\]
这里，\(B(K, 2)\) 是组合数，用于计算样本对的数量。
3. **整体局部学习目标 \(\mathcal{L}_{\text{meta}}\)**：
\[
\mathcal{L}*{\text{meta}} = \mathcal{L}*{\text{seg}}(t_i^k; \hat{\theta}^k) + \gamma \mathcal{L}*{\text{boundary}}(x_i^k, t_i^k; \hat{\theta}^k)
\]
这里，\(\mathcal{L}*{\text{seg}}\) 是分割的 dice 损失，\(\mathcal{L}_{\text{boundary}}\) 是边界损失，\(\hat{\theta}^k\) 是根据公式4更新的参数，\(\gamma\) 是平衡超参数。
4. **优化目标**：
\[
\theta^k = \arg \min_{\theta^k} \mathcal{L}*{\text{seg}}(x_i^k; \theta^k) + \mathcal{L}*{\text{meta}}(x_i^k, t_i^k; \hat{\theta}^k)
\]
这是针对参数 \(\theta^k\) 的最终优化目标，结合了分割损失和元学习目标。

这些公式的目的是增强特征的域不变性和判别能力，通过正则化特征的类内一致性和类间分离性，提升医学图像分割模型在不同分布下的泛化能力。

## 快速傅里叶变换

https://blog.csdn.net/a493823882/article/details/117925648

**图像可以看做是一个定义为二维平面上的信号，该信号的幅值对应于像素的灰度（对于彩色图像则是RGB三个分量），如果我们仅仅考虑图像上某一行像素，则可以将之视为一个定义在一维空间上信号，这个信号在形式上与传统的信号处理领域的时变信号是相似的**。不过是一个是定义在空间域上的，而另一个是定义在时间域上的。所以图像的频率又称为空间频率，它反映了图像的[像素](https://so.csdn.net/so/search?q=%E5%83%8F%E7%B4%A0&spm=1001.2101.3001.7020)灰度在空间中变化的情况。例如，一面墙壁的图像，由于灰度值分布平坦，其低频成分就较强，而高频成分较弱；而对于国际象棋棋盘或者沟壑纵横的卫星图片这类具有快速空间变化的图像来说，其高频成分会相对较强，低频则较弱（注意，是相对而言）。

![Untitled](Paper/attachments/FedDG%20Federated%20Domain%20Generalization%20on%20Medical%20I%206e77342e9b62432aaf735889c97dbad9/Untitled.png)

![Untitled](Paper/attachments/FedDG%20Federated%20Domain%20Generalization%20on%20Medical%20I%206e77342e9b62432aaf735889c97dbad9/Untitled%201.png)

# 4o

### Method Section Summary for "FedDG: Federated Domain Generalization for Medical Image Segmentation"

### 3. Method

**3.1 Federated Domain Generalization**

- **Preliminaries**:
    - \( (X, Y) \): Joint image and label space.
    - \( S = \{S_1, S_2, ..., S_K\} \): Set of K distributed source domains.
    - Each domain \( S_k = \{(x_{ki}, y_{ki})\}_{i=1}^{N_k} \) is sampled from a domain-specific distribution \( (X^k, Y) \).
    - Objective: Learn a model \( f_\theta : X \rightarrow Y \) from K distributed source domains that generalizes to an unseen target domain T.
- **Federated Learning Process**:
    - Central server and K local clients communicate.
    - In each federated round \( t \):
        - Clients receive global model weights \( \theta \).
        - Clients update the model with their local data \( S_k \) for E epochs.
        - Central server aggregates local parameters \( \theta_k \) to update the global model.
    - Federated averaging algorithm (FedAvg) aggregates local parameters weighted by local dataset sizes.
- **Challenges**:
    - **Decentralized Data**: Clients can only access their individual local distributions, limiting the use of multi-source distributions.
    - **Heterogeneity**: Medical images from different clinical sites have distinct distributions, challenging domain-invariance.
    - **Ambiguity**: Medical anatomical structures often have ambiguous boundaries, posing challenges for domain-invariance of features in these regions.

**3.2 Continuous Frequency Space Interpolation (CFSI)**

- **Concept**: Exchange distribution information across clients without sharing raw images by leveraging frequency space.
- **Frequency Space Transformation**:
    - Convert images to frequency space, separating style information from content.
    - Share frequency space information among clients to simulate multi-source distributions.
- **Interpolation Mechanism**:
    - Use interpolation in frequency space to transform local images towards styles of other clients.
    - Gradually increase interpolation ratio \( \lambda \) to simulate continuous distribution changes.
    - This enriches multi-source distributions, enhancing domain-invariance.
- **Implementation**:
    - Hyperparameters: \( \lambda \) (interpolation ratio), \( \tau \) (temperature parameter), \( \gamma \) (weight for training objectives).
    - Local models trained with Adam optimizer.
    - Train 100 federated rounds with local epoch \( E = 1 \).

This method section outlines the challenges of federated domain generalization in medical image segmentation and presents the proposed solution using continuous frequency space interpolation to achieve better generalizability across unseen domains .

“FedDG:联邦域泛化用于医学图像分割”的方法部分总结

＃＃＃＃方法

**3.1联邦域泛化**

- **前期工作**:
- \( (X, Y) \):联合图像和标签空间。
—\( S = \{S_1, S_2, ..., S_K\} \): K个分布式源域的集合。
—每个域\( S_k = \{(x_{ki}, y_{ki})\}*{i=1}^{N_k} \)从一个特定于域的分布\( (X^k, Y) \)中采样。
-目标:学习一个模型\( f*\theta : X \rightarrow Y \)从K个分布式源域推广到一个看不见的目标域T。
- **联邦学习过程**:
—中心服务器与K个本地客户端通信。
-在每个联合回合\( t \):
-客户端接收全局模型权重\( \theta \)。
—客户端使用本地数据\( S_k \)更新E epoch。
—中心服务器通过聚合本地参数\( \theta_k \)更新全局模型。
—联邦平均算法(FedAvg)根据本地数据集大小对本地参数进行加权。

**挑战**:

- **分散的数据**:客户端只能访问他们各自的本地发行版，限制了多源发行版的使用。
- *异质性:来自不同临床地点的医学图像具有不同的分布，挑战域不变性。
- **歧义性**:医学解剖结构的边界往往具有歧义性，这对这些区域特征的域不变性提出了挑战。

3.2连续频率空间插值(CFSI)**

- **概念**:通过利用频率空间在不共享原始图像的情况下跨客户端交换分布信息。
- **频率空间变换**:
-将图像转换为频率空间，将样式信息与内容分离。
—在客户端之间共享频率空间信息，模拟多源分布。
- **插补机制**:
-在频率空间中使用插值将本地图像转换为其他客户端的样式。
-逐渐增加插值比\( \lambda \)，模拟连续的分布变化。
-这丰富了多源分布，增强了域不变性。
- **实施**:
-超参数:\( \lambda \)(插值比)，\( \tau \)(温度参数)，\( \gamma \)(训练目标的权重)。
-用Adam优化器训练的局部模型。
-用本地纪元\( E = 1 \)训练100个联邦回合。

该方法部分概述了医学图像分割中联邦域泛化的挑战，并提出了使用连续频率空间插值的解决方案，以实现更好的跨未知域的泛化。