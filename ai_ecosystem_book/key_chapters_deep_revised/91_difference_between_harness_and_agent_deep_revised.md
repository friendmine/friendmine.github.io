---
layout: book_chapter
title: "第91章 Harness 与 Agent 的区别"
description: "Agent 负责把事情往前推进，Harness 负责决定这件事在什么边界里推进、怎样被看见、怎样被接管、以及最终怎样被交付。前者更像执行智能，后者更像组织智能。"
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

Agent 负责把事情往前推进，Harness 负责决定这件事在什么边界里推进、怎样被看见、怎样被接管、以及最终怎样被交付。前者更像执行智能，后者更像组织智能。
![插图 第91章 执行智能 vs 组织智能对照图]({{ site.baseurl }}/ai_ecosystem_book/images/chapter_091_difference_between_harness_and_agent.png)

> 插图 第91章 执行智能 vs 组织智能对照图

一旦这两层不拆开，团队就会持续在错误层面上用力：本该补流程、权限和交付结构的问题，会被误塞进模型和 prompt。

---

## 1. 这两个概念之所以总被混淆，是因为用户看到的是一个整体

从用户视角看，面前通常只有一个产品界面：输入目标，系统开始工作，最后给出结果。  
在这种体验里，Agent 和 Harness 很容易被看成同一个东西。

但工程上，两者承担的职责完全不同。  
如果不把它们分开，系统设计就会迅速陷入混乱。  
因为你会开始把本该属于权限和工作流的问题，错误地塞进模型决策里；又把本该由执行智能承担的判断，错误地期待 UI 或配置层去解决。

很多项目后期的混乱，本质上都来自概念没有拆开。

而这类混乱最麻烦的地方在于，它会让团队持续在错误层面上用力。  
本来该补的是审批和回退，结果大家去写更长的系统提示；  
本来该补的是目标分解和工具选择，结果大家去加更多界面配置。  
概念不拆开，工程努力就会不断被错误归因带偏。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_01.png)

> 图91-1 “智能内核”与“工作框架”的对照图  
> Agent 更像把任务往前推的智能内核，Harness 更像决定这套推进过程怎样被组织、约束、看见和交付的工作框架。两者共同作用，系统才会从“会动”变成“可用”。

---

## 2. Agent 回答的是“怎么做”，Harness 回答的是“在什么制度里做”

如果用一句最简洁的话区分两者，可以这样说：

- Agent 决定怎样把任务推进下去
- Harness 决定这套推进过程以什么规则和结构运行

Agent 更接近行动内核。  
它理解目标，做局部规划，调用工具，依据反馈继续推进。

Harness 更接近制度环境。  
它决定上下文如何被提供，动作如何被授权，过程如何被记录，何时需要审批，结果如何变成 artifact。

一旦用这个角度去看，很多原本纠缠不清的问题就会立刻清楚。

最常见的混淆，通常发生在几个具体地方。

第一种，是把 `Harness 问题错当 Agent 问题`。  
例如系统总是漏掉审批、权限边界混乱、产出无法沉淀成 artifact，这些问题很多时候并不是 Agent 不够聪明，而是工作框架没有把规则和交付路径组织好。

第二种，是把 `Agent 问题错当 Harness 问题`。  
例如系统明明已经有完整流程和审计，却仍然不会拆任务、不会选工具、不会根据反馈改计划，这时再加更多界面和配置通常也没用，因为真正短板在执行智能本身。

第三种，是把 `两层逻辑全塞进 prompt`。  
这会让目标理解、权限约束、流程要求、异常处理全都混在一个巨大的语言块里。  
短期看像省事，长期看却会让系统边界越来越糊，最后谁也说不清问题到底出在哪一层。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_02.png)

> 图91-2 Agent 与 Harness 各自回答什么问题  
> 这张图把两层职责进一步拉开。Agent 更像“怎么推进”的执行智能，Harness 更像“在什么制度里推进”的组织结构。只有这两层被清楚区分，工程边界才不会长期混线。

也可以把它理解成两种完全不同的“智能”。  
Agent 更像执行智能，关注的是目标如何被推进；  
Harness 更像组织智能，关注的是这套推进过程怎样被约束、被看见、被接住。  
前者决定事情会不会往前走，后者决定这件事往前走时会不会把组织拖进混乱。

---

## 3. 没有 Harness，Agent 很容易变成危险的自由执行器

一个裸 Agent 可能已经很聪明，也可能已经会调很多工具。  
但只要它缺少明确的上下文装配、权限治理、接管入口和过程记录，它就仍然很难进入生产场景。

原因不是它不会做事，而是它做事的方式不可组织。  
它可能：

- 目标理解漂移
- 权限边界模糊
- 错误无法追责
- 结果难以沉淀

这类系统通常在演示中看起来很强，一进入真实组织流程就开始暴露问题。  
因为组织真正要的从来不只是行动能力，而是可被管理的行动能力。

所以“裸 Agent 很强”这件事，本身并不值得惊讶。  
缺少 Harness 的系统，本来就最容易在短期里显得灵活、自由、能力很满。  
但它越自由，组织越难判断它会在哪一步越界；  
它越能行动，责任边界就越需要被显式补出来。  
这也是为什么很多产品不是死在能力不够，而是死在能力开始外溢后没人敢接。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_03.png)

