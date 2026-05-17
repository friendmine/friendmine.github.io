---
layout: book_chapter
title: "第91章 Harness 与 Agent 的区别"
description: "Agent 负责推进任务，Harness 负责组织这次推进发生在什么上下文、什么权限、什么流程、什么控制边界和什么交付结构里。"
book_active: true
permalink: /book/chapters/091-difference-between-harness-and-agent/
book_kind: chapter
chapter_number: 91
chapter_slug: difference-between-harness-and-agent
book_volume: volume_3
prev_url: /book/chapters/090-five-layer-structure-of-harness/
prev_title: "第90章 Harness 的五层结构"
next_url: /book/chapters/092-harness-forms-across-scenarios/
next_title: "第92章 不同场景下的 Harness 形态"
---
# 第91章 Harness 与 Agent 的区别

## 先说结论

Agent 负责推进任务，Harness 负责组织这次推进发生在什么上下文、什么权限、什么流程、什么控制边界和什么交付结构里。

Agent 更像执行智能。
Harness 更像组织智能。

![插图 第91章 执行智能 vs 组织智能对照图]({{ site.baseurl }}/ai_ecosystem_book/images/chapter_091_difference_between_harness_and_agent.png)

> 插图 第91章 执行智能 vs 组织智能对照图

第90章讲了 Harness 的五层结构：界面、上下文、动作、控制和流程。
本章进一步把 Harness 和 Agent 拆开：如果两者混在一起，团队会持续在错误层面上用力，把本该由系统结构承担的问题误塞进模型和 prompt。

---

## 1. 两个概念容易混淆，是因为用户看到的是一个整体

从用户视角看，面前通常只有一个产品：
输入目标，系统开始工作，最后给出结果。

在这种体验里，Agent 和 Harness 很容易被看成同一个东西。

但工程上，两者承担的职责明显不同。

Agent 负责让任务往前推进：

- 理解目标
- 做局部规划
- 选择下一步
- 调用工具
- 根据反馈继续行动

Harness 负责让这次推进可被组织承接：

- 上下文如何装配
- 动作如何被授权
- 过程如何被看见
- 高风险节点如何审批
- 结果如何变成 artifact
- 失败时如何接管和回退

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_01.png)

> 图91-1 “智能内核”与“工作框架”的对照图
> Agent 更像把任务往前推的智能内核，Harness 更像决定这套推进过程怎样被组织、约束、看见和交付的工作框架。两者共同作用，系统才会从会动变成可用。

概念不拆开，工程努力就会被错误归因带偏。
本该补审批和回退，团队却去写更长的系统提示；
本该补目标分解和工具选择，团队却去加更多界面配置。

---

## 2. Agent 回答“怎么推进”，Harness 回答“在什么制度里推进”

如果用一句话区分两者，可以这样说：

- Agent 决定怎样把任务推进下去
- Harness 决定这套推进过程以什么规则和结构运行

Agent 是行动内核。
它关心的是：下一步怎么做，哪个工具合适，遇到反馈后如何调整。

Harness 是制度环境。
它关心的是：系统看到了什么，能不能做这个动作，谁能批准，过程如何记录，结果怎样交付。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_02.png)

> 图91-2 Agent 与 Harness 各自回答什么问题
> Agent 更像“怎么推进”的执行智能，Harness 更像“在什么制度里推进”的组织结构。只有两层被清楚区分，工程边界才不会长期混线。

也可以把它理解成两种不同层次的智能。
Agent 决定事情会不会往前走。
Harness 决定事情往前走时，会不会被团队接受、被流程承接、被责任体系容纳。

这一区分不是术语洁癖。
它直接决定系统出了问题时，团队知道该修哪一层。

---

## 3. 把 Harness 问题错当 Agent 问题，是常见工程误判

很多系统表现不好，表面上像 Agent 不够聪明。
实际短板却在 Harness。

例如：

- 系统漏掉审批节点
- 权限边界混乱
- 上下文长期装配不稳定
- 工具返回没有标准化
- 过程不可回放
- 结果无法沉淀为 artifact
- 人类无法在关键时刻接管

这些问题并不是靠更聪明的 Agent 就能稳定解决。

