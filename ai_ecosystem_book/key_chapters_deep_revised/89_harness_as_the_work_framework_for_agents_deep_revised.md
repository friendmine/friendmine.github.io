---
layout: book_chapter
title: "第89章 Harness：给 Agent 套上工作框架"
description: "如果 Agent 是会行动的内核，那么 Harness 就是在决定这种行动能不能被团队信任、被流程约束、被现实长期接住的那层骨架。"
book_active: true
permalink: /book/chapters/089-harness-as-the-work-framework-for-agents/
book_kind: chapter
chapter_number: 89
chapter_slug: harness-as-the-work-framework-for-agents
book_volume: volume_3
prev_url: /book/chapters/088-why-humans-must-stay-in-the-loop/
prev_title: "第88章 人为什么必须在环"
next_url: /book/chapters/090-five-layer-structure-of-harness/
next_title: "第90章 Harness 的五层结构"
---
# 第89章 Harness：给 Agent 套上工作框架

## 先说结论

如果 Agent 是会行动的内核，那么 Harness 就是在决定这种行动能不能被团队信任、被流程约束、被现实长期接住的那层骨架。  
很多 demo 的问题从来不是“模型不会做”，而是系统根本没有给它一套可持续工作的秩序。

---

![插图 I-11 Harness 作为工作框架]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_11.png)

> 插图 I-11 Harness 作为工作框架

## 1. 为什么只有 Agent 还远远不够
一个会规划、会调工具、会持续执行的 Agent，当然已经比普通问答模型前进了一大步。  
但只要它想进入真实工作流，问题就会立刻从“能不能做”扩展成“怎样被组织起来”。

用户从哪里进入？  
系统怎样理解当前任务？  
权限如何暴露？  
状态怎样保存？  
中途出了问题谁来接手？  
最后的结果怎样沉淀成真正可用的产物？

这些问题都不属于 Agent 狭义上的执行智能，却直接决定它能不能进入现实。  
没有这些结构，再会行动的 Agent 也只是一个四处试探边界的内核。

这也是很多团队做完 demo 后第一次撞墙的位置。  
他们会发现问题不再是“模型会不会做”，而是“系统怎么承接这次做”。  
谁来给它喂对上下文，谁来限定它能碰哪些工具，谁来记录它中途改过哪些计划，谁来接住最终交付物。  
这些问题一旦没有答案，Agent 越能行动，组织反而越不敢放它进入真实流程。

---

## 2. Harness 不是包装层，而是工作秩序层

不少人第一次接触这个概念，会把 Harness 理解成一个界面壳或者产品外壳。  
这种理解太浅。

Harness 真正做的，是围绕 Agent 建立一整套工作秩序。  
它要决定：

- 任务如何进入系统
- 上下文如何被装配
- 工具如何被注册和授权
- 状态如何被记录
- 人如何在关键处接入
- 最终结果如何被交付和沉淀

所以，Harness 不是把 Agent 包起来的塑料壳，而是把 Agent 放进秩序中的骨架。  
没有这层骨架，系统即使能动，也很难长期稳定地工作。

换句话说，Harness 真正承接的是组织对行动的要求。  
组织从来不只关心“这件事能不能被做出来”，还关心“这件事是谁触发的、按什么规则做的、失败时怎么停、结果怎样继续流转”。  
如果这些要求都还压在 Agent 自己身上，系统很快就会在最需要秩序的地方开始失真。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_01.png)

> 图89-1 Harness 的总体结构  
> 这张图的重点，是把 Harness 从“界面层”重新拉回“秩序层”和“控制层”。

---

## 3. 从聊天界面到工作框架，是 AI 产品真正的结构跃迁

聊天界面之所以在早期如此有效，是因为它把模型能力用最简单的方式暴露给了用户。  
但聊天界面能解决的，主要是“输入一句，得到一句”的交互问题。

一旦任务变成长时间、多步骤、带权限和交付物的协作过程，聊天就不再是全部。  
系统必须开始显式拥有：

- 任务状态
- 工作区
- 工具面板
- 审批节点
- 产物区域
- 回放和审计能力

