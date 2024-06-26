#### Pre-training of Deep Bidirectional Transformers for Language Understanding

**预训练多层双向transformer编码器的模型架构+MLM（masked language model）和NSP（next sentence prediction）**

### 创新点

- 多层双向连接的transformer encoder块架构更好的利用self-attention特性
- 使用masked language model进行预训练

### 对比

![[attachments/9f2e9b6f228a0e95cc535f433dcc7b1e_MD5.png]]
**GPT采用了transformer但为单向；而ELMo采用了双向的LSTM，但这种双向只是the concatenation of independently trained left-to-right and right-to-left LSTM，是一种伪双向，而且LSTM在捕捉长距离依赖上是弱于transformer的；**
- 在Transformer模型中，双向性主要通过自注意力（Self-Attention）机制实现。自注意力允许模型在生成每个单词的表示时考虑输入序列中的所有单词，包括它自己。因此，在BERT中，当计算某个单词的表示时，模型会考虑到整个输入序列中的所有单词，这就是所谓的双向上下文。
- 这种双向性是在Transformer的每个层次上实现的。BERT由多个这样的双向Transformer层堆叠而成。自注意力每个层中的自注意力机制都能访问前一层所有单词的输出，从而允许信息在序列中自由流动，无论是向前还是向后。

**对比ELMo**
- 训练独立的LTR和RTL模型，并将每个词块表示为这两个模型的串联。缺点有：(a) 这是单一双向模型的两倍代价；(b) 对于像QA这样的任务来说，这是不直观的，因为RTL模型无法对其问题的答案作出规定；(c) 它的强度远低于深度双向模型，因为深度双向模型可以选择使用左或右语境。
 **对比OpenAI GPT(Generative pre-trained transformer)**
 - 由于BERT是双向的Transformer block连接，考虑双向上下文，它特别适合于自然语言理解（NLU）任务，如问答、命名实体识别、情感分析等，这些任务需要对文本有深入的理解。
 - GPT的单向特性使其特别适合于文本生成任务，如文本续写、机器翻译、对话生成等。在这些任务中，模型需要根据给定的上文生成下文，而GPT的设计恰好符合这种顺序生成的需求。

### 输入表示

由三个embeding相加而成：
![[attachments/9e77f111e09d70bd1429aad2fd98e603_MD5.png]]
- Token Embeddings是词向量，第一个单词是CLS标志，可以用于之后的分类任务
- Segment Embeddings用来区别两种句子，因为预训练不光做LM还要做以两个句子为输入的分类任务
- Position Embeddings和之前文章中的Transformer不一样，不是三角函数而是学习出来的

### 预训练任务

 除输出层外，相同的体系结构还用于预训练和微调。 相同的预训练模型参数用于初始化不同下游任务的模型。 在微调期间，所有参数都将进行微调。 [CLS]是在每个输入示例前添加的特殊符号，[SEP]是特殊的分隔符标记（例如，分隔问题/答案）。
 
![[attachments/50495a852bd19b9a304d940239e85957_MD5.png]]

#### MLM

预训练阶段随机mask掉一定比例的input tokens，然后只预测这些被mask掉的tokens。这就是所谓的 “masked LM” (MLM), 其实就是 Cloze task完形填空，这样可以让bert同时学习到左右两侧的信息
在训练过程中随机mask 15%的token，而不是把像cbow一样把每个词都预测一遍。最终的损失函数只计算被mask掉那个token。

因为序列长度太大（512）会影响训练速度，所以90%的steps都用seq_len=128训练，余下的10%步数训练512长度的输入。

#### NSP下句预测

许多重要的下游任务，例如问答（QA）和自然语言推断（NLI），都是基于理解两个文本句子之间的关系的。这个没有办法直接由语言模型捕捉，所以他们增加了一个 next sentence prediction任务。具体的，对于训练语料中一对句子A和B，B有一半的概率是A的下一句，一半的概率是随机的句子。

### 微调