> 图91-3 为什么裸 Agent 难进入生产环境  
> 很多系统的问题不是“不会做”，而是“做的方式不可组织”。这也是 Harness 不可替代的根本原因，它补的不是装饰，而是组织对行动的承接能力。

---

## 4. 反过来，没有 Agent，Harness 也只是一套空框架

但另一面同样成立。  
如果系统只有流程、权限、日志和界面，却没有足够强的执行智能，那么它也只是一个精致的外壳。

它也许能：

- 接收请求
- 记录状态
- 展示审批
- 沉淀结果

却未必能真正把复杂任务往前推进。  
这就像一辆车拥有完整车身、方向盘和刹车，却没有足够有效的发动机。

所以，Agent 和 Harness 不是替代关系，也不是谁附属于谁。  
它们是两个不同层次的问题，必须同时成立，系统才真正成立。

---

## 5. 真正的差别，不在“谁更重要”，而在“谁解决哪类问题”

一些争论之所以无效，是因为它们总在问“Agent 更重要还是 Harness 更重要”。  
这个问题本身就问错了。

更合理的问题是：  
当系统失败时，问题究竟出在什么层面。

如果它不会分解任务、不会选下一步、不会正确使用工具，问题多半在 Agent。  
如果它总是漏关键信息、权限混乱、无法回放、结果无法交付，问题多半在 Harness。

把两种失败清楚地区分开来，团队才知道应该补的是执行智能，还是工作框架。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_04.png)

> 图91-4 失败定位：执行智能问题还是工作框架问题  
> 这张图的作用，是把无效争论压成可定位的问题判断。很多团队浪费时间，不是因为没有努力，而是因为没有先分清责任层。

---

## 6. 为什么未来很多团队的核心竞争力会从 Agent 设计转向 Harness 设计

随着基础模型逐渐平台化，越来越多团队都能调用接近的底层能力。  
在这种情况下，单纯依靠“我们有一个不错的 Agent”并不足以建立长期优势。

真正难复制的，往往是围绕场景打磨出来的 Harness：

- 哪些上下文该在什么时候出现
- 哪些审批节点决定组织是否放心
- 哪些交付格式最适合被团队继续使用
- 哪些回放和审计机制能降低治理成本

这些并不由模型参数自动长出，而是由长期的产品设计和系统工程积累出来。  
这也是为什么未来竞争很可能会越来越偏向 Harness。

从商业上看，这也是更难被复制的部分。  
同一类模型能力，很多团队都可以买到；  
但哪几类审批节点最关键、哪种 artifact 最容易被团队继续工作、哪种回放结构最有助于定位事故，这些通常都来自长期的场景打磨。  
因此，Harness 往往比单点 Agent 技巧更容易形成真正的产品壁垒。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/91_difference_between_harness_and_agent_deep_revised__mermaid_05.png)

> 图91-5 竞争重心为什么会从 Agent 设计偏向 Harness 设计  
> 当执行智能越来越平台化，真正难复制的部分就会更集中在工作结构和治理结构上。产品壁垒因此自然向 Harness 收缩。

---

## 7. 工程现实：最常见的错误，是把所有逻辑都塞进 Agent

很多系统初期都走过一条相似的弯路：  
把工具说明、权限策略、流程要求、输出格式、异常处理，全部写进一个巨大的系统提示词或规划提示词里。

这样做的短期好处是快。  
长期代价却非常高：

- 规则难维护
- 行为难解释
- 改一处容易牵连全局
- 系统边界极不清晰

这类问题背后，正是没有把 Harness 和 Agent 分开思考。  
一旦分开，很多逻辑就会从“依赖模型临场记住”变成“由系统稳定托底”。

这也是一个非常实用的工程判断：  
凡是希望“每次都别忘”的东西，通常就不该只写在 Agent 的语言层里。  
如果某条规则一旦忘掉就会造成权限事故、流程事故或交付事故，那它更应该被提升进 Harness，变成稳定结构，而不是留在 prompt 里赌模型每次都记得住。

---

## 8. 容易误判的地方

### 误解一：Harness 只是 Agent 的包装层

Harness 不是外包装，而是组织、约束和治理行动的系统层。

### 误解二：只要 Agent 足够强，Harness 的价值就会下降

恰恰相反，行动能力越强，越需要更强的框架来承接风险和复杂度。

### 误解三：两者只是不同团队的命名习惯

名称可以不同，但职责差异是真实存在的，混淆它们会直接带来工程代价。

---

## 延伸阅读建议

- 推荐起点：这一章建议把智能内核和工作框架分开继续看，也就是分别追 reasoning engine 和 orchestration layer 两条路线。
- 继续延伸：如果你关心为什么很多团队在错误层面上用力，建议继续追 product architecture、control boundary、system decomposition。
- 检索关键词：`orchestration layer`、`reasoning engine`、`control boundary`、`system decomposition`、`product architecture`。

---

## 10. 本章小结

1. Agent 负责推进任务，Harness 负责为这种推进提供边界、结构和交付秩序。  
2. 没有 Harness，Agent 难以进入生产；没有 Agent，Harness 也会沦为空壳。  
3. 两者不是谁更重要，而是分别解决不同层次的问题。  
4. 许多系统混乱都来自把本该属于 Harness 的问题错误地塞进 Agent。  

核心判断：

> Agent 决定系统会不会做事，Harness 决定这套做事方式能不能被团队接受、被流程承接、也被责任体系容纳。
