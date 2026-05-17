---
layout: book_chapter
title: "第58章 self-attention 的核心机制"
description: "Self-attention 让每个位置通过 Q、K、V 计算上下文关系，并把相关信息加权汇聚回来。"
book_active: true
permalink: /book/chapters/058-essence-of-self-attention/
book_kind: chapter
chapter_number: 58
chapter_slug: essence-of-self-attention
book_volume: volume_2
prev_url: /book/chapters/057-tokens-and-embeddings/
prev_title: "第57章 token 与 embedding"
next_url: /book/chapters/059-modular-structure-of-transformer/
next_title: "第59章 Transformer 的模块化结构"
---
# 第58章 self-attention 的核心机制

上一章讲 token 与 embedding：文本先被切成 token，再被映射成向量，并带上位置信息。第58章要回答下一步：这些 token 向量进入 Transformer 后，如何彼此建立上下文关系？

Self-attention 做的事，可以概括成一句话：每个位置根据当前表示生成查询，再和其他位置做匹配，最后把相关位置的信息按权重汇聚回来。这里的 attention 借用了“注意”这个词，但它在模型里首先是一套可并行计算的相关性路由机制。

这套机制强在灵活：一个 token 可以直接利用远处上下文；一段代码可以把变量使用和定义联系起来；一张图的 patch 也可以和其他区域建立关系。代价也很明确：如果每个位置都和其他位置计算关系，序列越长，计算和显存压力越重。

![插图 第58章 注意力热区与关系查找图]({{ site.baseurl }}/ai_ecosystem_book/images/chapter_058_essence_of_self_attention.png)

> 插图 第58章 注意力热区与关系查找图

---

## 1. 序列建模难在关系不总是相邻

语言、代码和长文档里，关键依赖经常隔得很远。一个代词可能依赖前文名词，一个函数调用可能依赖远处定义，一个问题的答案可能藏在上下文中另一个段落。

RNN 通过隐藏状态沿时间传递信息，但远距离依赖需要经过很多步。路径越长，信息越容易被压缩、稀释或覆盖。CNN 可以扩大感受野，但通常仍需要多层堆叠才能覆盖远处位置。

Self-attention 换了一种方式。它允许一个位置在同一层里和许多其他位置建立关系，并根据相关性汇总信息。这样，远处信息不必总是沿顺序链一步步传来，而可以通过 attention 权重被直接纳入当前表示。

---

## 2. Q、K、V 把“找谁”和“取什么”拆开

Self-attention 的核心是 Query、Key、Value。每个 token 向量会通过不同线性变换生成三类向量：

- Query：当前位置想查找什么线索。
- Key：每个位置如何被匹配和索引。
- Value：匹配后可以提供什么内容。

这三个向量的分工很重要。Query 和 Key 用来计算相关性，Value 承载要被汇聚的信息。模型因此可以把“谁和我相关”与“从相关位置取回什么内容”分开处理。

这种拆分让 attention 像一个内部检索过程：当前位置发出查询，所有位置提供索引，系统根据匹配结果取回内容。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/58_essence_of_self_attention_deep_revised__mermaid_01.png)

> 图58-1 Q、K、V 拆开的是找谁和取什么

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/58_essence_of_self_attention_deep_revised__mermaid_02.png)

> 图58-2 Q、K、V 的最小关系图
> Query 决定要找什么，Key 决定怎样被找到，Value 决定找到后提供什么。

---

## 3. 核心公式是在算相关性并加权汇聚

Self-attention 的常见公式是：

$$
\mathrm{Attention}(Q,K,V)=\mathrm{softmax}\left(\frac{QK^\top}{\sqrt{d_k}}\right)V
$$

它可以拆成三步：

1. `QK^\top`：计算每个 query 与所有 key 的匹配分数。
2. `softmax(...)`：把分数归一化为权重。
3. `权重 × V`：用权重汇总对应位置的 value。

其中 `\sqrt{d_k}` 是缩放项，用来避免维度较大时点积分数过于极端，导致 softmax 分布过尖。这个细节看似小，却关系到训练稳定性。

所以，attention 的机制可以落到很具体的三件事上：相关性计算、权重归一化、信息汇聚。它的强大来自这套计算可以在每一层、每个位置、多个头上反复执行。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/58_essence_of_self_attention_deep_revised__mermaid_03.png)

> 图58-3 核心公式的直觉，就是算相关性，再做加权汇聚

---

## 4. 注意力权重是运行时生成的信息路由

Self-attention 不预先规定某个位置只能看谁。每次输入不同，query 和 key 的匹配结果都会变化，注意力权重也会变化。

这意味着信息路径是在运行时生成的。一个 token 在某一层可能更多关注局部语法，在另一层可能关注远处实体；一段代码里，某些头可能把变量使用和定义连接起来；问答任务中，某些位置可能把问题词和证据片段连接起来。

注意力权重可以理解为一种动态路由：模型根据当前上下文决定信息从哪里来、以多大比例进入当前位置的新表示。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/58_essence_of_self_attention_deep_revised__mermaid_04.png)

> 图58-4 注意力权重是在运行时生成的路由
> 重点不只是模型看哪里，而是它每一层都在重新组织信息路径。

---

## 5. Self-attention 同时承担检索和融合

Self-attention 先检索，再融合。

检索阶段，它用 query 和 key 计算当前位置与其他位置的相关性。融合阶段，它用这些权重加权汇总 value，把来自不同位置的信息合成当前位置的新表示。

