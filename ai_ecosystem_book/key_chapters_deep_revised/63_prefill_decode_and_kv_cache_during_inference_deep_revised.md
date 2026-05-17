---
layout: book_chapter
title: "第63章 推理阶段：prefill、decode 与 KV cache"
description: "训练和后训练决定模型具备什么能力，推理系统决定这些能力能否以可承受的延迟、成本和并发交付给用户。"
book_active: true
permalink: /book/chapters/063-prefill-decode-and-kv-cache-during-inference/
book_kind: chapter
chapter_number: 63
chapter_slug: prefill-decode-and-kv-cache-during-inference
book_volume: volume_2
prev_url: /book/chapters/062-reinforcement-learning-and-rlhf/
prev_title: "第62章 强化学习与 RLHF：从奖励到行为塑形"
next_url: /book/chapters/064-multimodal-models/
next_title: "第64章 多模态模型"
---
# 第63章 推理阶段：prefill、decode 与 KV cache

## 先说结论

第62章讲的是后训练如何塑造模型行为。
但模型经过训练和对齐之后，还要面对一个更现实的问题：它能不能在用户可接受的延迟、成本和并发条件下把能力交付出来。

大语言模型的在线推理，不能只用一句“模型正在生成”概括。
它通常可以分成两个阶段：`prefill` 负责读入并处理已有上下文，`decode` 负责逐 token 生成输出。
两者连在一起，却是两类不同的系统问题。

`KV cache` 是自回归模型能做流式交互的重要机制。
它把已经算过的 Key 和 Value 留下来，让后续生成不用反复重算全部历史。
代价也很直接：上下文越长、并发越高，缓存越占显存和带宽。
所以，推理工程不是附属优化，而是大模型产品能否成立的交付层。

---

## 1. 训练决定能力，推理决定能力能否被用户碰到

训练阶段回答的是模型会什么。
后训练回答的是模型怎样更符合人类协作方式。
推理阶段回答的则是：这些能力能否被稳定、快速、可负担地送到用户面前。

真实产品关心的问题很具体：

- 用户多久看到第一个 token
- 后续输出是否顺滑
- 单次请求成本是否可承受
- 并发上来时是否排队严重
- 长上下文是否拖慢整体服务

如果这些问题处理不好，模型即使能力很强，用户也会觉得它慢、贵、不稳。
所以，推理不是训练之后的收尾环节，而是能力进入产品时的第一道现实约束。

---

## 2. prefill 和 decode 是两段性质不同的计算

一次请求进入系统后，模型通常先处理输入上下文，再逐步生成输出。
这两段过程分别叫 `prefill` 和 `decode`。

`prefill` 像是把用户给的整段材料读进来。
模型需要处理 prompt、历史对话、检索材料、系统提示等已有 token，并为它们建立后续生成可用的中间状态。

`decode` 则是逐 token 生成。
每一步生成一个新 token，再把这个 token 放回上下文，继续生成下一步。
它是在线发生的，用户会直接感知节奏。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/63_prefill_decode_and_kv_cache_during_inference_deep_revised__mermaid_01.png)

> 图63-1 prefill 与 decode 的基本时序
> prefill 更像整段读入，decode 更像逐步在线生成。

把这两段分开很重要。
很多“推理慢”的问题，表面看都是等待，实际可能来自不同位置：长 prompt 拖慢 prefill，低带宽和高并发拖慢 decode，排队和调度则可能夹在两者之间。

---

## 3. prefill 的压力来自整段上下文

在 prefill 阶段，模型要一次性处理输入序列。
如果用户上传长文档、保留多轮历史、附带检索结果，输入 token 数就会迅速增加。

这一阶段有几个特点：

- 输入越长，计算和显存压力越大
- 计算可以较充分并行，但总量不小
- 它直接影响 `TTFT`，也就是首 token 延迟
- 长短请求混在一起时，会影响服务调度

从注意力机制的直觉看，输入序列越长，token 之间需要建立的关系越多。
传统全注意力在 prefill 时会带来接近：

$$
O(n^2)
$$

的关系计算压力，其中 `n` 是输入长度。
具体实现会通过 FlashAttention 等方法降低访存和提升效率，但长上下文仍然会显著推高 prefill 成本。

---

![插图 I-23 prefill : decode 的用户体感图]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_23.png)

> 插图 I-23 prefill : decode 的用户体感图

## 4. decode 的压力来自逐步生成

decode 阶段和 prefill 很不一样。
它每次只生成一个新 token，但生成过程是串行推进的：前一个 token 没出来，后一个 token 就没有上下文基础。

这带来几类系统压力：

- 每一步都要尽量短
- 每一步都要访问历史上下文的缓存
- 用户会直接感知输出间隔
- 并发越高，调度越容易影响节奏

所以 decode 更像低延迟在线服务，而不是一次性批量计算。
一个系统可以在离线评测中表现不错，却在 decode 节奏上让用户觉得卡顿。

这里常用两个指标：

- `TTFT`：time to first token，用户等到第一个 token 的时间
- `TPS`：tokens per second，后续输出 token 的速度

`TTFT` 影响“系统有没有反应”的感受，`TPS` 影响“回答是否顺滑”的感受。
对聊天产品来说，两者都很要紧。

---

## 5. KV cache 用显存换掉大量重复计算

Transformer 的注意力计算会产生 Query、Key、Value。
在自回归生成中，新 token 需要关注前面已有 token。
如果每生成一步都把全部历史重新算一遍，成本会迅速失控。