这时，产品的本体就已经不再只是对话，而是工作框架。  
Harness 这个概念，正是为了给这种结构跃迁命名。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_02.png)

> 图89-2 从聊天界面到工作框架  
> 真正的结构跃迁，不是界面更复杂，而是系统开始显式承担状态、治理、交付和协作责任。

---

## 4. Harness 真正补上的，是 Agent 永远无法独自补上的几类结构

从功能上看，Harness 通常在五个方向上发挥决定性作用。

第一，它补上下文装配。  
系统必须在合适的时刻，把合适的信息送进模型窗口。

第二，它补上工具和权限治理。  
不是所有能力都该默认暴露，也不是所有动作都该被无条件执行。

第三，它补上状态和过程记录。  
任务进行到哪里，为什么改计划，失败发生在哪一步，这些都需要被系统性保存。

第四，它补上人在环机制。  
审批、接管、回退和人工纠偏，都必须有明确入口。

第五，它补上交付。  
用户要的通常不是一段漂亮解释，而是代码、报告、表格、设计稿、审批结果或可继续流转的 artifact。

这五类结构拼起来，才叫工作框架。

而且这五类结构并不会因为模型更强就自动长出来。  
模型强了，可能只是让 Agent 更有能力进入更多步骤；  
但越能进入更多步骤，就越需要 Harness 把这些步骤组织成可解释、可接管、可交付的序列。  
所以 Harness 的价值不会随着模型升级而下降，反而通常会被放大。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_03.png)

> 图89-3 Harness 补上的五类关键结构  
> 这张图把 Harness 的核心价值压得更清楚了。它不是在 Agent 外面多包一层，而是在补那些 Agent 单独无法长期承担的组织性能力。

---

## 5. 一个真实 Harness 往往怎样承接任务

如果把它写成过程，Harness 通常更接近下面这种工作流：

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_04.png)

> 图89-4 Harness 承接任务的基本流程  
> 这条链说明，Harness 真正承接的是“工作秩序”，而不只是模型调用。

这也是为什么 Agent 很难单独形成产品。  
它可以是执行核心，但很少能自己完成入口、治理、状态、人工接管和结果交付这整条链。

很多“模型产品”之所以在第二阶段停住，就是因为它们把第一阶段的成功误读成了产品已经成立。  
用户被聪明的执行打动过一次，不代表组织愿意长期把工作压上去。  
真正从试验品走向系统产品的门槛，通常全都长在 Harness 这一层。

---

## 6. 为什么 Harness 会成为未来 AI 产品的真正竞争单位

随着基础模型越来越平台化，单靠“我们底层用的是哪个模型”很难形成长期壁垒。  
用户最终感知到的差异，越来越来自系统层：

- 它记不记得事情
- 它能不能稳定调工具
- 它能不能进入团队流程
- 它有没有权限边界
- 它能不能被回放和审计
- 它交付的结果能不能继续工作

这些几乎全部属于 Harness 的问题域。  
也就是说，未来很多 AI 产品之间真正拉开差距的，不会是模型名称，而是围绕模型构造出的工作框架。

最容易看清这件事的方式，是拿两个看起来都像“代码助手”的产品做对照。  
一个产品也许只是把强模型接进聊天框，能给出不错的建议；  
另一个产品则能读仓库、接 issue、保留状态、受权限约束、在失败时回退、交付可继续工作的 artifact。  
表面上两者都在“调用同类模型”，但后者卖给团队的其实已经不是模型回答，而是一套能被组织接住的工作结构。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_05.png)

> 图89-5 为什么竞争会从模型名称转向 Harness  
> 当模型能力逐渐可获得，用户感知到的长期差异就会更多落在系统层。谁能把能力稳定地组织成工作秩序，谁就更容易形成真实产品壁垒。

---

## 7. Harness 让“会行动”变成“可被组织的行动”

从系统史的角度看，真正有价值的技术往往不是第一次跑起来的技术，而是第一次能被大规模组织起来的技术。  
电力如此，计算机如此，互联网也是如此。

Agent 的潜力在于行动。  
Harness 的意义在于让这种行动具备四个关键属性：

- 可组织
- 可观察
- 可约束
- 可交付

