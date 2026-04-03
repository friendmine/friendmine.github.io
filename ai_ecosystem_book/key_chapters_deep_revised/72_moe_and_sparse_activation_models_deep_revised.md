---
layout: book_chapter
title: "第72章 MoE 与稀疏激活模型"
description: "MoE 的关键，不是让模型“无条件更大”，而是让系统在拥有巨大参数总量的同时，每次只激活其中一部分。"
book_active: true
permalink: /book/chapters/072-moe-and-sparse-activation-models/
book_kind: chapter
chapter_number: 72
chapter_slug: moe-and-sparse-activation-models
book_volume: volume_2
prev_url: /book/chapters/071-small-models-large-models-and-system-division-of-labor/
prev_title: "第71章 小模型、大模型与系统分工"
next_url: /book/chapters/073-limits-and-hallucinations-of-llms/
next_title: "第73章 LLM 的边界与幻觉"
---
# 第72章 MoE 与稀疏激活模型

## 先说结论

MoE 的关键，不是让模型“无条件更大”，而是让系统在拥有巨大参数总量的同时，每次只激活其中一部分。  
它第一次大规模把“参数总量”和“单次实际计算量”拆开了，但代价是路由、负载均衡和跨设备通信都会变得更难。

---

## 1. 为什么 MoE 会在大模型时代变得重要

当模型继续变大，一个现实问题很快出现：  
如果每次推理都把所有参数完整算一遍，成本会迅速失控。  
训练很贵，推理更贵，部署也会变得更难。

MoE 的基本直觉就是：  
参数可以很多，但不必每次都全部激活。  
这让系统有机会在保持超大容量的同时，把单次计算量压在相对可承受的范围里。

这一步非常关键。  
它不是让大模型“省一点”，而是改写了“大模型必须 dense 全算”的默认前提。

但这里最容易被误判的一点是：  
MoE 不是白拿容量，而是在用更复杂的系统组织去换取“参数很多、单次不全算”的新平衡。  
如果团队只盯着参数总量和理论 FLOPs，忽视路由、通信和负载均衡，最后很容易得到一个“看起来更大，实际却没更划算”的系统。

这也是 MoE 最容易被高估的地方。  
参数规模变大，并不自动等于训练和部署都更便宜；  
很多时候，它只是把 dense 模型的单点压力，换成了更难处理的系统税。

---

## 2. dense model 和 sparse model 的差别，在于每次到底算多少

传统稠密模型（dense model）的特点是：  
某一层里所有参数几乎都会被当前输入参与计算。  
而稀疏模型（sparse model）里的 MoE 更像这样一层结构：

- 有很多专家模块（expert）
- 有一个路由器（router）
- 每次输入只会激活其中少数几个专家模块

最小写法可以压成：

$$
y=\sum_{i \in TopK(x)} g_i(x) E_i(x)
$$

意思是：  
输入 `x` 到来后，路由器（router）先决定最值得调用哪些专家模块（expert），  
再把这些 expert 的输出按权重 `g_i(x)` 组合起来。  
也就是说，模型总体参数可以很大，但每次只走其中几条路。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/72_moe_and_sparse_activation_models_deep_revised__mermaid_01.png)

> 图72-1 MoE 的最小结构  
> 模型的总容量不再等于单次必须全部动员的计算量。

---

## 3. 它的价值，不只是更大，而是更合理地使用昂贵计算

MoE 的真正吸引力在于，它让不同输入去激活不同参数子集。  
从系统视角看，这相当于：

- 总体容量更大
- 单次计算受控
- 模型内部出现某种“功能分工”可能性

这也是为什么很多人会把它理解成“大模型内部的稀疏分工结构”。  
虽然这种分工未必总是干净明确，但它确实提供了一种新的扩张方式：  
不是所有能力都必须靠同一套 dense 参数承担。

---

![插图 I-36 MoE 的路由现场]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_36.png)

> 插图 I-36 MoE 的路由现场

## 4. 但 router 并不是白送的，它本身就会带来新的系统税
MoE 真正难的地方，往往不是“router 会不会选”，而是它一旦选偏，整套系统会不会立刻出现局部雪崩。  

如果流量突然集中到某一类任务，比如代码或某个小语种，router 可能会把大量请求都压到少数 expert 上。  
这时部分 expert 会迅速过热，其他 expert 却大面积空转；为了搬运这些失衡流量，跨卡通信和调度成本又会进一步上涨。  

也就是说，论文里看起来很漂亮的“稀疏激活省算力”，到了真实集群里，可能会被负载不均、通信开销和调度复杂度重新吃掉。  
这正是 MoE 系统税最容易被低估的地方。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/72_moe_and_sparse_activation_models_deep_revised__mermaid_02.png)

> 图72-2 MoE 的难点，不在“会不会选”，而在“选完之后整套系统怎么扛住”。

---

## 5. 训练和推理都会被它改写

MoE 不是只在推理侧占便宜。  
它对训练同样会带来明显影响：

- 并行策略改变
- 专家并行（expert parallel）成为重要话题
- load balance 约束进入损失函数或训练目标
- router 的稳定性变成训练成败关键因素

而在推理侧，MoE 又会引出新的权衡：

- 单次 FLOPs 可能下降
- 但路由、访存、通信不一定同步下降
- 整体时延未必线性改善

所以 MoE 的价值从来不是“免费更强”，而是把问题从 dense 计算转成稀疏路由和系统协调。

从产品角度看，这种转移很关键。  
因为用户最终并不为“参数是不是按稀疏方式被组织”买单，而只会看到时延、成本和稳定性。  
如果稀疏结构没有真的把这些结果改善出来，MoE 再优雅，也还没有兑现成系统价值。

---

## 6. 它为什么和“小模型、大模型与系统分工”天然相连

上一章讲的是不同规模模型在系统里的分工。  
MoE 进一步提醒我们：  
即便在一个模型内部，分工也会出现。

这意味着未来的系统分工可能有两层：

- 模型之间的分工
- 模型内部 expert 之间的分工

一旦这两层都同时存在，AI 系统就更像一个被组织起来的能力网络，而不是一块完整均匀的计算体。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/72_moe_and_sparse_activation_models_deep_revised__mermaid_03.png)

> 图72-3 MoE 让“分工”不只发生在模型之间，也发生在模型内部。

---

## 7. 容易误判的地方

### 误解一：MoE 就是“参数更多所以更强”

它真正关键的是稀疏激活，不是参数数字本身。

### 误解二：稀疏激活就一定更便宜

通信、调度、负载均衡都可能把便宜重新吃回去。

### 误解三：MoE 会自然长出清晰专家分工

部分分工会出现，但未必总是稳定、清楚、可解释。

---

## 延伸阅读建议

- 推荐起点：Shazeer 等关于稀疏专家模型的经典工作，以及 Switch Transformer，是理解 MoE 为什么重新变成主流的关键节点。
- 继续延伸：如果想把这一章和工程现实接起来，建议继续看 routing、load balance、expert parallel、serving cost。
- 检索关键词：`Mixture of Experts`、`Switch Transformer`、`routing`、`load balancing`、`expert parallel`。

---

## 9. 本章小结

1. MoE 把总参数量和单次实际计算量拆开了。  
2. 它通过 router + expert 结构实现稀疏激活。  
3. 它的价值在于更合理地使用昂贵计算，而不是“免费变大”。  
4. 通信、调度和负载均衡是它最现实的系统税。  
5. 它让分工开始进入模型内部。  

核心判断：

> MoE 真正代表的，不只是更大的模型，而是大模型第一次系统性地学会了“不是每次都把所有能力一起叫出来”。