如果审批缺失，不能指望模型每次都自觉停下来。
如果权限边界混乱，不能指望 prompt 每次都提醒它别越界。
如果 artifact 没有生命周期，不能指望模型一句“已完成”就完成交付。

这些都属于 Harness 要承担的结构责任。

凡是系统希望“每次都别忘”的东西，通常不应该只写在语言层。
如果某条规则一旦忘掉就会造成权限事故、流程事故或交付事故，它就应该进入 Harness，成为稳定结构。

---

## 4. 把 Agent 问题错当 Harness 问题，也同样危险

反过来，Harness 也不能替代 Agent。

如果系统已经有完整流程、权限、日志和界面，却仍然不会把任务往前推进，问题可能在 Agent。

典型表现包括：

- 不会分解任务
- 不会选择工具
- 不会根据反馈改计划
- 不会识别目标变化
- 不会处理局部冲突
- 不会从错误结果中恢复

这时继续增加界面、审批和配置，不一定能解决问题。
因为短板在执行智能。

Harness 可以提供边界、材料和制度。
但它不能凭空替代“下一步怎么做”的智能判断。

所以，Agent 和 Harness 不是替代关系。
它们是两个不同层次的问题。
一个负责推进，一个负责承接。

---

## 5. 没有 Harness，Agent 很容易变成危险的自由执行器

一个未被 Harness 约束的 Agent，可能已经很聪明，也可能已经会调用很多工具。
但只要缺少上下文装配、权限治理、接管入口和过程记录，它仍然很难进入生产。

原因不是它不会做事，而是它做事的方式不可组织。

它可能：

- 目标理解漂移
- 关键约束被漏掉
- 权限边界模糊
- 高风险动作直接执行
- 错误难以责任还原
- 结果难以沉淀
- 失败无法回放

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_03.png)

> 图91-3 为什么缺少 Harness 的 Agent 难进入生产环境
> 很多系统的问题不是不会做，而是做的方式不可组织。Harness 补的不是装饰，而是组织对行动的承接能力。

缺少 Harness 的系统，短期可能显得灵活、自由、能力很满。
但它越自由，组织越难判断它会在哪一步越界。
它越能行动，责任边界就越需要被显式补出来。

很多产品不是卡在能力不够，而是卡在能力扩大后缺少承接结构。

---

## 6. 没有 Agent，Harness 也只是一套精致空框架

另一面也同样成立。

如果系统只有流程、权限、日志和界面，却没有足够强的执行智能，它也只是一个精致外壳。

它也许能：

- 接收请求
- 展示状态
- 记录审批
- 保存结果
- 管理权限

但它未必能：

- 理解复杂目标
- 做出可行计划
- 选择合适工具
- 根据反馈调整
- 把任务推进到完成

这就像一辆车有车身、方向盘、刹车和仪表盘，却没有足够有效的发动机。

所以，成熟系统不是“只要 Agent”或“只要 Harness”。
而是执行智能和组织智能同时成立。

---

## 7. 失败定位要先判断：是执行智能问题，还是工作框架问题

很多无效争论，来自一个错误问题：
Agent 更重要，还是 Harness 更重要？

更有用的问题是：
系统失败时，问题究竟出在哪一层。

如果系统不会拆任务、不会选工具、不会根据反馈调整，问题多半在 Agent。

如果系统漏上下文、权限混乱、无法回放、结果无法交付，问题多半在 Harness。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_04.png)

> 图91-4 失败定位：执行智能问题还是工作框架问题
> 这张图把无效争论压成可定位的问题判断。很多团队浪费时间，不是因为没有努力，而是没有先分清责任层。

这类定位非常实际。

如果是 Agent 问题，可能要换模型、调规划策略、改工具选择逻辑、补任务分解能力。
如果是 Harness 问题，可能要改上下文层、工具协议、权限系统、审批节点、trace 和 artifact 生命周期。

定位错了，修复就会越修越乱。

---

## 8. “所有逻辑塞进 prompt”是混淆两层后的典型反模式

很多系统早期都会走过一条捷径：
把工具说明、权限策略、流程要求、输出格式、异常处理，全部塞进一个巨大的系统提示词或规划提示词里。

短期看，这很快。
长期看，代价很高：

- 规则难维护
- 边界难解释
- 改一处容易牵连全局
- 高风险规则容易被上下文稀释
- 事故后很难判断责任层