这就是为什么 Transformer 的每一层都像在重新组织上下文。上一层得到的表示，会在下一层再次生成 Q、K、V，再次计算关系，再次汇聚信息。经过多层堆叠，模型可以逐步形成更复杂的上下文表示。

这也解释了 attention 为什么不会简单复制内容。它汇总的是被权重调制后的 value，并且每一层都会继续被前馈网络、残差和归一化处理。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/58_essence_of_self_attention_deep_revised__mermaid_05.png)

> 图58-5 self-attention 同时完成信息来源检索和信息融合

---

## 6. 热力图有帮助，但不能被过度解释

Attention heatmap 会把位置之间的权重可视化成二维图。它能帮助我们看到某一层、某个头在某次输入中，哪些位置之间权重更高。

如果一个词依赖前文实体，热力图中可能出现远距离亮点；如果模型主要关注局部结构，亮区可能集中在对角线附近。这种可视化能帮助理解信息路由。

但 heatmap 不是完整解释。注意力权重高，不等于该位置就是因果原因；某一层某一头的图，也不能代表整个模型的决策过程。模型输出来自多层、多头、前馈网络和输出层共同作用。

因此，热力图适合作为分析入口，而不是作为模型解释的全部证据。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/58_essence_of_self_attention_deep_revised__mermaid_06.png)

> 图58-6 热力图能暴露部分路由关系，但不能等同于完整因果解释

---

## 7. Causal mask 让生成模型只能看过去

在语言生成中，模型通常要从左到右预测下一个 token。训练时虽然整段文本可以并行送入模型，但当前位置不能偷看未来 token，否则训练目标就会泄漏答案。

解决方法是 causal mask。它会把当前位置之后的 token 屏蔽掉，让当前位置只能关注自己以及之前的位置。

这点很关键。它解释了一个看似矛盾的事实：自回归 Transformer 在训练时可以并行处理整段序列，但每个位置的注意力仍受因果约束；推理时则需要一个 token 一个 token 地生成，并利用 KV cache 复用过去计算。

---

## 8. 代价来自成对关系计算

Self-attention 的能力来自位置之间的广泛连接，代价也来自这里。若序列长度为 `n`，每个位置都要和其他位置计算相关性，注意力矩阵规模大致是 `n × n`。

这就是常说的复杂度问题：

```text
attention cost ~ O(n^2)
```

序列越长，计算、显存、带宽和 KV cache 压力都会上升。长上下文模型、RAG、代码仓库分析、多轮对话和长视频理解，都会遇到这类成本。

工程上的许多优化都围绕这个问题展开：FlashAttention 改善内存访问，稀疏注意力减少关系数量，滑动窗口限制可见范围，线性注意力尝试近似全局关系，KV cache 优化则降低自回归推理中的重复计算。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/58_essence_of_self_attention_deep_revised__mermaid_07.png)

> 图58-7 self-attention 的能力来自广泛可见，代价也来自广泛可见

---

## 9. 在工程里，attention 既是能力中心，也是性能中心

研究视角下，attention 是上下文关系建模机制。工程视角下，它常常也是性能瓶颈。

模型为什么慢、显存为什么高、长上下文为什么吞吐下降、KV cache 为什么会成为部署问题，很多都和 attention 的计算方式有关。到了大模型服务阶段，attention 不再只是论文公式，而会变成推理引擎、编译器、显存调度和硬件设计共同面对的问题。

这也是为什么后面的 Transformer 模块化结构不能只讲 attention。一个可用模型还需要前馈层、残差连接、归一化、位置机制、并行策略和推理优化共同配合。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/58_essence_of_self_attention_deep_revised__mermaid_08.png)

> 图58-8 在研究里它是能力机制，在工程里它也常常是性能中心

---

## 10. 这一章如何接到 Transformer 的模块化结构

第58章讲的是 self-attention 如何让 token 向量之间建立关系。下一章会把视角放大到整个 Transformer block。

Transformer 的能力不是 attention 单独完成的。Attention 负责关系路由，前馈层负责局部非线性加工，残差连接和归一化负责训练稳定性，位置机制负责序列结构。只有把这些模块放在一起，才能理解 Transformer 为什么能够稳定堆叠、持续放大，并成为现代基础模型的主干。

---

## 本章收束

1. Self-attention 解决的是 token 之间如何动态建立上下文关系的问题。
2. Q、K、V 把匹配标准和可取内容拆开，使模型可以先找相关位置，再汇聚信息。
3. attention 公式做的是相关性计算、权重归一化和 value 加权汇聚。
4. 注意力权重是运行时生成的信息路由，会随输入、层和注意力头变化。
5. self-attention 同时承担检索与融合功能。
6. heatmap 有助于观察路由，但不能被当作完整因果解释。
7. causal mask 让自回归模型训练时遵守只看过去的约束。
8. self-attention 的成本来自成对关系计算，长上下文会显著放大系统压力。
9. 在工程里，attention 既是能力机制，也是性能和显存优化的重点。

这一章真正要留下的不是公式本身，而是公式背后的结构：每个 token 会在运行时根据上下文动态选择信息来源，并把这些信息汇聚成新的表示。

---

## 延伸阅读

- 推荐起点：Vaswani 等《Attention Is All You Need》中关于 scaled dot-product attention 和 multi-head attention 的部分。
- 继续延伸：把 Q/K/V、causal mask、multi-head attention、KV cache、FlashAttention、稀疏注意力和长上下文优化放在一起理解。
- 检索关键词：`self-attention`、`scaled dot-product attention`、`QKV`、`causal mask`、`KV cache`、`FlashAttention`、`sparse attention`。
