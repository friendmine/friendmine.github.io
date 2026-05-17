---
layout: book_chapter
title: "第56章 为什么 Transformer 改变了世界"
description: "Transformer 改变世界，不只因为效果提升，而是它用 self-attention、可并行训练和可扩展结构，重写了现代 AI 的信息处理主干。"
book_active: true
permalink: /book/chapters/056-why-transformer-changed-the-world/
book_kind: chapter
chapter_number: 56
chapter_slug: why-transformer-changed-the-world
book_volume: volume_2
prev_url: /book/chapters/055-diffusion-models/
prev_title: "第55章 扩散模型：从噪声到生成"
next_url: /book/chapters/057-tokens-and-embeddings/
next_title: "第57章 token 与 embedding"
---
# 第56章 为什么 Transformer 改变了世界

上一章讲扩散模型：生成式视觉系统通过逐步去噪，获得了一条稳定、可控、可优化的工程路线。第56章转向另一条更广泛的主线：Transformer 为什么成为现代 AI 的核心结构之一。

Transformer 的影响，不只是某个任务上的分数提升。它改变了信息在模型内部流动的方式，也改变了训练如何并行、模型如何放大、任务如何统一、产业如何围绕一种结构建设软件和硬件基础设施。

在 RNN 时代，序列信息需要沿时间一步步传递；在 Transformer 中，序列位置之间可以通过 attention 更直接地建立关系。这个变化让模型更适合长距离依赖、更适合大规模并行训练，也更容易和预训练范式结合。它的代价同样明显：显存、上下文长度、注意力计算和推理成本都会成为系统问题。

---

![插图 I-06 Transformer 改写路线图]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_06.png)

> 插图 I-06 Transformer 改写路线图

## 1. Transformer 改变的是序列信息的组织方式

RNN 把序列处理成一条时间链：当前位置依赖上一时刻状态，信息沿着链条逐步向后传。这个结构直观，但并行性差，远距离信息要经过长路径才能影响当前判断。

Transformer 换了一种方式。它不再要求信息只能沿顺序状态逐步传递，而是让不同位置之间通过 attention 建立关联。一个 token 可以根据当前计算需要，对其他 token 分配不同权重，再汇总出新的表示。

这并不意味着 Transformer 不关心顺序。它仍需要位置编码或其他位置信息注入。变化在于，顺序不再以隐藏状态链的形式支配整个信息流动。模型获得了一种更灵活的内部路由方式。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/56_why_transformer_changed_the_world_deep_revised__mermaid_01.png)

> 图56-1 从顺序状态路线到 Transformer 路线
> Transformer 的关键变化，是把序列信息从逐步传递改成更直接的关联建模。

---

## 2. Self-attention 让位置之间动态建立关系

Self-attention 的核心可以用三个向量理解：Query、Key、Value。每个 token 会产生自己的 query、key 和 value。一个位置的 query 会和其他位置的 key 比较，得到注意力权重，再用这些权重加权汇总对应的 value。

简化写法是：

```text
Attention(Q, K, V) = softmax(QK^T / sqrt(d_k)) V
```

这个公式表达了三个动作：比较相关性、归一化成权重、按权重汇总信息。它让模型可以在同一层里根据上下文动态决定“该看哪里”。

这种机制比固定窗口或顺序递推更灵活。主语可以和远处谓语建立联系，代码变量可以和前文定义建立联系，问题可以和上下文中的证据片段建立联系。attention 不是理解本身，但它为上下文关系提供了高效的计算结构。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/56_why_transformer_changed_the_world_deep_revised__mermaid_02.png)

> 图56-2 Transformer 改写的是模型内部的信息流动方式

---

## 3. Transformer block 的价值来自整块结构

Transformer 不只有 attention。一个可大规模堆叠的 Transformer block，通常包括以下部分：

- 位置信息注入
- 多头 self-attention
- 前馈网络
- 残差连接
- 归一化层

Attention 负责在序列位置之间混合信息；前馈网络负责对每个位置的表示做非线性变换；残差连接让深层网络更容易训练；归一化让数值更稳定。多头机制则允许模型在不同子空间里同时关注不同关系。

这组结构组合在一起，才形成了稳定可堆叠的模块。Transformer 能够扩大到很多层、很多参数和很多数据，不是靠单个技巧，而是靠整块结构在训练稳定性、表达能力和硬件执行之间取得了较好平衡。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/56_why_transformer_changed_the_world_deep_revised__mermaid_03.png)

> 图56-3 Transformer block 的最小结构图
> Attention、前馈层、残差和归一化共同构成可稳定堆叠的结构单元。

---

## 4. 并行训练让它适合现代算力

RNN 的时间递推让训练难以充分并行。后一步依赖前一步状态，硬件很难一次性处理全部时间步。

Transformer 的训练方式更适合张量化并行。给定一段序列，attention 和前馈层中的大量计算可以转化为矩阵乘法、批处理和并行张量操作。这与 GPU、TPU 以及后来的 AI 加速器非常匹配。

这点对技术史很关键。一个模型结构如果只在数学上可行，但很难被现代硬件高效执行，就不容易成为产业主干。Transformer 的成功，既来自建模能力，也来自它和大规模并行算力之间的结构匹配。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/56_why_transformer_changed_the_world_deep_revised__mermaid_04.png)

> 图56-4 Transformer 和现代并行算力之间存在结构共振

---

## 5. 它让预训练和规模化变成稳定路线

