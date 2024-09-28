# Fed-MENU：Federated Multi-organ Segmentation with Inconsistent Labels

Owner: hikari

## 学术汇报：《Federated Multi-organ Segmentation with Inconsistent Labels》

### 1. 论文标题

- **IEEE Transactions on Medical Imaging** 上发表的论文《Federated Multi-organ Segmentation with Inconsistent Labels》。

### 2. 作者信息

- 作者：Xuanang Xu, Hannah H. Deng, Jaime Gateno, Pingkun Yan（高级会员，IEEE）。

### 3. 论文摘要

- **研究背景**：联邦学习（Federated Learning, FL）是一种新兴的范式，允许在不同数据所有者之间不共享数据的情况下进行大规模分散学习，有助于解决医学图像分析中的隐私问题。
- **研究挑战**：现有方法要求客户端标签一致性，限制了应用范围。实际中，不同临床场所可能只标注特定感兴趣的器官，与其他场所部分或没有重叠。
- **研究方法**：本文提出了一种新颖的联邦多编码U-Net（FedMENU）方法，用于多器官分割，通过不同的编码子网络提取器官特定特征。
- **研究结果**：在六个公共腹部CT数据集上的广泛实验表明，Fed-MENU方法能够有效地使用部分标记的数据集，获得性能优于其他局部或集中学习方法训练的模型。

### 4. 关键词

- 联邦学习、深度学习、医学图像分割、不一致标签。

### 5. 研究内容

- **问题定义**：在医学图像分割中，不同临床场所可能遵循不同的标注协议，导致不同场所的ROI（感兴趣区域）标签不一致。
- **方法创新**：提出了多编码U-Net（MENU-Net），通过不同的编码子网络提取特定器官的特征，并设计了辅助通用解码器（AGD）来增强这些特征的显著性。
- **实验验证**：使用四个公共腹部CT数据集进行实验，证明了Fed-MENU方法在不同部分标记的数据集上训练全局模型的有效性。

### 6. 实验结果

- 实验结果表明，Fed-MENU方法在不共享原始数据的情况下，能够利用不同部分标记的独立数据集来训练全局模型，且性能优于任何单一数据集上训练的局部学习模型，以及合并所有数据集训练的集中学习模型。

### 7. 研究意义

- 本研究提出了一种新的FL问题解决方案，即如何使用不一致标签的隔离数据集协作训练全局模型，具有临床意义和技术紧迫性。

### 8. 代码可用性

- 论文提供了公开的源代码链接：[https://github.com/DIAL-RPI/Fed-MENU。](https://github.com/DIAL-RPI/Fed-MENU%E3%80%82)

### 9. 结论

- 论文总结了Fed-MENU方法在处理FL中的部分标签问题方面的有效性和优越性能，并指出了未来可能的研究方向。

### 10. 讨论与展望

- 论文讨论了方法的局限性和在实际应用中可能遇到的安全问题，并提出了可能的解决方案，如结合差分隐私技术来增强FL方法的安全性。

---