---
layout: book_chapter
title: "第89章 Harness：给 Agent 套上工作框架"
description: "如果 Agent 是会行动的智能内核，Harness 就是把这种行动放进上下文、权限、工具、流程、观测、接管和交付结构里的工作框架。"
book_active: true
permalink: /book/chapters/089-harness-as-the-work-framework-for-agents/
book_kind: chapter
chapter_number: 89
chapter_slug: harness-as-the-work-framework-for-agents
book_volume: volume_3
prev_url: /book/chapters/088-why-humans-must-stay-in-the-loop/
prev_title: "第88章 人为什么需要在环"
next_url: /book/chapters/090-five-layer-structure-of-harness/
next_title: "第90章 Harness 的五层结构"
---
# 第89章 Harness：给 Agent 套上工作框架

## 先说结论

如果 Agent 是会行动的智能内核，Harness 就是把这种行动放进上下文、权限、工具、流程、观测、接管和交付结构里的工作框架。

很多 demo 的问题不是模型不会做，而是系统没有给它一套能被团队信任、被流程约束、被现实长期接住的工作秩序。

![插图 I-11 Harness 作为工作框架]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_11.png)

> 插图 I-11 Harness 作为工作框架

第88章讨论人在环，说明高风险动作、不可逆后果和价值判断密集的节点需要被人看见、接管和负责。
本章要把这个思想推进一步：当 Agent 开始能行动时，系统必须有一层工作框架，把人的介入、上下文、权限、动作、状态和交付组织起来。

---

## 1. 为什么只有 Agent 还不够

一个会规划、会调工具、会持续执行的 Agent，已经比普通问答模型前进了一大步。
但只要它想进入真实工作流，问题就会立刻从“能不能做”变成“怎样被组织起来”。

系统必须回答：

- 用户从哪里进入任务
- 任务目标如何被结构化
- 上下文如何被装配
- 工具如何被暴露和授权
- 权限边界如何设定
- 状态如何保存
- 中途出错谁来接手
- 结果如何沉淀成可继续使用的 artifact
- 整个过程能否被回放和审计

这些问题不属于 Agent 狭义上的执行智能，却直接决定 Agent 能不能进入现实。

没有这些结构，再会行动的 Agent 也只是一个四处试探边界的内核。
它也许能完成一次 demo，却很难被组织长期托付。

这也是很多团队做完 demo 后很快遇到的位置。
他们会发现，真正卡住的不再是“模型会不会做”，而是“系统怎么承接这次做”。

---

## 2. Harness 不是包装层，而是工作秩序层

不少人刚接触 Harness，会把它理解成界面壳、产品外壳，或者给 Agent 加一层工具栏。
这种理解太浅。

Harness 做的，是围绕 Agent 建立一整套工作秩序。

它要决定：

- 任务如何进入系统
- 模型看见什么上下文
- 工具如何注册、调用和返回
- 动作如何被授权
- 高风险节点如何审批
- 状态如何被记录
- 人如何在关键处介入
- 结果如何被交付和沉淀

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_01.png)

> 图89-1 Harness 的总体结构
> Harness 不是界面外壳，而是把 Agent 放进上下文、工具、权限、控制、流程和交付秩序里的系统骨架。

所以，Harness 不是把 Agent 包起来的塑料壳。
它是把 Agent 放进秩序中的骨架。

组织不只关心“这件事能不能被做出来”。
组织还关心：
这件事是谁触发的，按什么规则做的，失败时怎么停，结果怎样继续流转，责任如何还原。

如果这些要求都压在 Agent 自己身上，系统会在最需要秩序的地方开始失真。

---

## 3. 从聊天界面到工作框架，是 AI 产品的结构跃迁

聊天界面在早期非常有效。
它把模型能力用最简单的方式暴露给用户。

但聊天界面主要解决的是：
输入一句，得到一句。

一旦任务变成长时间、多步骤、带权限、带交付物的协作过程，聊天就不再是全部。

系统需要开始显式拥有：

- 任务状态
- 工作区
- 工具面板
- 权限设置
- 审批节点
- artifact 区域
- 过程记录
- 回放和审计能力

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_02.png)

> 图89-2 从聊天界面到工作框架
> 这种结构跃迁，不是界面更复杂，而是系统开始显式承担状态、治理、交付和协作责任。

这时，产品本体已经不再只是对话。
它开始成为工作框架。

Harness 这个概念，正是为了给这种结构跃迁命名。

---

## 4. Harness 补上的，是 Agent 难以独自承担的组织性能力

