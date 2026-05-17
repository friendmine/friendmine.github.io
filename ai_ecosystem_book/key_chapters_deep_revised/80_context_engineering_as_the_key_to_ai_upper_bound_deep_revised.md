---
layout: book_chapter
title: "第80章 Context Engineering：决定 AI 上限的关键工程"
description: "Context Engineering 处理的不是窗口能装多少字，而是模型在每一轮任务里看到什么、按什么顺序看到、依据什么继续行动。"
book_active: true
permalink: /book/chapters/080-context-engineering-as-the-key-to-ai-upper-bound/
book_kind: chapter
chapter_number: 80
chapter_slug: context-engineering-as-the-key-to-ai-upper-bound
book_volume: volume_3
prev_url: /book/chapters/079-why-more-tools-are-not-always-better/
prev_title: "第79章 工具不是越多越好"
next_url: /book/chapters/081-structure-of-context/
next_title: "第81章 Context 的组成结构"
---
# 第80章 Context Engineering：决定 AI 上限的关键工程

## 先说结论

Context Engineering 处理的，不是“给模型多塞一点材料”。
它处理的是：模型在这一轮任务里能看到什么、看不到什么、以什么顺序看到、哪些信息被压缩、哪些状态被刷新、哪些证据被保留。

第79章讲到，工具质量决定 Agent 的行动空间质量。
但工具能否被正确选择和使用，还取决于模型眼前摆着怎样的信息环境。
如果任务目标、工具说明、权限边界、历史状态和检索材料组织混乱，再好的工具也可能被错用。

所以，Context Engineering 是把模型能力兑现成系统能力的装配工程。
它不直接扩大模型参数，却会影响模型在真实任务里的表现上限。

---

![插图 I-09 Context Engineering 现场图]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_09.png)

> 插图 I-09 Context Engineering 现场图

## 1. 上下文从输入问题变成系统工程问题

早期讨论 context，人们常把它理解成窗口长度：
能装多少 token，能塞多少资料，能不能一次放进整份文档。

但进入真实系统后，问题会变得更具体。
窗口长度只是容量，系统质量取决于装进去的内容是否服务当前任务。

模型在每一轮里只基于可见信息行动。
任务状态没有给，它就不知道当前进度。
权限边界没有给，它就可能误判动作风险。
检索材料给错了，它就会基于错误资料继续推理。
历史信息没有压缩，它就会被旧细节拖住。

因此，上下文不是边角配置，而是系统工程的一部分。
它决定模型当前所处的信息环境，也决定模型能否把已有能力用到正确位置。

---

## 2. Context 不是窗口长度，而是模型当前可见世界

把 Context 理解成“窗口里的长文本”，会让工程判断变形。
更准确的说法是：Context 是模型当前可见世界的总和。

它可能包括：

- 系统指令与行为边界
- 用户当前请求
- 当前任务状态
- 历史摘要
- 检索材料
- 工具说明与权限
- 当前工作空间和环境信号
- 输出格式与验收要求

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/80_context_engineering_as_the_key_to_ai_upper_bound_deep_revised__mermaid_01.png)

> 图80-1 Context 是模型当前可见世界的总和
> 这张图把 context 从窗口容量问题，拉回到信息环境设计问题。

这个定义很实用。
它提醒团队不要只问“窗口够不够大”，还要问：

- 该看的信息是否进入了窗口
- 不该看的噪声是否被排除
- 信息顺序是否服务判断
- 过期状态是否被替换
- 冲突信息是否有优先级

---

## 3. 上下文质量取决于相关性、充分性、新鲜度和冲突控制

上下文出问题，通常不只是“资料不够”。
它可能来自四类质量缺陷。

第一是相关性不足。
窗口里有很多内容，但和当前任务关系弱，模型被迫在噪声中寻找信号。

第二是充分性不足。
当前任务需要的约束、背景、数据或工具结果缺位，模型只能猜。

第三是新鲜度不足。
任务已经推进，窗口里还保留旧目标、旧状态或旧结论。

