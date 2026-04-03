---
layout: book_chapter
title: "第57章 token 与 embedding"
description: "不管送进模型的是散文、代码还是表格，系统都不会直接“理解整句话”，而是先把输入切成 token，再把这些离散符号映射成向量。"
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

## 先说结论

不管送进模型的是散文、代码还是表格，系统都不会直接“理解整句话”，而是先把输入切成 token，再把这些离散符号映射成向量。  
语言模型后面的一切能力，都是在这道入口之后才开始生长的。

---

## 1. 语言进入模型的第一步，不是理解，而是切分

人读一句话时，往往先感到的是意思；  
模型并不是这样开始的。  
在它真正参与任何推理、生成和对话之前，文本必须先被拆成可计算单位。

这一步极其重要，因为它决定了模型一开始究竟在看什么。  
它并没有直接看到语义、意图和世界知识，而是先看到一串经过编码的符号片段。  
所以语言模型的入口，首先不是“意义”，而是“切分”。

一旦理解这一点，你对很多后续现象就会更清醒。  
模型看起来像在理解语言，其底层起点其实是一种高度工程化的符号处理过程。

---

## 2. token 不是词，也不只是字，它是一种为计算服务的折中单位

很多人第一次接触 tokenizer 时，会本能地以为英文按单词切、中文按单字切。  
真实系统远没有这么朴素。

token 更像一种计算单位。  
它可能是一个字、半个词、一个完整词、标点、空格，甚至某种常见前后缀。  
为什么要这样设计？因为模型必须在几件事之间找到平衡：

- 词表不能大到难以管理
- 序列不能长到推理成本失控
- 罕见词和新词不能被彻底处理坏
- 多语言和多领域最好还能兼容

所以 tokenization 从来不是语言学的自然切分，而是语言结构、统计频率和系统成本三者之间的一次折中设计。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_01.png)

> 图57-1 token 不是自然语言的天然边界，而是长度、词表和泛化之间的折中结果  

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_02.png)

> 图57-2 语言进入模型的最小入口路径  
> 模型并不是直接“看到句子意义”，而是先把文本切成 token，再把 token 映射成向量，之后一切上下文关系和推理能力才有机会开始形成。

这张图真正要压实的是：  
语言模型的起点不是意义本身，而是工程化后的可计算表示。  
如果入口切分和向量化方式发生变化，后面整条能力链都会被改写。  
也正因为如此，token 与 embedding 不是边角细节，而是底层感知接口。

---

![插图 I-35 tokenizer 的入口代价]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_35.png)

> 插图 I-35 tokenizer 的入口代价

## 3. tokenizer 其实是一种很容易被低估的系统能力
许多人把 tokenizer 当成训练前的预处理脚本，觉得它对模型主体无足轻重。  
这是一种典型误判。

tokenizer 会直接影响：

- 序列长度
- 罕见词和新词的表现
- 多语言公平性
- 代码、数学符号和结构化文本的处理方式
- 推理阶段的成本和延迟

也就是说，它并不只是把文本切开，而是在为整个模型规定输入世界的基本粒度。  
粒度一旦选错，后面再强的注意力和再大的参数，也只能在一个先天不优的入口上工作。

很多后续的系统边界，其实在这里就已经被埋下了。  
比如用户临时创造的新词、冷门术语或小众写法，如果 tokenizer 不能稳定处理，模型后面的理解和生成就会跟着偏掉。  
长上下文为什么迅速变贵，代码为什么容易被切得支离破碎，领域术语为什么学不稳，多语言为什么表现不均衡，这些问题往往都能一路追溯回 tokenizer 最开始的切分方式。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_03.png)

> 图57-3 tokenizer 并不是前处理脚本，而是在规定模型最初看到世界的基本粒度  

---

## 4. embedding 的价值，是让离散符号第一次进入可计算的几何世界

token 本身只是编号。  
编号不能直接参与语义运算，也不能自然支持相似性和上下文组合。  
embedding 的任务，就是把这些离散编号映射成连续向量。

这一步完成后，符号世界才第一次进入几何世界。  
不同 token 不再只是彼此不同的 ID，而开始拥有方向、距离、邻近关系。  
于是，模型终于能够在向量空间里做后面的事：变换、组合、对齐、聚类、预测。

从这个角度看，embedding 不是查表技巧，而是语言真正进入现代 AI 的那道门。  
没有它，Transformer 再强也没有对象可操作。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_04.png)

> 图57-4 embedding 的关键，不是换成向量，而是让符号第一次进入几何世界  

---

## 5. token 和 embedding 决定的，其实是模型“看见世界”的第一层坐标系

如果把模型比作一个正在形成内部世界观的系统，那么 tokenization 决定它先把世界切成什么颗粒度，embedding 决定这些颗粒度第一次进入什么坐标系。

