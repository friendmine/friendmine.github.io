---
layout: book_chapter
title: "第90章 Harness 的五层结构"
description: "Harness 需要分层，不是为了让架构图更漂亮，而是为了让界面、上下文、动作、控制和流程各自承担清楚责任，系统出问题时能定位、能止损、能演进。"
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

Harness 需要分层，不是为了让架构图更漂亮，而是为了让界面、上下文、动作、控制和流程各自承担清楚责任，系统出问题时能定位、能止损、能演进。

不分层的代价，通常不是代码不优雅，而是出了事故以后没人说得清到底是哪一层先失守。

![插图 第90章 Harness 五层剖视图]({{ site.baseurl }}/ai_ecosystem_book/images/chapter_090_five_layer_structure_of_harness.png)

> 插图 第90章 Harness 五层剖视图

第89章说明 Harness 是 Agent 的工作框架。
本章进一步把这个框架拆成五层：Interface、Context、Action、Control、Workflow。
这五层不是按技术炫技划分，而是按责任边界划分。

---

## 1. 不分层的 AI 系统，很快会变成一团难以定位的混杂结构

很多 AI 系统一开始都能跑。
问题是，它们经常把界面、prompt、上下文、权限、工具调用、状态存储和流程判断混在一起。

演示阶段，这种写法很快。
进入真实环境，它会迅速暴露责任边界问题。

事故排查时，团队会答不上这些问题：

- 用户看到的状态，是真实系统状态，还是模型推断出来的说法
- 当前模型调用里到底注入了哪些上下文
- 某个工具动作是谁批准的
- 高风险动作为什么没有被拦截
- 任务卡住时，当前处于哪个流程阶段
- 交付物是正式版本，还是中间草稿
- 出错以后应该回滚哪一层

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_01.png)

> 图90-1 Harness 的五层结构分解图
> 五层不是按技术展示划出来的，而是按责任划出来的。Interface 负责看见，Context 负责看清，Action 负责动手，Control 负责托底，Workflow 负责把整件事做成。

没有分层，所有问题最后都会被压成一句话：系统不稳定。
但“系统不稳定”不是可修复的诊断。
工程上真正需要的是知道哪一层不稳定。

---

## 2. 五层结构先解决责任边界，而不是先解决代码组织

这五层可以先用一句话概括。

- `Interface Layer`：人如何看见系统、操作系统、接收交付物
- `Context Layer`：模型当前看见什么信息、规则、记忆和状态
- `Action Layer`：模型的行动意图如何变成标准工具调用
- `Control Layer`：哪些动作可以发生，哪些必须拦截、审批或回滚
- `Workflow Layer`：任务如何跨步骤、跨状态、跨异常持续推进

如果进一步写成输入、输出和边界，会更清楚：

- `Interface Layer`
  输入：用户目标、操作、确认、反馈
  输出：可见状态、审批入口、交付物、接管入口
  边界：负责人怎样看见和操作，不替 Agent 做推理

- `Context Layer`
  输入：规则、历史、记忆、检索结果、当前任务状态
  输出：模型当前可见的工作上下文
  边界：负责模型看到什么，不直接执行动作

- `Action Layer`
  输入：模型给出的动作意图
  输出：标准化工具调用、工具结果、错误语义
  边界：负责怎么动手，不决定是否应该放行

- `Control Layer`
  输入：动作申请、权限规则、风险等级、人工信号
  输出：放行、拦截、审批、审计、回退、接管
  边界：负责能不能做和出事怎么止损，不产生业务目标

- `Workflow Layer`
  输入：当前阶段、步骤依赖、完成条件、异常状态
  输出：下一阶段、分支选择、等待、返工、完成或回退
  边界：负责事情怎样连续做完，不替代具体执行智能

一旦边界清楚，工程讨论会从“都很重要”变成“到底是哪一层责任没有站住”。

---

## 3. Interface Layer 负责把系统状态翻译成人能使用的协作界面

很多人看到 Interface Layer，会以为这只是聊天窗口。
实际上，界面层远不止输入框和输出框。

成熟的 Interface Layer 需要展示：

- 当前任务目标
- 系统所处阶段
- 关键上下文摘要
- 中间执行痕迹
- 等待审批的动作
- 可接管入口
- 交付物和版本
- 错误与恢复建议

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_02.png)

> 图90-2 Interface Layer 的最小职责闭环
> 界面层不是把模型输出原样显示出来，而是把任务状态、可操作入口和可交付结果翻译成人类可用的协作对象。

这一层做不好，系统再强，用户也会感到模糊和失控。

典型失败包括：