第四是冲突控制不足。
不同来源给出不一致信息，系统没有说明该相信哪一层。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/80_context_engineering_as_the_key_to_ai_upper_bound_deep_revised__mermaid_02.png)

> 图80-2 模型看见什么，会显著影响它能做什么

Context Engineering 的工作，就是持续处理这些质量问题。
它不是把窗口填满，而是在有限窗口里组织更适合当前行动的信息环境。

---

## 4. Prompt Engineering 和 Context Engineering 处理的不是同一层

Prompt Engineering 关注用户或系统如何表达任务。
它处理的是一句话、一段指令或一组输出约束怎么写得更清楚。

Context Engineering 处理的是整套信息环境如何装配。
它关心：

- 什么信息进入窗口
- 什么信息需要压缩
- 什么状态要持续保留
- 什么材料只在当前阶段出现
- 信息冲突时谁优先
- 工具结果如何回流给模型
- 任务推进后上下文如何更新

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/80_context_engineering_as_the_key_to_ai_upper_bound_deep_revised__mermaid_03.png)

> 图80-3 Prompt Engineering 和 Context Engineering 处理的不是同一层问题

Prompt 可以把任务说清楚。
Context 则要让模型在执行任务时看见该看的世界。
当任务变长、工具变多、状态变复杂，工程重心就会从“最后一句怎么写”转向“这一轮该让模型看到什么”。

---

## 5. 模型上限不等于系统上限

同一个模型，放进不同系统里，表现可能差异很大。
差异未必来自模型本体，也可能来自上下文装配。

一个系统即使用了高能力模型，如果上下文里混着旧目标、重复材料、低可信来源和不完整工具结果，表现仍然会发飘。
另一个系统使用同类模型，但能把任务状态、证据、工具结果和下一步约束组织清楚，表现会明显更稳。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/80_context_engineering_as_the_key_to_ai_upper_bound_deep_revised__mermaid_04.png)

> 图80-4 模型上限不等于系统上限
> 系统上限还取决于能否把模型放进正确的信息环境。

这就是为什么很多团队升级模型后，仍然会遇到同类问题：
模型更会推理了，但它看到的材料还是乱的；
模型更会调用工具了，但工具说明、权限和历史结果没有组织好；
模型更会写了，但任务状态没有被持续刷新。

模型能力是基础。
上下文工程决定这些能力在具体系统中能兑现多少。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/80_context_engineering_as_the_key_to_ai_upper_bound_deep_revised__mermaid_05.png)

> 图80-5 很多系统瓶颈不在模型层，而在上下文层

---

## 6. 成熟上下文是动态系统，不是一次性拼接

很多上下文失败，来自一次性拼接思路。
系统把用户输入、历史记录、检索材料和工具说明一起塞进窗口，然后期待模型自己分清主次。

真实任务不是静态的。
任务会推进，状态会变化，某些材料会失效，新的工具结果会产生，用户目标也可能修正。

成熟上下文需要动态更新：

- 新任务状态要进入窗口
- 旧状态要退出或降级
- 长历史要压缩成摘要
- 工具结果要结构化回流
- 证据来源要保留
- 临时材料完成使命后要移出

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/80_context_engineering_as_the_key_to_ai_upper_bound_deep_revised__mermaid_06.png)

> 图80-6 成熟上下文往往是动态系统，而不是一次性拼接出来的长输入

动态上下文的目的，是让模型始终看见“当前局面”。
如果旧信息还占着高优先级位置，新状态却没有进入窗口，模型就会在过期地面上继续行动。

---

## 7. 上下文装配是一条工程流水线

成熟系统不会把所有材料一股脑放进窗口。
它通常会有一条装配流水线。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/80_context_engineering_as_the_key_to_ai_upper_bound_deep_revised__mermaid_07.png)

> 图80-7 Context 装配的基本工程流程
> 上下文工程是在持续装配一个足以支持本轮行动的可见世界。

这条流水线通常包括：

