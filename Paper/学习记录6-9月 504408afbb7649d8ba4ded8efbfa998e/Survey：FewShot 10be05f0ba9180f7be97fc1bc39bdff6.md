---
tags:
  - Survey
  - FSS
---

# Survey：FewShot

Owner: hikari

根据迁移学习的分类，FSL 问题有很多变体，包括单样本学习（OSL）、零样本学习（ZSL）、跨领域小样本学习。

- `One-shot learning`：OSL 对支持数据集中的每个样本只有一个正确的标签，其目的是在已发现的类别中找到最相似的类别作为匹配。在警方审讯过程中，这两个过程非常相似。证人只看了嫌疑人一眼，警方提供的照片可以视为查询图像。证人只需要对这些照片回答“是”或“否”。同样，单样本学习不会对数据进行具体的分类，而只是按照相似度函数的顺序进行聚类。根据现有的工作，单样本学习可以分为两种主要方法。一种是使用生成模型来将先验知识[[十三](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib13)，[14](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib14)，[15](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib15)]，其中贝叶斯编程学习[[16](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib16)]是最具代表性的框架[[17](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib17)]。另一种方法是将 OSL 分类任务转换为验证任务[[18](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib18)，[19](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib19)]。
- `Zero-shot learning`：ZSL 是由 Lampert 等人首次提出的[[20](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib20)]，它考虑了 FSL 中更极端的情况。在没有任何查询样本的情况下，推理机制仅依赖于识别以前未见过的样本。ZSL 本质上是通过使用高维语义特征[[21](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib21)，[22](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib22)，[23](https://ar5iv.labs.arxiv.org/html/2205.06743?_immersive_translate_auto_translate=1#bib.bib23)]来替代低维原始数据。嵌入表示和自动编码器是构建中间语义空间的最有效方法，其中包含更全面定义类别的属性。到目前为止，零样本学习是最接近人类智能的方法之一，可以辨别以前未观察到的类别。一次性学习和 FSL 本质上可以被视为特殊的 ZSL。
- `Cross-domian few-shot learning`：在迁移学习中，目标域中每个类都有足够多的可用样本，当源域和目标域之间发生较大的域转移时，知识迁移往往变得十分具有挑战性。跨域小样本学习结合了迁移学习和FSL的挑战。在存在域间隙的情况下，源域和目标域中的类的交集为空，而目标域中每个类的可用样本量极小，单靠源域数据来提升模型的泛化能力，对模型的性能提升非常有限。本工作主要关注特征的域转移变换和辅助数据集的构建。跨域小样本学习可以说是目前FSL领域最具挑战性的场景之一。