- 用户看不出任务走到哪一步
- 中间状态只藏在聊天记录里
- 审批入口不清楚
- 交付物和解释混在一起
- 人想接管时不知道从哪里接

这也是为什么很多聊天式产品后面会被迫长出任务卡、状态面板、artifact 区、审批入口和历史记录。
不是产品突然变重，而是工作一旦变长，纯对话很难继续承担可见性和可操作性。

---

## 4. Context Layer 是系统给模型组织“当前世界”的地方

Agent 的上限，很大程度上取决于它每一轮能看见什么。
这就是 Context Layer 的工作。

它不是简单拼 prompt。
它要决定：

- 哪些系统规则始终有效
- 哪些用户目标和约束要置顶
- 哪些任务状态必须带入
- 哪些历史信息需要摘要
- 哪些检索结果值得进入窗口
- 哪些记忆与本轮决策有关
- 哪些旧信息必须退场
- 哪些数据因权限不能暴露

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_03.png)

> 图90-3 Context Layer 如何组织模型的“当前世界”
> 这一层的本质，是决定模型在这一轮到底看见什么。很多系统表现不稳，不是模型突然变差，而是 Context Layer 没有把关键约束和状态组织好。

Context Layer 出问题，常见表现不是系统立刻崩溃。
更常见的是一种漂移感：
模型似乎在理解，实际一直漏关键约束。

从调试角度看，这一层也最容易被误判。
团队看到结果变差，会先怀疑模型版本、推理参数或提示词文案；
真实原因可能是上下文供给已经漂了，关键状态没进窗口，旧规则没退场，检索材料顺序错了。

如果 Context Layer 没有独立出来，这类问题很难稳定定位。

---

## 5. Action Layer 把语言意图压成可执行、可校验的动作

模型可以说：
应该查数据库，应该修改文件，应该调用接口，应该创建工单。

但系统不能直接把这些自然语言当作动作。
Action Layer 要把语言意图压成标准动作。

它通常负责：

- 工具注册
- 工具 schema
- 参数约束
- 协议转换
- dry-run 支持
- 结果标准化
- 错误语义统一
- 幂等和重试策略
- 副作用说明

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_04.png)

> 图90-4 Action Layer 把语言意图压成标准动作
> Action Layer 的价值，是把“想做什么”稳定翻译成“怎样动手”。没有这层，工具能力越多，系统越容易在关键动作上变脆。

Action Layer 清楚，系统的行动空间就清楚。
Action Layer 混乱，Agent 看起来会很多事，实际上经常在关键动作上失手。

典型失败包括：

- 参数格式正确但语义错误
- 工具返回成功但只完成一半
- 错误码不可读
- 重试造成重复副作用
- 工具接口边界模糊
- 模型把工具结果误解为任务完成

工具越多，Action Layer 越不能靠临时说明和模型自觉。
它必须成为稳定契约。

---

## 6. Control Layer 决定系统是否值得被信任

Control Layer 是很多团队最容易后补，也最容易补不好的部分。

演示环境里，没有控制层似乎也能跑通。
真实世界里，缺少控制层的系统很难被放心使用。

这一层要解决：

- 权限边界
- 风险分级
- 审批节点
- 操作预算
- 停止条件
- trace 和 replay
- 审计记录
- 回退与人工接管

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_05.png)

> 图90-5 Control Layer 是信任的闸门
> 控制层不创造业务目标，但它决定动作是否能够发生，以及发生之后是否拥有可审计、可回退、可解释的责任路径。

Control Layer 的意义不是让系统保守。
它的意义是让行动拥有责任路径。

没有控制层，Agent 的动作半径会扩大，却缺少审计、回退和接管路径。
一旦出事，组织没有缓冲。

特别是越权、误触发、死循环、成本失控和不可逆动作，都不能只靠 prompt 约束。
它们需要由 Control Layer 硬性托底。

---

## 7. Workflow Layer 负责让任务从单步动作变成可完成流程

很多系统的问题不是单步能力不足，而是无法把多个正确步骤组织成一件完成的事。

Workflow Layer 处理的是任务连续性。

它要回答：

- 当前处于哪个阶段
- 下一步应该走向哪里
- 哪些步骤有固定顺序
- 哪些节点需要等待
- 哪些地方允许返工
- 哪些异常需要回退
- 什么时候任务算完成
- 交付后是否需要复盘

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_06.png)

> 图90-6 Workflow Layer 负责任务连续性
> Workflow Layer 的价值，不在于画流程图，而在于给系统提供“这件事现在走到哪、接下来往哪、失败后怎么回退”的持续组织能力。

