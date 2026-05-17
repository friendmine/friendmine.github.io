---
layout: book_chapter
title: "第72章 MoE 与稀疏激活模型"
description: "MoE 把参数总量和单次计算量拆开，让模型拥有更大容量的同时，每次只激活其中一部分专家模块。"
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

第71章讲的是模型之间的系统分工。
第72章继续往里推进一层：在一个大模型内部，也可以出现分工。
MoE（Mixture of Experts）就是这种思路的代表。

MoE 的关键，不是把模型无条件做大，而是把“参数总量”和“单次实际计算量”拆开。
模型可以拥有很多专家模块，但每个 token 只会被路由到少数几个专家上计算。
这样，系统获得更大容量的同时，不必每次都把全部参数算一遍。

但 MoE 不是免费午餐。
它把 dense 模型的计算压力，转成了路由、负载均衡、专家并行、跨设备通信和服务调度问题。
如果只看参数规模和理论 FLOPs，很容易高估 MoE 的收益，低估它的系统税。

---

## 1. Dense 模型每次都会动用大量参数

传统稠密模型（dense model）里，每个 token 经过一层时，大部分相关参数都会参与计算。
这种结构简单、稳定、易于实现，也方便做推理优化。

但模型越大，问题越明显：

- 训练成本上升
- 推理成本上升
- 显存压力上升
- 延迟和吞吐更难平衡
- 部署门槛更高

如果每次请求都要完整动员巨大模型，系统很快会被成本和时延限制。
这就是稀疏激活路线出现的背景。
它试图回答一个问题：能不能让模型拥有更大总容量，但每次只使用其中一部分？

---

## 2. MoE 的基本结构是 router 加 experts

MoE 通常会把某些前馈网络层替换成专家层。
这一层里有多个 expert，还有一个 router。

基本流程是：

1. token 表示进入 MoE 层。
2. router 为这个 token 选择少数几个 expert。
3. 被选中的 expert 分别计算输出。
4. 系统把 expert 输出按权重合并。

可以用一个简化公式表达：

$$
y=\sum_{i \in TopK(x)} g_i(x) E_i(x)
$$

这里 `TopK(x)` 表示 router 为输入 `x` 选择的少数 expert，`g_i(x)` 是对应权重，`E_i(x)` 是第 `i` 个 expert 的输出。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/72_moe_and_sparse_activation_models_deep_revised__mermaid_01.png)

> 图72-1 MoE 的基本结构
> 模型总容量不再等于单次需要全部动员的计算量。

这个结构让模型可以扩大参数总量，同时控制每个 token 的实际计算路径。

---

## 3. 稀疏激活的价值在于容量与计算解耦

MoE 的吸引力来自一个分离：

- 参数总量可以很大
- 每次 token 只激活少数 expert
- 单次 FLOPs 可以相对受控
- 不同输入可以走不同参数路径

这让模型内部出现一种能力分工的可能。
某些 expert 可能更常处理代码，某些更常处理特定语言或任务形态。
这种分工未必总是干净可解释，但它提供了一种不同于 dense 扩容的路径。

换句话说，MoE 的价值不是“参数数字更大”。
它的价值在于：系统有机会用更高总容量承载更多模式，同时避免每次推理都支付全量计算成本。

---

![插图 I-36 MoE 的路由现场]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_36.png)

> 插图 I-36 MoE 的路由现场

## 4. Router 是 MoE 的控制中枢

router 决定每个 token 该去哪些 expert。
这个选择会直接影响模型质量、训练稳定性和推理效率。

router 需要处理几件事：

- 哪些 expert 更适合当前 token
- 每个 expert 接收多少 token
- 是否要保持负载均衡
- 是否允许 token 被丢弃或转发
- 路由分布是否会随训练失衡

MoE 常见的 Top-1 或 Top-2 routing，就是在质量和成本之间做取舍。
Top-1 更省，但表达空间更窄；
Top-2 更灵活，但计算和通信更重。

router 一旦选偏，expert 分工就会失衡。
有些 expert 过载，有些 expert 空闲，系统既浪费容量，也可能降低质量。

---

## 5. 负载均衡是 MoE 的现实难点

在理想情况下，不同 expert 都能被合理使用。
但真实训练和推理里，流量常常会集中到少数 expert。

例如，某类代码请求、某种语言、某种格式输入，可能持续被 router 分配给同一组 expert。
这会带来几个问题：

