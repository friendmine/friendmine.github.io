---
layout: book_chapter
title: "第90章 Harness 的五层结构"
description: "Harness 需要分层，不是为了把架构图画得更漂亮，而是为了让系统变复杂以后，仍然知道界面、上下文、动作、控制和流程分别该由谁负责。"
book_active: true
permalink: /book/chapters/090-five-layer-structure-of-harness/
book_kind: chapter
chapter_number: 90
chapter_slug: five-layer-structure-of-harness
book_volume: volume_3
prev_url: /book/chapters/089-harness-as-the-work-framework-for-agents/
prev_title: "第89章 Harness：给 Agent 套上工作框架"
next_url: /book/chapters/091-difference-between-harness-and-agent/
next_title: "第91章 Harness 与 Agent 的区别"
---
# 第90章 Harness 的五层结构

## 先说结论

Harness 需要分层，不是为了把架构图画得更漂亮，而是为了让系统变复杂以后，仍然知道界面、上下文、动作、控制和流程分别该由谁负责。  
不分层的代价，通常不是“代码不优雅”，而是出了问题时根本不知道该从哪一层下手止损。
![插图 第90章 Harness 五层剖视图]({{ site.baseurl }}/ai_ecosystem_book/images/chapter_090_five_layer_structure_of_harness.png)

> 插图 第90章 Harness 五层剖视图

---

## 1. 不分层的 AI 系统，最后很容易沦为难以维护的混杂结构

很多草率搭起来的 AI 系统，一开始都能跑。  
问题在于，它们往往把界面、prompt、权限、工具调用和状态存储混在一起写。  
这种写法在演示阶段也许足够快，但一旦进到真实环境，最先炸开的往往不是某个功能点，而是责任边界。

因为到了真正出事的时候，你会立刻发现自己答不上这些最关键的问题：

- 人类在前端界面上看到的，究竟是真实机器反馈，还是模型自己的推断结果？
- 当前大模型推断时，究竟被夹带了多少无关或污染性的上下文？
- 这个高风险动作是谁放行的？有哪些危险动作并未被控制层拦截？
- 当前这条长任务流，究竟是在哪个节点开始偏航的？

五层结构真正解决的，就是把这团纠缠在一起的责任线拆开。  
一旦无视分层，系统最先失去的不是“优雅”，而是定位问题和快速止损的能力。  
前端挂了，会被误判成模型问题；权限穿透了，又有人想靠改 prompt 补洞；任务长链崩了，团队开始在接口、模型和流程之间互相甩锅。  
最后所有问题都会表现成一句模糊的话：系统不稳定。可真正致命的地方在于，没有人知道到底是哪一层先失守的。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_01.png)

> 图90-1 Harness 的五层结构分解图  
> 五层不是按技术炫技划出来的，而是按责任划出来的。界面负责看见，Context 负责看清，Action 负责动手，Control 负责托底，Workflow 负责把整件事做成。

---

## 2. Interface Layer 负责把系统的状态翻译成人能使用的界面

许多人一看到 Interface Layer，就以为这只是在说聊天窗口。  
实际上，这一层远不止输入框和输出框。

真正成熟的 Interface Layer 需要向人展示：

- 当前任务目标
- 系统所处阶段
- 中间执行痕迹
- 等待审批的动作
- 最终交付物

它的作用不是把模型的话显示出来，而是把整个协作过程变成人类可以理解、可以操作的界面对象。  
如果这一层做不好，系统再强，用户也只会感到模糊和失控。

如果用输入、输出和责任边界来描述这五层，会更清楚：

- `Interface Layer`
  输入：用户目标、操作、确认  
  输出：可见状态、交付物、审批入口  
  边界：负责“人怎么看见和操作”，不负责替 Agent 做判断

- `Context Layer`
  输入：规则、历史、记忆、检索结果、当前任务状态  
  输出：模型当前可见的工作上下文  
  边界：负责“模型看到什么”，不直接执行动作

- `Action Layer`
  输入：模型给出的动作意图  
  输出：标准化工具调用和统一结果  
  边界：负责“怎么动手”，不决定这件事是否被允许