`KV cache` 的思路很直接：
在 prefill 和前面 decode 步骤中已经得到的 Key、Value 先缓存下来。
后续生成新 token 时，只为新 token 计算新的 Query、Key、Value，再让它的 Query 去访问缓存中的历史 Key、Value。

这样做不会让模型少看历史，而是减少重复计算。
它把大量“重算历史”的成本，换成了“保存并读取历史缓存”的成本。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/63_prefill_decode_and_kv_cache_during_inference_deep_revised__mermaid_02.png)

> 图63-2 KV cache 的基本工作方式
> 它不是让模型少理解上下文，而是避免每生成一个 token 就把已算过的历史再算一遍。

代价也清楚：缓存需要显存。
模型层数越多、隐藏维度越大、上下文越长、并发请求越多，KV cache 的占用就越高。
很多在线系统的瓶颈，不在模型权重本身，而在缓存、带宽和调度。

---

## 6. 用户感到慢，往往是多个环节叠加出来的

用户不会区分 prefill、decode、排队和网络。
他只会感到：系统是不是迟迟没有反应，回答是不是一顿一顿。

从系统侧拆开，延迟通常来自几类来源：

- 请求排队：并发过高或资源不足
- prefill：输入上下文过长
- decode：逐 token 生成速度低
- KV cache：缓存访问和显存占用带来压力
- 网络与前端：流式返回、渲染和连接质量影响体感

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/63_prefill_decode_and_kv_cache_during_inference_deep_revised__mermaid_03.png)

> 图63-3 用户体感延迟主要在推理系统层形成，而不是训练层

这也是为什么“模型聪明”和“产品好用”不能直接画等号。
同一个模型，在不同推理服务、不同上下文策略、不同缓存管理和不同批处理方式下，用户感受可能差很多。

---

## 7. 长上下文会把缓存、带宽和调度压力一起放大

长上下文让模型能读更多材料，也让推理系统承受更多压力。
输入越长，prefill 越重；上下文越长，KV cache 越大；生成越长，decode 要访问的历史越多。

长上下文带来的问题不只是一条请求变慢。
在多用户服务里，它还会影响资源调度：

- 长请求占用更多显存
- 长短请求混跑会造成等待不均
- 缓存碎片会降低资源利用率
- 大上下文会挤压可服务并发

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/63_prefill_decode_and_kv_cache_during_inference_deep_revised__mermaid_04.png)

> 图63-4 长上下文放大的，主要是缓存与带宽压力

因此，长上下文不是单纯的能力参数。
它会改变成本结构、延迟结构和产品策略。
很多产品需要限制上下文长度、压缩历史、做检索筛选或把长任务拆分执行，原因就在这里。

---

## 8. 推理优化是在吞吐、延迟和成本之间找平衡

推理优化不是单一技巧，而是一组取舍。
系统既要让单个用户等待更短，也要让总体吞吐更高，还要控制显存和算力成本。

常见手段包括：

- 连续批处理：把不同请求动态合批，提高硬件利用率
- paged attention：用更像分页内存的方式管理 KV cache，减少浪费
- FlashAttention：降低 attention 计算中的访存开销
- speculative decoding：用较小模型或草稿路径先猜 token，再让主模型验证
- 量化与蒸馏：降低权重和计算成本
- prompt caching：复用系统提示或固定前缀的计算结果

这些方法解决的问题不同。
有的改善 prefill，有的改善 decode，有的提升并发，有的降低成本。
工程团队需要根据产品目标选择组合：聊天助手、代码补全、长文档问答、批量生成服务，对延迟和吞吐的偏好都不一样。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/63_prefill_decode_and_kv_cache_during_inference_deep_revised__mermaid_05.png)

> 图63-5 同一个模型会被不同推理系统放大成差异很大的用户体验

---

## 9. 推理层决定“会答”能否变成“答得起”

商业系统里，常见瓶颈不是模型不会回答，而是回答成本太高、等待太久、并发太低。

这类问题会直接影响产品形态：

- 首 token 太慢，用户会以为系统失去响应
- TPS 太低，长回答会显得拖沓
- KV cache 太重，并发能力会下降
- 长上下文请求过多，整体队列会抖动
- 成本过高，产品定价和使用频率都会受限

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/63_prefill_decode_and_kv_cache_during_inference_deep_revised__mermaid_06.png)

> 图63-6 商业系统常见痛点，不是不会答，而是“会答但答不起”

这就是推理系统的价值。
它不是把模型跑起来这么简单，而是在现实约束里决定模型能力能否被持续使用。
进入第64章之后，多模态会进一步放大这类问题：图像、音频、视频都会增加输入处理、缓存、带宽和评测复杂度。

---

## 10. 本章小结

1. 训练和后训练决定模型能力与行为，推理系统决定这些能力如何交付。
2. `prefill` 负责处理已有上下文，主要影响首 token 延迟。
3. `decode` 负责逐 token 生成，主要影响流式输出节奏。
4. `KV cache` 通过保存历史 Key、Value，减少自回归生成中的重复计算。
5. KV cache 用显存和带宽换速度，长上下文和高并发会迅速放大代价。
6. `TTFT` 和 `TPS` 是理解用户体感的两个基础指标。
7. 连续批处理、paged attention、FlashAttention、推测解码和 prompt caching 都是在不同位置改善推理效率。
8. 推理工程决定模型从“会答”走向“答得起、答得快、答得稳”。

核心判断：

> 大模型产品不是把训练好的权重接到接口上就结束；推理系统要在延迟、吞吐、显存和成本之间持续取舍，才能把模型能力变成可用体验。
