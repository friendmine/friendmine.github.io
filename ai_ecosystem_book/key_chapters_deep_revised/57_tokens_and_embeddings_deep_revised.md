---
layout: book_chapter
title: "第57章 token 与 embedding"
description: "语言进入 Transformer 之前，需要先被切成 token，再映射成向量；这一步决定了模型入口处看到什么，也决定了后续上下文计算的成本和边界。"
book_active: true
permalink: /book/chapters/057-tokens-and-embeddings/
book_kind: chapter
chapter_number: 57
chapter_slug: tokens-and-embeddings
book_volume: volume_2
prev_url: /book/chapters/056-why-transformer-changed-the-world/
prev_title: "第56章 为什么 Transformer 改变了世界"
next_url: /book/chapters/058-essence-of-self-attention/
next_title: "第58章 self-attention 的本质"
---
# 第57章 token 与 embedding

上一章讲 Transformer 如何改写现代 AI 的信息处理主干。但 Transformer 并不会直接处理“整句话的意义”。在 attention 开始计算之前，文本需要先被切成 token，再被映射成向量。

这一步看起来像输入预处理，实际决定了模型最初能看到什么。tokenizer 决定文本被切成什么颗粒度，embedding 决定这些离散符号以什么坐标进入向量空间。后面的 self-attention、上下文窗口、推理成本、多语言表现和代码能力，都会受到这道入口影响。

所以，token 与 embedding 不是边角细节。它们是语言进入模型的接口，也是现代语言模型成本、能力和系统兼容性的起点。

---

## 1. 语言进入模型的第一步是编码，而不是理解

人读一句话时，先感受到的是意义；模型不是这样开始的。模型收到文本后，第一步是把字符序列转换成可计算的符号编号。

例如一句话会先经过 tokenizer，被拆成一串 token；每个 token 再被映射成词表中的整数 ID；这些 ID 继续进入 embedding 层，变成向量。模型后面的计算，处理的是这些向量，而不是原始自然语言本身。

这一区分很重要。很多模型行为都可以追溯到入口编码：为什么同一段文本在不同模型里 token 数不同，为什么代码和数学符号容易变长，为什么长上下文成本会迅速上升，为什么某些语言或术语表现不均衡。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_01.png)

> 图57-1 token 不是自然语言的自然边界，而是长度、词表和泛化之间的折中结果

---

## 2. token 不是词，也不只是字

token 是为模型计算服务的编码单位，不等于自然语言学里的词，也不等于中文字符或英文单词。

一个 token 可能是一个字、一个词、半个词、常见后缀、空格、标点、代码片段，甚至是某种特殊控制符。常见 tokenizer 会基于统计频率和压缩效率学习子词单元，例如 BPE、WordPiece 或 unigram language model 这类方法。

这种设计是在几个目标之间折中：

- 词表不能大到难以训练和部署。
- 序列不能长到推理成本失控。
- 生僻词、新词、多语言和代码不能全部变成未知符号。
- 常见片段应该被更高效地编码。

因此，tokenization 不是“把语言自然切开”，而是把语言压成模型可以处理的离散符号流。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_02.png)

> 图57-2 语言进入模型的最小入口路径
> 文本先被切成 token，再映射成向量，后续上下文关系才有计算对象。

---

![插图 I-35 tokenizer 的入口代价]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_35.png)

> 插图 I-35 tokenizer 的入口代价

## 3. tokenizer 决定模型最初看见世界的粒度

tokenizer 不只是训练前脚本。它规定了模型最初看到世界的基本粒度。

如果切得太碎，同一段内容会变成更长序列，attention 成本和上下文占用都会上升。长文档、代码库、表格和多轮对话会更快撞到上下文窗口上限。

如果切得太粗，词表会膨胀，低频片段可能学不稳，遇到新词和跨领域内容时泛化会变差。多语言系统还会遇到另一层问题：某些语言被编码得更紧凑，某些语言被切得更碎，成本和效果都可能出现差异。

这就是 tokenizer 容易被低估的地方。它不是模型能力的全部，却会持续影响序列长度、泛化能力、多语言公平性、代码处理和推理成本。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_03.png)

> 图57-3 tokenizer 不是前处理脚本，而是在规定模型最初看到世界的基本粒度

---

## 4. embedding 把离散 ID 放进向量空间

tokenizer 输出的是整数 ID。整数 ID 只能表示“这是词表里的第几个符号”，不能直接表达相似性、上下文关系或语义结构。

embedding 层的作用，是把每个 token ID 映射成一个连续向量：

```text
token_id -> embedding vector
```

向量进入模型后，才可以参与矩阵运算、相似度比较、线性变换和 attention 计算。不同 token 的向量会在训练中被调整，使它们逐渐承载模型需要的关系结构。

需要注意的是，embedding 不是“词义字典”。一个 token 的初始 embedding 只是上下文计算的起点。经过多层 Transformer 后，同一个 token 在不同上下文中会形成不同的上下文化表示。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_04.png)