没有 Workflow Layer，系统会做动作，却不知道阶段；
会执行步骤，却不知道何时等待、何时回退、何时正式完成。

这就是 Harness 与简单工具集合之间的重要差别。
工具集合解决“能不能做某个动作”。
Workflow 解决“能不能把一件事做成”。

---

## 8. 五层结构的价值，是形成统一工程语言

系统当然可以划成四层、六层，或使用不同命名。
五层结构的价值，不是宣称它唯一正确，而是它足够贴近现实责任边界。

有了这套语言，团队更容易讨论：

- 当前问题是界面不可见，还是上下文污染
- 是工具接口不稳，还是控制层缺失
- 是任务状态没建模，还是 workflow 没设计
- 是 Agent 不会做，还是 Harness 没接住

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/90_five_layer_structure_of_harness_deep_revised__mermaid_07.png)

> 图90-7 五层结构为什么能降低工程沟通成本
> 分层的另一层价值，是让团队讨论问题时不再停留在“系统不稳定”，而是能直接定位到责任层。

统一语言会显著降低沟通成本。
更重要的是，它让改动开始局部化。

界面要变，未必要碰控制层。
权限策略要收紧，未必要重写任务逻辑。
上下文装配升级，也不必顺手把 workflow 改坏。

---

## 9. 工程现实：多数技术债都来自层次混线

现实里常见技术债，大多能在五层混线里找到源头。

例如：

- 在 UI 里写死 workflow 判断
- 在 prompt 里硬编码权限策略
- 把工具错误处理塞进上下文拼装逻辑
- 把任务状态散落在消息历史里
- 让模型自行决定高风险动作是否放行
- 把 artifact 生命周期混进聊天回复

这些做法短期看似省事，长期都会让系统难以维护、难以调试、难以扩展。

分层的意义，就是提前阻止这些混线变成结构性债务。

成熟团队后期往往会发现：
五层结构最大的价值不是图画得清楚，而是系统演进速度更稳。
因为每次改动都更容易知道影响范围。

---

## 10. 第90章到第91章：分层之后，还要区分 Agent 和 Harness

本章讲的是 Harness 内部怎样分层。
第91章会继续回答另一个关键问题：
Harness 和 Agent 到底有什么区别。

这两个问题连在一起看，工程边界会更清楚。

Agent 更像执行智能，负责理解目标、做局部规划、选择工具、根据反馈推进。
Harness 更像组织智能，负责把这种推进放进界面、上下文、动作、控制和流程这些边界里。

如果团队不先理解五层结构，就很难理解 Harness 为什么不是 Agent 的包装层。
如果团队不区分 Agent 和 Harness，就会把五层责任重新塞回模型和 prompt。

---

## 11. 容易误判的地方

### 误解一：分层只是大公司才需要的架构洁癖

不是。
哪怕是小团队，只要系统接触真实工具、权限和交付，职责边界清楚就能显著减少返工。

### 误解二：Context Layer 只是 prompt 拼接逻辑

Context Layer 是系统认知组织的核心。
它决定模型在当前任务里看见怎样的世界。

### 误解三：Action Layer 只是工具调用封装

不只是封装。
它要定义工具契约、参数边界、错误语义和副作用处理。

### 误解四：Control Layer 可以等系统成熟后再补

一旦系统开始接触真实动作和真实责任，控制层就不能再是可选项。

### 误解五：Workflow Layer 只是画流程图

Workflow Layer 负责任务连续性、等待、返工、完成条件和异常回退。
它不是流程图装饰。

---

## 延伸阅读建议

- 推荐起点：继续看 interface contract、context engineering、tool protocol、policy layer、workflow orchestration 等结构化设计主题。
- 工程延伸：建议追 control plane、approval chain、audit system、task state model、artifact lifecycle、trace replay。
- 检索关键词：`interface contract`、`context engineering`、`tool protocol`、`policy layer`、`workflow orchestration`、`control plane`、`audit system`。

---

## 12. 本章小结

1. Harness 可以拆成 Interface、Context、Action、Control、Workflow 五层。
2. 五层结构的目的，是把不同类型的复杂度放回正确责任位置。
3. Interface 负责可见和可操作，Context 负责模型看到什么，Action 负责标准动作，Control 负责信任和止损，Workflow 负责任务连续性。
4. 分层不是形式主义，而是定位问题、局部改动和长期演进的工程手段。
5. 理解五层结构，是下一章区分 Harness 与 Agent 的前提。

核心判断：

> Harness 的五层结构，是在为 AI 协作系统建立一套能定位问题、承接责任、控制风险、持续交付的工程语法。