- `Control Layer`
  输入：动作申请、风险等级、权限规则、人工信号  
  输出：放行、拦截、审计、回退、接管  
  边界：负责“能不能做、出了事怎么止损”，不负责产生业务目标

- `Workflow Layer`
  输入：当前阶段、步骤依赖、完成条件、异常状态  
  输出：下一阶段、分支选择、任务完成或回退  
  边界：负责“事情怎么连续做完”，不替代具体执行智能

一旦这五层的输入输出清楚了，很多工程争论就会从“都很重要”变成“到底是哪一层责任没有站住”。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_02.png)

> 图90-2 Interface Layer 的最小职责闭环  
> 界面层不是把模型输出原样显示出来，而是把任务状态、可操作入口和可交付结果翻译成人类可用的协作对象。

这也是为什么很多看起来很“自然”的聊天式产品，后面会被迫长出状态面板、任务卡片、审批入口和 artifact 区。  
不是产品突然变重了，而是工作一旦变长，纯对话很难继续承担可见性和可操作性。  
Interface Layer 真正负责的，是把系统已经拥有的复杂性翻译成用户还能驾驭的形状。

---

## 3. Context Layer 是整个 Harness 的认知中枢

Agent 的上限，很大程度上取决于系统在每一轮能给它看见什么。  
这就是 Context Layer 的工作。

它要处理的并不只是“把历史消息拼进去”，而是要决定：

- 哪些系统规则始终有效
- 哪些任务状态必须被显式带入
- 哪些历史信息应该被摘要
- 哪些检索结果值得放进当前窗口
- 哪些记忆与本轮决策有关

这一层如果做得粗糙，后果往往不是系统立刻崩溃，而是它会稳定地产生一种“似乎在理解，实际一直在漏关键约束”的漂移感。  
所以 Context Layer 不只是技术组件，更是整个系统的认知组织方式。

从调试角度看，Context Layer 也是最容易被误判的一层。  
很多团队看到结果变差，会先怀疑模型版本、推理参数或者 prompt 文案；  
但真实问题往往是上下文供给已经漂了，关键状态没进窗口、旧约束没退场、检索材料顺序错了。  
如果没有把 Context Layer 独立出来，团队几乎不可能稳定定位这类问题。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_03.png)

> 图90-3 Context Layer 如何组织模型的“当前世界”  
> 这一层的本质，是决定模型在这一轮到底看见什么。很多系统表现不稳，不是模型突然变差，而是 Context Layer 长期没有把真正关键的约束和状态组织好。

---

## 4. Action Layer 把语言决策翻译成可执行动作

模型可以说“应该查数据库”“应该修改文件”“应该调用接口”，但真正的系统必须把这些模糊意图落到具体动作上。  
Action Layer 负责的就是这件事。

它通常要承担：

- 工具注册
- 参数约束
- 协议转换
- 结果标准化
- 失败返回的统一处理

这一层越清楚，系统的行动空间就越明确。  
反过来，如果 Action Layer 混乱，Agent 看起来像会很多事，实际上却常常在关键动作上失手，因为它碰到的是一组定义模糊、边界不清的接口。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_04.png)

> 图90-4 Action Layer 把语言意图压成标准动作  
> Action Layer 的价值，在于把“想做什么”稳定地翻译成“怎样动手”。没有这层，工具能力越多，系统反而越容易在关键动作上变得脆弱。

---

## 5. Control Layer 决定系统能不能被信任

Control Layer 是许多团队最容易后补、也最容易补不好的部分。  
原因很简单：在演示环境里，没有控制层似乎也能跑通；但一旦进入真实世界，没有控制层，系统就不配被使用。

这一层要解决的是：

- 权限边界
- 审批节点
- 中断机制
- trace 和 replay
- 审计记录
- 回退与人工接管

它的意义不在于让系统更保守，而在于让系统的行动拥有清晰责任路径。  
没有控制层，Agent 就像没有刹车和仪表盘的车，跑起来很刺激，出事时却没有任何缓冲。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_05.png)

> 图90-5 Control Layer 是信任的闸门  
> 控制层不创造业务目标，但它决定动作是否能够真正发生，以及发生之后是否拥有可审计、可回退、可解释的责任路径。

---

## 6. Workflow Layer 决定任务能否被连续做成