> 图57-4 embedding 的关键，不只是换成向量，而是让离散符号进入可计算空间

---

## 5. 位置信息让 token 向量进入序列

只有 token embedding 还不够。Transformer 的 attention 本身并不直接知道 token 的先后顺序，因此模型需要额外的位置信息。

常见做法包括 absolute position encoding、相对位置编码、旋转位置编码等。它们的作用，是让模型在处理 token 向量时，获得这些向量在序列中的位置信息，以及位置之间大致有什么关系。

这一步很关键。没有位置信息，“我喜欢你”和“你喜欢我”会更难区分；代码中的缩进、括号、变量使用位置也会更难被组织。位置编码不是装饰，而是序列结构进入 Transformer 的必要通道之一。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_05.png)

> 图57-5 token 与 embedding 共同定义模型看见语言的第一层坐标系

---

## 6. token 同时是表示单位、计算单位和产品单位

在产品世界里，token 不只是底层技术单位。它还会直接变成成本单位。

同一段文本被切成多少 token，会影响：

- 上下文窗口能装多少内容
- attention 计算和 KV cache 占用
- 首 token 延迟和生成速度
- API 计费
- 长文档、代码和表格任务的可行性

这就是为什么用户在处理长文档、代码仓库或多语言内容时，会明显感受到 token 的现实重量。tokenization 不只是模型入口，也会改变产品体验和商业成本。

一个系统如果 tokenizer 对代码切得很碎，同样上下文窗口能放下的代码就更少；如果对某种语言编码效率低，同样任务的成本就更高。模型能力和产品成本在这里连接起来。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_06.png)

> 图57-6 在产品世界里，token 同时是表示角色、计算角色和计费角色

---

## 7. tokenizer 和 embedding 会带来长期兼容问题

tokenizer 一旦确定，就和模型参数、embedding 矩阵、训练数据分布、上下文长度和下游系统绑定在一起。后期随意更换 tokenizer，通常意味着 embedding 层和大量模型参数都要重新适配。

工程中常见的问题包括：

- 新模型的 tokenizer 与旧索引不兼容。
- embedding 空间升级后，向量检索结果发生漂移。
- 多语言 token 分布不均衡，导致成本和效果差异。
- 代码、公式、表格被切得过碎，影响长上下文任务。
- 特定领域术语总被拆开，模型难以稳定学习。

这也是为什么 token 与 embedding 不是一次性小决策。它们会随着模型升级、产品迭代、检索系统、RAG 索引和多语言服务一起成为长期地基。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_07.png)

> 图57-7 工程里困难的，常常不是能不能切，而是空间和接口能否长期兼容

---

## 8. 容易误判的地方

### 误解一：token 就是单词或汉字

token 是面向计算的编码单位。它可能对应词、字、子词、标点、空格或特殊符号。

### 误解二：embedding 只是把编号换成向量

向量化只是表面。embedding 层为后续 attention、前馈网络和上下文表示提供了最初的几何空间。

### 误解三：tokenizer 可以随便换

tokenizer 与词表、embedding、模型参数和下游索引深度绑定。更换 tokenizer 往往意味着整套系统都要重新适配。

---

## 9. 这一章如何接到 self-attention

第57章说明了语言如何进入 Transformer：文本先被切成 token，token 再进入 embedding 空间，并叠加位置信息。

下一章要回答的问题是：这些 token 向量进入模型后，如何彼此建立关系？self-attention 会让每个位置根据 query、key、value 去判断应该从哪些位置取信息、取多少信息。

也就是说，token 与 embedding 提供“对象”，self-attention 负责在这些对象之间建立上下文关系。理解入口，才能理解后面的注意力计算为什么会产生能力，也为什么会产生成本。

---

## 10. 本章小结

1. 语言进入模型的第一步是编码和切分，而不是直接理解意义。
2. token 是面向计算的离散单位，不等于自然语言中的词或字。
3. tokenizer 在词表规模、序列长度、泛化、多语言和成本之间做折中。
4. embedding 把 token ID 映射成连续向量，让离散符号进入可计算空间。
5. token embedding 需要位置信息，才能进入序列结构。
6. token 同时是表示单位、计算单位和产品成本单位。
7. tokenizer 与 embedding 会影响模型升级、RAG 索引、多语言服务和长期兼容性。
8. self-attention 的对象，正是这些已经向量化并带有位置信息的 token。

核心判断可以压成一句话：

> 语言模型入口处接触到的不是完整意义，而是被 tokenizer 切分、被 embedding 向量化、再带入序列位置的符号流；这道入口会深刻影响后续能力和成本。

---

## 延伸阅读建议

- 推荐起点：Vaswani 等《Attention Is All You Need》中关于输入 embedding 与位置编码的部分，以及主流 tokenizer 方法的介绍。
- 继续延伸：把 tokenization、BPE、embedding、position encoding、KV cache、上下文窗口和向量检索一起理解。
- 检索关键词：`tokenization`、`BPE`、`WordPiece`、`embedding layer`、`position encoding`、`RoPE`、`context window`。