- 少数 expert 过载
- 其他 expert 利用率低
- token 可能因为容量限制被丢弃或降级处理
- 跨设备通信变得不均衡
- 服务延迟出现长尾

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/72_moe_and_sparse_activation_models_deep_revised__mermaid_02.png)

> 图72-2 MoE 的难点，不在会不会选，而在选完之后整套系统怎么扛住

因此，MoE 训练里通常会引入 load balancing loss、capacity factor、expert parallel 等机制。
这些机制并不是细枝末节，而是 MoE 能否稳定工作的工程核心。

---

## 6. MoE 的系统税来自通信、调度和并行

MoE 看起来减少了单次计算量，但它增加了系统协调成本。

常见系统税包括：

- token 要被路由到不同 expert
- expert 可能分布在不同设备上
- 跨卡通信增加
- batch 内 token 分布不均
- expert 负载波动导致长尾延迟
- 推理服务更难做稳定调度

Dense 模型虽然计算重，但路径相对整齐。
MoE 的路径更稀疏，也更不规则。
这会让集群调度、通信拓扑、内存管理和推理服务都变复杂。

所以，MoE 的收益不能只看理论 FLOPs。
如果通信和调度开销过高，稀疏激活节省下来的计算可能被系统税吃掉。

---

## 7. 训练和推理都会被 MoE 改写

训练侧，MoE 会带来新的问题：

- router 是否稳定
- expert 是否塌缩
- 负载是否均衡
- expert parallel 如何切分
- 梯度和通信如何组织
- 不同 expert 是否学到有用分工

推理侧，MoE 也不只是“少算一点”：

- 单 token 延迟未必线性下降
- batch 形态更难稳定
- 热门 expert 会造成服务热点
- 多租户请求会改变负载分布
- 模型版本升级会影响路由行为

因此，MoE 是模型结构和系统工程共同改变。
它不能只由模型团队单独判断，还需要训练、推理、基础设施和产品侧一起评估。

---

## 8. MoE 和系统分工是一条连续逻辑

第71章讲的是模型之间的分工。
MoE 讲的是模型内部的分工。

两者逻辑相通：

- 系统层面：路由器决定任务交给哪个模型
- MoE 层面：router 决定 token 交给哪些 expert
- 系统层面：不同模型承担不同复杂度和成本位置
- MoE 层面：不同 expert 承担不同输入子空间

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/72_moe_and_sparse_activation_models_deep_revised__mermaid_03.png)

> 图72-3 MoE 让分工不只发生在模型之间，也发生在模型内部

这说明 AI 系统的未来不只是“更大”。
更重要的是能力如何被组织，计算如何被调度，昂贵资源如何只在需要时被调用。

---

## 9. MoE 不能解决模型边界和幻觉问题

MoE 可以改善容量和计算的关系，但它不是可靠性的通用答案。

它不能直接解决：

- 事实校验
- 引用来源
- 幻觉
- 工具执行安全
- 权限控制
- 长期记忆治理

如果底层目标仍然是生成高概率文本，MoE 只是改变了模型内部的参数组织方式。
它不会让模型直接拥有事实核查能力，也不会让语言输出自然变成可靠证据。

这就是第73章要接着讨论的内容。
模型结构可以更强、更大、更稀疏、更高效，但 LLM 的事实边界和幻觉风险仍要正面处理。

---

## 10. 本章小结

1. Dense 模型每次会动用大量参数，规模继续上升后成本压力会显著增加。
2. MoE 通过 router 和 experts，让每个 token 只激活少数专家模块。
3. 稀疏激活的价值在于把参数总量和单次计算量部分解耦。
4. router 决定 token 去向，是质量、负载和效率的控制中枢。
5. 负载均衡、capacity factor、expert parallel 是 MoE 的工程核心。
6. MoE 的系统税主要来自通信、调度、并行和服务长尾。
7. MoE 同时改写训练和推理，不只是模型结构技巧。
8. MoE 延续了第71章的分工逻辑，只是把分工推进到模型内部。
9. MoE 不能直接解决幻觉和事实可靠性，第73章将收束这些边界问题。

核心判断：

> MoE 的意义，不是让模型简单变大，而是让模型在更大总容量和可承受单次计算之间建立新的组织方式；它带来稀疏分工，也带来路由、负载和通信这些现实系统税。