这背后正是没有把 Harness 和 Agent 分开。

合理做法是把稳定规则从 prompt 里提升出来。

- 权限应进入权限系统
- 审批应进入工作流
- 工具契约应进入 Action Layer
- 交付结构应进入 artifact 管理
- 可观察性应进入 trace 和日志
- 高风险中止条件应进入 Control Layer

Prompt 可以表达任务意图和当前策略。
但它不应该承担全部制度责任。

---

## 9. 竞争重心会从单点 Agent 技巧转向 Harness 设计

随着基础模型平台化，越来越多团队都能调用接近的底层能力。
在这种条件下，仅仅拥有一个不错的 Agent，不一定能形成长期优势。

更难复制的，往往是围绕场景打磨出来的 Harness。

例如：

- 哪些上下文应在什么时候出现
- 哪些工具结果必须结构化返回
- 哪些动作必须审批
- 哪类 artifact 更适合团队继续工作
- 哪种回放结构最有助于定位事故
- 哪套评测样本能发现关键退化

这些不是模型参数自动长出来的。
它们来自长期产品设计、系统工程和场景理解。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_05.png)

> 图91-5 竞争重心为什么会从 Agent 设计偏向 Harness 设计
> 当执行智能越来越平台化，难复制的部分会更集中在工作结构和治理结构上。产品壁垒因此自然向 Harness 收缩。

从商业上看，Harness 往往比单点 Agent 技巧更容易形成壁垒。
同一类模型能力可以买到。
但一套能稳定承接具体工作流、权限结构、交付物和失败复盘的系统，不容易复制。

---

## 10. 第91章到第92章：职责拆开以后，形态会随场景变化

本章把 Agent 和 Harness 的职责拆开。
下一章要继续说明：
Harness 没有统一长相。

因为不同场景里的任务对象、动作空间、风险结构和交付物不同，Harness 的形态也会不同。

编程场景需要文件、diff、测试和回滚。
企业流程需要角色、审批、单据和审计。
研究场景需要证据链、引用、笔记和不确定性管理。
工业和机器人场景需要实时状态、仿真、控制台和安全停机。

这说明 Harness 不是一个抽象口号。
它会随着真实任务的承重结构变化而变化。

---

## 11. 容易误判的地方

### 误解一：Harness 只是 Agent 的包装层

不是。
Harness 是组织、约束、观察、接管和交付行动的系统层。

### 误解二：只要 Agent 足够强，Harness 的价值就会下降

恰恰相反。
行动能力越强，越需要更强的框架承接风险、权限、流程和交付。

### 误解三：两者只是不同团队的命名习惯

名称可以不同，但职责差异真实存在。
混淆它们会带来直接工程代价。

### 误解四：流程、权限和交付都可以写进 prompt

高风险、长期稳定、需要审计的规则不应只依赖语言提示。
它们应该进入 Harness 的稳定结构。

### 误解五：Harness 越厚，Agent 越不重要

Harness 不能替代执行智能。
成熟系统需要两者同时成立。

---

## 延伸阅读建议

- 推荐起点：把 intelligent agent、orchestration layer、workflow engine、control plane 分开看，会更容易理解职责边界。
- 工程延伸：继续追 context engineering、tool protocol、permission boundary、artifact lifecycle、workflow orchestration、debug trace。
- 检索关键词：`orchestration layer`、`reasoning engine`、`control boundary`、`workflow orchestration`、`system decomposition`、`artifact lifecycle`。

---

## 12. 本章小结

1. Agent 负责推进任务，Harness 负责为推进提供上下文、权限、流程、控制和交付秩序。
2. 没有 Harness，Agent 难以进入生产；没有 Agent，Harness 也会沦为空框架。
3. 两者不是谁更重要，而是分别解决执行智能和组织智能的问题。
4. 常见工程错误，是把本该属于 Harness 的长期规则和控制责任塞进 prompt。
5. 随着基础模型平台化，长期产品壁垒会越来越集中在 Harness 的工作结构和治理结构上。

核心判断：

> Agent 决定系统会不会做事，Harness 决定这套做事方式能不能被看见、被接管、被交付，也能不能被团队和责任体系长期接受。