一旦具备这四个属性，系统就不再只是一个令人惊讶的演示，而开始具备进入团队、进入组织、进入生产流程的资格。

从这个角度看，Harness 真正做的是把“会行动”翻译成“值得被组织接住”。  
技术高光的意义，只有在它能被流程、角色、责任和交付体系吸收时才会持续。  
谁能把这一步做扎实，谁才真的把 Agent 从能力演示带到了现实工作世界。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_06.png)

> 图89-6 Harness 把行动变成可用系统  
> 只有把行动放进组织、观察、约束和交付结构里，系统才会从“会做一件事”变成“能长期被团队使用的东西”。

---

## 8. 工程现实：很多产品真正的地狱第二阶段，都是在大炼 Harness 钢铁时流血的

如果把它放回更长的软件演化脉络里看，Harness 和传统工作流、控制台、审批流、BPM 并不是毫无关系的两套东西。  
真正新的难点不在于“终于接上了长流程”，而在于这套流程中段突然被塞入了一个会解释、会生成、会调用工具、但行为并不天然稳定的智能内核。  
也正因为如此，工业界才会反复遇到同一个现实：做一个会说话、能演示的 Agent 并不难，难的是把它压进一条可审计、可回滚、可长期维护的工作框架里。

现实里，第一阶段的骗经费演示通常快得令人发指。  
一个会说话、能连几个工具的单机 Agent，往往很容易做出漂亮 demo。  
但真正困难的第二阶段，是把它推进到容不得长时间漂移和大面积失误的真实工作流里。到了那一步，团队才会发现自己真正缺的不是模型能力，而是完整的 Harness 骨架。

需要补上的，通常是这些并不性感、却决定系统能否长期工作的东西：

- 能跨越数天持续保存的状态系统
- 多层审批、熔断和权限拦截
- 能承接长流程的任务管理结构
- 细到字段级、动作级的权限控制
- 可回放、可定位、可复盘的调试面板
- 能稳定沉淀并继续流转的交付产物

也正因为如此，真正能把 Harness 做扎实的团队始终是少数。  
谁能把这套看似沉重、实则关键的控制骨架真正补齐，谁才更有机会把 AI 从“能演示的功能”推进成“能被长期依赖的系统”。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/89_harness_as_the_work_framework_for_agents_deep_revised__mermaid_07.png)

> 图89-7 AI 产品第二阶段的真实补课清单  
> 很多团队的问题不是不知道怎么做 demo，而是不知道怎么把 demo 变成工作系统。Harness 之所以重要，正是因为第二阶段缺的几乎全是它负责的东西。

---

## 9. 容易误判的地方

### 误解一：Harness 只是聊天界面加一点工具栏

真正的 Harness 更接近控制平面和工作骨架，不是皮肤层升级。

### 误解二：只要 Agent 足够强，Harness 可以简化为可有可无

能力越强，行动半径越大，越需要更强的组织和约束结构来承接。

### 误解三：Harness 的价值主要体现在体验层

它的核心价值其实在系统层，体现在上下文、权限、流程、观测和交付。

---

## 延伸阅读建议

- 推荐起点：这一章最适合顺着 workflow engine、control plane、policy enforcement、approval system 这些平台骨架继续看。
- 继续延伸：如果你想把 Harness 放回真实软件形态里理解，建议继续追 audit trail、execution boundary、多租户治理。
- 检索关键词：`workflow engine`、`control plane`、`policy enforcement`、`audit trail`、`execution boundary`。

---

## 11. 本章小结

1. Harness 不是包装层，而是把 Agent 放进现实工作秩序中的框架。  
2. 它负责上下文装配、工具治理、状态记录、人在环和交付等关键结构。  
3. Harness 的本体不是界面，而是任务入口、治理机制和交付结构。  
4. 没有 Harness，Agent 很难长期稳定进入真实工作流。  
5. 随着模型平台化，Harness 会越来越接近 AI 产品的真正本体。  

核心判断：

> 模型不是产品，Agent 也还不是产品；真正接近产品本体的，是那个能把智能组织进工作秩序的 Harness，因为团队最终依赖的不是一次聪明表现，而是一套可以长期托付的工作骨架。