Harness 通常在五个方向上发挥决定性作用。

第一，上下文装配。
系统需要在合适时刻，把合适信息送进模型当前工作现场。
这包括规则、历史、状态、检索材料、用户约束和权限过滤后的内容。

第二，工具和权限治理。
不是所有工具都该默认暴露，也不是所有动作都该被无条件执行。
工具越多，权限越需要清楚。

第三，状态和过程记录。
任务进行到哪里，为什么改计划，调用了哪些工具，失败发生在哪一步，都需要被系统性保存。

第四，人在环机制。
审批、接管、回退和人工纠偏，不能只是口号。
它们需要明确入口、状态可见性和责任链。

第五，交付结构。
用户要的通常不是一段好看的解释，而是代码、报告、表格、设计稿、审批结果或可继续流转的 artifact。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_03.png)

> 图89-3 Harness 补上的五类关键结构
> Harness 不是在 Agent 外面多包一层，而是在补那些 Agent 单独无法长期承担的组织性能力。

这些结构不会因为模型更强就自动长出来。
模型强了，可能只是让 Agent 更有能力进入更多步骤。
但越能进入更多步骤，就越需要 Harness 把这些步骤组织成可解释、可接管、可交付的序列。

所以 Harness 的价值不会随着模型升级而下降，反而通常会被放大。

---

## 5. 一个真实 Harness 怎样承接任务

如果把 Harness 写成一条工作链，它通常会这样承接任务：

1. 接收用户目标。
2. 澄清目标和约束。
3. 装配上下文。
4. 暴露必要工具和权限。
5. 让 Agent 规划和执行。
6. 在关键节点审批或接管。
7. 记录 trace 和状态变化。
8. 交付 artifact。
9. 复盘失败与改进点。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_04.png)

> 图89-4 Harness 承接任务的基本流程
> Harness 承接的是工作秩序，而不只是模型调用。它把入口、上下文、权限、执行、接管和交付连成一条链。

这也是为什么 Agent 很难单独形成完整产品。
它可以是执行核心，但很少能自己完成入口、治理、状态、人工接管和结果交付这整条链。

很多“模型产品”在第二阶段停住，就是因为把第一阶段的成功误读成了产品已经成立。
用户被聪明执行打动一次，不代表组织愿意长期把工作压上去。

从试验品走向系统产品的门槛，通常集中在 Harness 这一层。

---

## 6. Harness 让“会行动”变成“可被组织的行动”

Agent 的潜力在于行动。
Harness 的意义在于让这种行动具备四个关键属性。

第一，可组织。
任务不再只是一次模型调用，而是进入工作区、状态、流程和交付结构。

第二，可观察。
团队能看见系统为什么这样做，任务走到哪里，哪里开始偏航。

第三，可约束。
工具、权限、审批、预算、停止条件和回滚路径都能被明确控制。

第四，可交付。
结果不只是文字回复，而是能进入后续工作的 artifact。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_06.png)

> 图89-6 Harness 把行动变成可用系统
> 只有把行动放进组织、观察、约束和交付结构里，系统才会从会做一件事变成能长期被团队使用的东西。

从这个角度看，Harness 做的是把“会行动”翻译成“值得被组织接住”。

技术高光的意义，只有在它能被流程、角色、责任和交付体系吸收时，才会持续。

---

## 7. Harness 会成为未来 AI 产品的重要竞争单位

随着基础模型越来越平台化，单靠“我们底层用哪个模型”很难形成长期壁垒。
用户感知到的差异，会越来越来自系统层。

用户会关心：

- 它记不记得项目状态
- 它能不能稳定调工具
- 它能不能进入团队流程
- 它有没有权限边界
- 它能不能被回放和审计
- 它交付的结果能不能继续工作

这些大多属于 Harness 的问题域。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_05.png)

> 图89-5 为什么竞争会从模型名称转向 Harness
> 当模型能力逐渐可获得，用户感知到的长期差异会更多落在系统层。谁能把能力稳定组织成工作秩序，谁就更容易形成真实产品壁垒。

可以拿两个代码助手做对照。
一个产品只是把强模型接进聊天框，能给出不错建议；
另一个产品能读仓库、接 issue、保留状态、受权限约束、在失败时回退、交付可继续工作的 diff。

表面上两者都在调用同类模型。
但后者卖给团队的，已经不是模型回答，而是一套能被组织接住的工作结构。

---

## 8. Harness 不是传统工作流的简单复刻

Harness 和传统工作流、控制台、审批流、BPM 并不是毫无关系。
但它也不是简单复刻。