很多系统的问题不是单步能力不足，而是无法把若干正确步骤组织成一个可持续推进的过程。  
Workflow Layer 处理的正是这种“任务连续性”问题。

它要回答的是：

- 当前处于哪一个阶段
- 下一步应当走向哪里
- 哪些步骤必须固定顺序
- 哪些节点允许分支和回退
- 什么时候任务算正式完成

一旦有了这一层，系统才真正从“会做一些动作”走向“能把一件事做完”。  
这也是 Harness 与简单工具集合之间最本质的差别之一。

很多系统看起来功能已经很全，却总让人觉得“不放心放进真实流程”，问题常常就卡在 Workflow Layer。  
因为它们会做动作，却不知道阶段；  
会执行步骤，却不知道什么时候该等待、何时该回退、何时才算真正完成。  
没有这层，系统永远更像会动手的零件，而不是能把事情做成的工作系统。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_06.png)

> 图90-6 Workflow Layer 负责任务连续性  
> Workflow Layer 的价值，不在于画一张流程图，而在于给系统提供“这件事现在走到哪、接下来该往哪、失败后怎么回退”的持续组织能力。

---

## 7. 五层结构的价值，不在于唯一正确，而在于形成统一工程语言

当然，你完全可以把系统划成四层、六层，甚至用别的名字。  
五层结构的意义并不在于宣称它是唯一真理，而在于它足够贴近现实需求，也足够便于团队沟通。

有了这套骨架，团队会更容易讨论：

- 当前问题是界面问题，还是上下文问题
- 是动作接口不稳，还是控制层缺失
- 是流程没有设计好，还是状态没有建模好

这类统一语言，会极大降低系统继续长大后的沟通成本。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_07.png)

> 图90-7 五层结构为什么能降低工程沟通成本  
> 分层的另一层价值，是让团队讨论问题时不再停留在“系统不稳定”这种大而化之的说法，而是能直接定位到责任层，从而更快修系统。

---

## 8. 工程现实：多数技术债都源于层次混线

现实里常见的技术债，几乎都能在五层混线里找到源头。  
比如：

- 在 UI 里写死工作流判断
- 在 prompt 里硬编码权限逻辑
- 把工具错误处理塞进上下文拼装逻辑
- 把任务状态散落在消息历史里

这些做法短期看似省事，长期都会让系统难以维护、难以调试、难以扩展。  
分层的真正意义，就是提前阻止这些混线变成结构性债务。

成熟团队后期往往会发现，五层结构最大的价值不在“画图更清楚”，而在“改动开始局部化”。  
界面要变，不一定要碰控制层；  
权限策略要收紧，不一定要重写任务逻辑；  
上下文装配要升级，也不必顺手把 workflow 一起改坏。  
谁先把这一点做出来，谁的系统演进速度就会明显更稳。

---

## 9. 容易误判的地方

### 误解一：分层只是大公司才需要的架构洁癖

哪怕是小团队，职责边界清楚也会显著减少返工和混乱。

### 误解二：Context Layer 只是 prompt 拼接逻辑

它其实是系统认知组织的核心，不只是字符串处理。

### 误解三：Control Layer 可以等系统成熟后再补

一旦系统开始接触真实动作和真实责任，控制层就不能再是可选项。

---

## 延伸阅读建议

- 推荐起点：这一章最适合继续看 interface contract、policy layer、tool boundary、workflow orchestration 这些结构化设计主题。
- 继续延伸：如果你关心五层结构如何真正落地，建议继续追 control plane、approval chain、audit system。
- 检索关键词：`interface contract`、`policy layer`、`workflow orchestration`、`control plane`、`audit system`。

---

## 11. 本章小结

1. Harness 可以被拆成 Interface、Context、Action、Control、Workflow 五层。  
2. 五层结构的目的是把不同类型的复杂度放回正确位置。  
3. Context 是认知中枢，Control 是信任前提，Workflow 是任务连续性的组织者。  
4. 分层不是形式主义，而是协作系统避免失控的基本工程手段。  

核心判断：

> Harness 的五层结构，本质上是在为 AI 协作系统建立一套能长期扩展、也能长期维护的工程语法。