- 收集：从用户请求、任务状态、工具结果、文档和知识库取材料
- 筛选：去掉和当前任务关系弱的内容
- 排序：把高优先级信息放在更容易被利用的位置
- 压缩：把长历史、长文档和重复内容变成可用摘要
- 标注：保留来源、时间、可信度和适用范围
- 装配：按任务需要组织成模型可读的上下文
- 回写：把模型输出、工具结果和用户反馈更新回任务状态

这条链里任何一环失真，模型都可能表现异常。
因此，Context Engineering 的难点不是“有没有材料”，而是材料如何被持续加工、选择、排序、压缩和更新。

---

## 8. Context Engineering 需要评测，而不是只靠感觉

很多团队做上下文优化时，只看最后回答是否“更像样”。
这不够。

上下文工程需要拆开评测：

- 检索是否命中需要的材料
- 压缩是否保住核心约束
- 装配顺序是否支持判断
- 旧信息是否及时退出
- 冲突信息是否有优先级
- 工具结果是否被正确引用
- 上下文变化是否提升任务完成率

如果不拆开评测，团队很难知道问题发生在哪一层。
是资料没召回，还是召回了但排序错了？
是摘要丢了约束，还是模型没有用到？
是上下文太少，还是噪声太多？

Context Engineering 进入生产后，不能停留在“感觉更好”。
它需要和任务完成率、错误率、人工接管率、引用准确率、工具误用率一起评估。

---

## 9. 这会成为 AI 产品的长期壁垒

Context Engineering 看起来像中间层，实际很难快速复制。

因为它不是一个模板，而是一套组织能力：

- 信息治理能力
- 文档和数据清洗能力
- 检索与排序能力
- 状态管理能力
- 历史压缩能力
- 工具结果装配能力
- 评测和故障定位能力

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/80_context_engineering_as_the_key_to_ai_upper_bound_deep_revised__mermaid_08.png)

> 图80-8 AI 产品的壁垒，往往长在上下文工程而不是表面 Prompt 模板

Prompt 模板可以被复制，模型也可以被采购。
但一个组织如何清洗知识、维护状态、处理冲突、更新资料、追踪来源、压缩历史和定位上下文事故，需要长期积累。

能长期站住的 AI 产品，通常不是只会写一句提示词，而是能持续组织一个可用的信息环境。

---

## 10. 容易误判的地方

### 误解一：上下文就是窗口长度问题

窗口长度只是容量。Context Engineering 处理的是信息选择、顺序、压缩、状态更新和冲突控制。

### 误解二：把材料都塞进去，模型就会自己判断

过多无关材料会增加噪声。上下文需要筛选和排序，而不是堆叠。

### 误解三：模型表现不好，主要说明模型不够大

很多系统问题来自上下文装配失败。先检查模型看见了什么，再判断是否需要换模型。

### 误解四：Context Engineering 只是 Prompt Engineering 的换名

Prompt 是表达层。Context Engineering 处理的是模型当前可见世界的整体装配。

---

## 延伸阅读建议

- 推荐起点：可以顺着 RAG orchestration、memory system、agent runtime、context compression 这些系统实现主题继续看。
- 继续延伸：如果你关心上下文质量如何度量，可以继续追 retrieval evaluation、context relevance、citation accuracy、state tracking。
- 检索关键词：`context engineering`、`RAG orchestration`、`memory system`、`context compression`、`agent runtime`、`state tracking`。

---

## 11. 本章小结

1. Context Engineering 不是窗口长度问题，而是信息环境装配问题。
2. Context 是模型当前可见世界的总和。
3. 上下文质量取决于相关性、充分性、新鲜度和冲突控制。
4. Prompt Engineering 处理表达层，Context Engineering 处理整套可见世界。
5. 成熟上下文是动态系统，需要持续更新、压缩、排序和回写。
6. Context Engineering 需要评测，不能只凭主观感觉。

核心判断：

> Context Engineering 决定的不是模型能装多少字，而是模型在每一轮任务里依据怎样的信息世界继续行动。