新的难点在于：
这套流程中间出现了一个会解释、会生成、会调用工具、会根据反馈行动的智能内核。

这带来新的工程要求：

- 上下文要动态装配
- 工具边界要被模型理解
- 权限要能约束模型行动半径
- 过程要能被 trace
- 人要能在中途接管
- 结果要能变成可维护 artifact
- 失败要能回放和复盘

传统工作流更多处理确定性步骤。
Harness 要处理的是智能行动与组织流程的结合。

这也是它复杂的地方。
它既不能只当模型壳，也不能只当老式流程引擎。

---

## 9. 工程现实：很多产品卡在把 demo 变成工作系统

现实里，第一阶段的演示通常推进很快。
一个会说话、能连几个工具的单机 Agent，很容易做出顺畅 demo。

第二阶段更难：
把它推进到不能长期漂移、不能承受大面积失误、必须被团队长期依赖的真实工作流里。

到了这一步，团队会发现缺的不只是模型能力，而是完整 Harness 骨架。

需要补上的，通常是这些并不性感、却决定系统能否长期工作的东西：

- 能跨越数天持续保存的状态系统
- 多层审批、熔断和权限拦截
- 能承接长流程的任务管理结构
- 字段级、动作级的权限控制
- 可回放、可定位、可复盘的调试面板
- 能稳定沉淀并继续流转的交付产物
- 可评测、可回归的失败样本库

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_07.png)

> 图89-7 AI 产品第二阶段的真实补课清单
> 很多团队的问题不是不知道怎么做 demo，而是不知道怎么把 demo 变成工作系统。Harness 之所以重要，正是因为第二阶段缺的大多是它负责的东西。

能把 Harness 做扎实的团队不会太多。
谁能把这套看似沉重、实则关键的工作骨架补齐，谁就更有机会把 AI 从能演示的功能推进成能被长期依赖的系统。

---

## 10. 第89章到第90章：概念之后，需要分层

本章定义了 Harness：它是给 Agent 套上的工作框架。

但只说“工作框架”还不够。
工程落地时，团队还需要知道：

- 界面负责什么
- 上下文负责什么
- 动作负责什么
- 控制负责什么
- 流程负责什么

这就是第90章要讨论的五层结构。

如果不分层，Harness 很容易再次变成一团混杂逻辑：
界面里写流程，prompt 里写权限，工具层处理状态，模型自己判断高风险动作是否放行。

所以第90章会把 Harness 拆成 Interface、Context、Action、Control、Workflow 五层。
这不是为了画图漂亮，而是为了让系统复杂以后仍然知道谁该负责什么。

---

## 11. 容易误判的地方

### 误解一：Harness 只是聊天界面加一点工具栏

不是。
Harness 更接近控制平面和工作骨架，不是皮肤层升级。

### 误解二：只要 Agent 足够强，Harness 可以简化为可有可无

能力越强，行动半径越大，越需要更强的组织和约束结构来承接。

### 误解三：Harness 的价值主要体现在体验层

它的核心价值在系统层，体现在上下文、权限、流程、观测、接管和交付。

### 误解四：Harness 就是传统工作流系统换个 AI 名字

不是。
Harness 要承接一个会生成、会推理、会行动、但也需要被约束和接管的智能内核。

### 误解五：模型升级以后 Harness 会自然消失

不会。
模型越能行动，越需要工作框架来限定边界、记录过程、交付结果和承接责任。

---

## 延伸阅读建议

- 推荐起点：继续看 workflow engine、control plane、policy enforcement、approval system、artifact lifecycle 等平台骨架主题。
- 工程延伸：建议追 context engineering、tool protocol、execution boundary、audit trail、human takeover、workflow orchestration。
- 检索关键词：`workflow engine`、`control plane`、`policy enforcement`、`audit trail`、`execution boundary`、`artifact lifecycle`、`human takeover`。

---

## 12. 本章小结

1. Harness 不是包装层，而是把 Agent 放进现实工作秩序中的工作框架。
2. 它负责上下文装配、工具治理、状态记录、人在环、控制边界和 artifact 交付。
3. Harness 让“会行动”变成“可组织、可观察、可约束、可交付的行动”。
4. 随着模型平台化，Harness 会越来越接近 AI 产品的系统本体。
5. 理解 Harness 之后，下一步是把它拆成第90章的五层结构。

核心判断：

> 模型不是产品，Agent 也还不是产品；更接近产品本体的，是那个能把智能组织进工作秩序的 Harness，因为团队依赖的不是一次聪明表现，而是一套可以长期托付的工作骨架。