这两步合起来，构成了模型的最底层感知接口。  
语言、代码、公式、表格之所以后来能在同一模型里共处，很大程度上都要先经过这里的设计。  
因此，这里看起来只是输入层，实则决定了模型的“第一视角”。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_05.png)

> 图57-5 token 与 embedding 如何定义模型的第一层坐标系  
> 看似只是输入层的几个设计，实际上会同时决定序列成本、表示质量和后续上下文计算的难易程度，从而共同构成模型看见世界的第一层坐标系。

许多后续能力差异，最后都能追溯到这里：  
切得太碎，上下文成本会膨胀；切得太粗，泛化会受损；嵌入空间组织得不好，后续层就要用更大代价去补。

---

## 6. 在产品世界里，token 也是成本单位，而不只是技术单位

很多用户是在使用模型后才意识到 token 的现实重量。  
同一句话被切成多少 token，会直接影响：

- 上下文能装多少信息
- 首 token 延迟和生成速度
- API 计费
- 长文档和代码任务的真实可行性

这说明 tokenization 并不是只属于研究人员的后台细节。  
它会实实在在地转化为产品体验和商业成本。  
因此，在现代基础模型时代，token 既是表示单位，也是算力单位，还是计费单位。

更直白地说，token 在今天至少同时扮演三种角色：

- 表示角色：它决定模型最初拿到什么颗粒度的世界
- 计算角色：它决定上下文长度、注意力成本和推理压力
- 产品角色：它决定计费方式、速度体验和长任务可行性

只有把这三层一起看，token 才不会被误读成一个纯底层实现细节。

分词粒度带来的系统差异也很具体。  
同一段代码，如果被切得过碎，就会让上下文长度迅速膨胀，推理成本上升，长文件任务更容易撞上窗口上限；  
而某些领域术语如果总被切裂，模型又可能迟迟学不稳这些概念。  
所以 tokenizer 看上去像预处理工具，实际上它会同时改写模型的表达入口、系统成本和产品可用边界。

这也是为什么很多团队做长文本、代码理解和多语言产品时，最后都会被迫重新审视 tokenizer。  
它平时看起来像藏在最底层的实现细节，可一旦进入产品化阶段，就会回来收账。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_06.png)

> 图57-6 在产品世界里，token 同时是表示角色、计算角色和计费角色  

---

## 7. 工程世界里，真正困难的是“空间兼容性”

embedding 一旦进入系统，就会带来许多长期问题：

- 新版本模型的空间是否兼容旧索引
- 多语言 token 的分布是否均衡
- 代码和自然语言是否共享同一表示空间
- 特定领域术语是否总被切得过碎

这些问题说明，token 与 embedding 并不是训练前的固定决策，而是贯穿模型演进和系统维护的长期地基。  
一旦基础地基反复震荡，整个上层应用都会被牵动。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/57_tokens_and_embeddings_deep_revised__mermaid_07.png)

> 图57-7 工程里真正困难的，常常不是“能不能切”，而是空间和接口能否长期兼容  

---

## 8. 容易误判的地方

### 误解一：token 就等于单词或汉字

token 是为计算服务的编码单位，它不等于自然语言学意义上的词或字。

### 误解二：embedding 只是把编号换成向量

真正关键的是，这些向量共同构成了后续一切上下文计算的基础空间。

### 误解三：tokenizer 调整一下不影响模型本质

它会直接改变长度、成本、泛化、多语言表现和系统兼容性。

---

## 9. 这一章为什么是 Transformer 之后必须单独讲的一步

上一章我们已经讲清 Transformer 为什么改变世界。  
这一章则把镜头拉近，告诉读者：  
Transformer 真正开始工作前，语言究竟是以什么形式进入这个主结构的。

下一章的 self-attention 会进一步解释，这些 token 向量进入模型后，是如何彼此建立上下文关系的。

---

## 延伸阅读建议

- 推荐起点：Vaswani 等《Attention Is All You Need》、Brown 等 `GPT-3`、Kaplan 等 `scaling laws` 这组材料很适合连起来读。
- 继续延伸：如果想把这一带真正读懂，建议把 tokenizer、attention、module design、pretraining 视作一条连续工程链，而不是四个独立名词。
- 检索关键词：`Transformer`、`self-attention`、`tokenization`、`pretraining`、`scaling laws`、`foundation model`。

---

## 10. 本章小结

1. 语言进入模型的第一步不是理解，而是切分。  
2. token 是面向计算的折中单位，不等于自然语言中的词或字。  
3. tokenizer 会显著影响序列长度、泛化、多语言表现和推理成本。  
4. embedding 让离散符号进入可计算的向量空间。  
5. token 与 embedding 共同定义了模型看见语言的第一层坐标系。  

核心判断：

> 语言模型真正接触到的，首先不是语言意义，而是被切分并嵌入向量空间后的符号序列，而这一步已经深刻决定了后面一切能力的边界。