Transformer 与预训练范式结合后，现代基础模型路线开始成形。模型可以在大量文本、代码、图像 patch 或多模态数据上预训练，再迁移到问答、摘要、生成、检索、代码、视觉和 Agent 等任务。

这种路线的价值在于可放大。更多数据、更大模型、更长训练，往往能在一组任务上带来可观察收益。规模定律、基础模型和大语言模型的发展，都依赖这种结构和训练范式的配合。

这不表示“只要变大，问题就会随之消失”。数据质量、训练目标、架构细节、优化策略、评测体系和系统成本都会影响结果。但 Transformer 提供了一条相对清晰的放大路径，让行业愿意围绕它持续投入。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/56_why_transformer_changed_the_world_deep_revised__mermaid_05.png)

> 图56-5 Transformer 把扩模型、扩数据和扩训练组织成更清晰的增长路线

---

## 6. 统一性让它跨过多个任务和模态

Transformer 的另一个影响，是统一性。文本可以被切成 token，代码可以被切成 token，图像可以被切成 patch 后送入类似结构，音频和视频也可以通过合适的编码方式进入序列建模框架。

这种统一性降低了系统复杂度。越来越多任务可以共享相似的表示方式、训练框架、推理基础设施和产品接口。BERT、GPT、ViT、多模态模型、代码模型和许多 Agent 系统，都围绕类似结构展开。

但统一不等于一切都被 Transformer 吞掉。第55章讲过，扩散模型仍是视觉生成的重要路线；语音、视频、机器人和世界模型也会引入各自结构。Transformer 更像一种强大的信息处理主干，而不是取消其他结构的唯一答案。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/56_why_transformer_changed_the_world_deep_revised__mermaid_06.png)

> 图56-6 Transformer 的影响力，很大一部分来自它能接纳更多任务和模态

---

## 7. 它改写了产业链，而不只是研究论文

Transformer 成为主干后，被重写的不只是模型论文。训练框架、推理引擎、模型压缩、编译器、硬件加速、向量数据库、RAG 系统、Agent 框架和应用产品，都开始围绕它优化。

这就是它改变世界的现实含义。一个架构如果能让研究、工程、硬件、产品和资本同时围绕它组织资源，它就不再只是一个模型选择，而会变成产业默认基础设施的一部分。

RNN/LSTM 并不是没有价值，它们在某些流式和低资源场景仍然有用。但它们失去工业中心位置，是因为 Transformer 与预训练、并行算力、基础模型和产品接口形成了更强的组合。

---

## 8. 它的代价也在系统层面放大

Transformer 的代价同样清楚。标准 self-attention 对序列长度的计算和显存开销通常随长度平方增长。上下文越长，attention 矩阵越大，训练和推理都会变重。

这带来一系列系统问题：

- 长上下文成本高
- KV cache 占用显存
- 推理延迟和吞吐压力大
- 多轮对话需要管理上下文压缩
- 大模型部署需要量化、并行和路由优化

因此，Transformer 的胜利不是因为它没有缺点，而是因为它的收益足以支撑整个生态去优化这些缺点。FlashAttention、稀疏注意力、线性注意力、长上下文压缩、KV cache 优化和模型并行，都是围绕这些代价展开的工程回应。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/56_why_transformer_changed_the_world_deep_revised__mermaid_07.png)

> 图56-7 Transformer 的胜利，不是没有代价，而是收益足以支撑生态持续优化

---

## 9. 这一章如何接到 token 与 embedding

第56章解释了 Transformer 为什么改变现代 AI 的主结构。下一章会把视角拉到入口处：在 Transformer 开始计算之前，语言究竟怎样进入模型？

Transformer 处理的不是原始句子，而是一串 token 向量。tokenizer 决定文本如何被切分，embedding 决定离散 token 如何进入向量空间。只有经过这一步，self-attention 才有对象可以比较、加权和组合。

所以第57章会讲 token 与 embedding。理解这一步，才能理解后面的 self-attention、上下文窗口、推理成本、多语言表现和代码建模为什么会受到输入切分方式影响。

---

## 10. 本章小结

1. Transformer 改变的是序列信息的组织方式，而不只是某个任务上的分数。
2. Self-attention 通过 Q/K/V 机制，让不同位置之间动态建立关系。
3. Transformer block 的稳定性来自 attention、前馈层、残差和归一化的组合。
4. 它适合矩阵运算和并行训练，因此与现代加速硬件高度匹配。
5. 它与预训练结合后，形成了可持续放大的基础模型路线。
6. 它的统一性让文本、代码、视觉、多模态和 Agent 系统共享相似底座。
7. 它改写了训练、推理、硬件、软件和产品链条。
8. 它也带来长上下文、显存、KV cache 和推理成本等系统代价。

核心判断可以压成一句话：

> Transformer 的关键影响，不是局部效果提升，而是它用可并行、可扩展、可统一的信息处理结构，成为现代 AI 产业愿意共同建设的主干之一。

---

## 延伸阅读建议

- 推荐起点：Vaswani 等《Attention Is All You Need》；读完本章后再回看，会更容易理解 self-attention 与并行训练的意义。
- 继续延伸：把 Transformer 与 scaling laws、BERT、GPT、ViT、FlashAttention、KV cache 和长上下文优化放在一起理解。
- 检索关键词：`Transformer`、`self-attention`、`QKV`、`scaling laws`、`foundation model`、`KV cache`、`FlashAttention`。