a 句子对分类（蕴含、矛盾或中立关系）
b 单句分类 （积极消极，新闻小说闲聊）
c 问答 （输出答案的start-end）
d 单句标记 （命名实体识别，词性标注
![[attachments/18fe60d9b27960d907ead652ea513b2c_MD5.png]]
![[attachments/aa6341ae781751a751d8a60e2c85046f_MD5.png]]

### 性能

斯坦福问题集，SWAG对抗生成数据集表现很好
![[attachments/d74051218d68f39323f41c533b89c4f4_MD5.png]]

### 结论

BERT是截至2018年10月的最新state of the art模型，通过预训练和微调横扫11项NLP任务，这就是最大的优点。而且它还用的是Transformer，也就是相对rnn更加高效、能捕捉更长距离的依赖。对比起之前的预训练模型，它捕捉到的是真正意义上的双向上下文信息。

**优点：**
（1）真正意义上的提供了双向语言模型；（2）fine-tuning方便；（3）效果好！

**缺点：**
（1）[MASK]标记在fine-tuning过程中不会出现，这造成了预训练与微调过程的失配，目前的办法只能缓解，问题依然存在，训练时如果使用过多[MASK]会影响模型表现；
（2）每个batch只预测15%的tokens，需要更多的轮次才能收敛。此外，
（3）BERT对于文本生成并不是最佳选择；
（4）分类时做平均可能要比只用[CLS] token的效果更好

# 名词解释

### Sentence Pair Classification（句子对分类）

在这种任务中，模型需要处理一对句子，并判断这两个句子之间的关系。例如，给定两个句子A和B，模型可能需要判断B是否是A的逻辑后续（如“下一句预测”任务），或者判断这两个句子是否属于相同的类别（如“自然语言推理”任务中的蕴含、矛盾或中立关系）。

- BERT处理句子对分类任务的方法是，在两个句子之间插入一个特殊的分隔符`[SEP]`，并在句子对的开头加上一个特殊的分类符号`[CLS]`。模型通过预训练学习到的知识，能够理解并编码这两个句子的内容及其上下文关系，最后使用`[CLS]`标记的输出来进行分类。

### Single Sentence Classification（单句分类）

单句分类任务要求模型对给定的单个句子进行分类。例如，判断一个句子表达的情感是积极的还是消极的（情感分析任务），或者判断一个句子属于哪个类别（如新闻分类）。

- 在处理单句分类任务时，BERT同样在句子开头加上`[CLS]`标记，并通过预训练学习到的知识来理解句子的含义。模型使用`[CLS]`标记的输出作为句子的整体表示，进而进行分类。

### 问答任务（Question Answering, QA）

在问答任务中，模型的目标是给定一个问题和一个文本段落，模型需要从文本段落中找到并返回回答问题的文本片段。BERT在这类任务上的应用通常涉及到理解问题的上下文以及定位文本段落中与问题相关的答案。

- **如何处理：** 对于问答任务，BERT模型首先将问题和文本段落作为一对输入序列，中间用`[SEP]`分隔符分开，并在序列的开头加上`[CLS]`标记。模型输出的是每个单词位置对应的向量表示，这些表示被用来预测答案的开始位置和结束位置。具体来说，模型训练时会学习两个分类器：一个用于预测答案开始的位置，另一个用于预测答案结束的位置。

### 单句标注任务（Single Sentence Tagging）

单句标注任务通常涉及到对句子中的每个单词或短语进行分类，例如命名实体识别（Named Entity Recognition, NER）、词性标注（Part-of-Speech Tagging, POS Tagging）等。这类任务要求模型能够识别句子中的特定元素，并为它们分配适当的类别标签。

- **如何处理：** 在单句标注任务中，BERT接收整个句子作为输入，并在句子的开头加上`[CLS]`标记。模型为句子中的每个单词生成一个向量表示，这些向量随后被用来为每个单词预测一个标签。与传统的基于序列的模型（如LSTM）不同，BERT能够利用其双向的特性，同时考虑每个单词前后的上下文，从而提高标注的准确性。

### 参考

[[NLP]BERT详解之一：论文解读 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/144026536)
