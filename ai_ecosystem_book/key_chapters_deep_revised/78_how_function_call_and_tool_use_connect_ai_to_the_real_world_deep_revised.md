---
layout: book_chapter
title: "第78章 Function Call / Tool Use：AI 如何连接真实世界"
description: "Function Call 和 Tool Use 让模型从语言层进入受控执行层，但可靠系统还需要 schema、参数校验、权限、审计、失败恢复和副作用治理。"
book_active: true
permalink: /book/chapters/078-how-function-call-and-tool-use-connect-ai-to-the-real-world/
book_kind: chapter
chapter_number: 78
chapter_slug: how-function-call-and-tool-use-connect-ai-to-the-real-world
book_volume: volume_3
prev_url: /book/chapters/077-limits-of-prompt-engineering/
prev_title: "第77章 Prompt 工程的边界"
next_url: /book/chapters/079-why-more-tools-are-not-always-better/
next_title: "第79章 工具不是越多越好"
---
# 第78章 Function Call / Tool Use：AI 如何连接真实世界

## 先说结论

Function Call 和 Tool Use 的意义，不是给模型多接几个 API。
它们让模型从“描述行动”进入“发起受控行动”：搜索、计算、查数据库、改文件、发消息、调用企业系统，都可以被放进一条结构化执行链。

第77章讲到，Prompt 不能独自承担工具、权限、状态和流程。
第78章接着讨论：当自然语言已经把任务说清楚，系统如何把“想做什么”转成“可以执行什么”。

这一步会显著扩大 AI 的能力边界，也会同步引入新的工程责任。
只要动作会影响真实文件、账户、数据库、消息或业务流程，问题就不再只是回答质量，而是参数是否正确、权限是否合规、动作是否可撤销、失败后能否恢复。

![插图 第78章 从 schema 到副作用的执行链]({{ site.baseurl }}/ai_ecosystem_book/images/chapter_078_how_function_call_and_tool_use_connect_ai_to_the_real_world.png)

> 插图 第78章 从 schema 到副作用的执行链

---

## 1. 没有工具，模型仍然停留在半封闭符号空间

基础模型可以解释概念、生成文本、做推理草稿，也可以根据上下文模拟很多任务。
但如果没有外部工具，它仍然无法直接完成几类工作：

- 获取实时信息
- 查询企业数据库
- 做精确计算
- 操作文件和代码仓库
- 读取网页和应用界面
- 调用业务系统完成动作

也就是说，模型可以谈论行动，却不能直接进入执行层。
它能说“应该查库存”，但不能自己访问库存系统。
它能说“建议改这个文件”，但不能自己写入文件。
它能说“需要发一封确认邮件”，但不能自己进入邮件系统。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/78_how_function_call_and_tool_use_connect_ai_to_the_real_world_deep_revised__mermaid_01.png)

> 图78-1 从模型到工具再到真实世界的调用图
> Tool Use 的价值，在于让模型通过结构化描述、参数约束和执行器代理进入受控执行层。

工具调用改变的是这条边界。
它把模型从纯语言空间接到外部系统，但不是让模型直接“碰世界”。
成熟做法是让模型提出结构化调用意图，由系统校验，再由执行器完成动作。

---

## 2. Function Call 是结构化调用协议，不是模型自己写代码

很多人初次接触 Function Call，会把它理解成“模型会写一段调用代码”。
更准确的理解是：Function Call 是语言模型和外部系统之间的一种结构化协议。

在这条协议里，通常会发生几步：

- 系统向模型暴露可用工具及其 schema
- 模型根据任务选择工具并生成参数
- 平台校验工具名、参数类型和权限边界
- 执行器调用真实系统
- 工具结果返回给模型或工作流继续处理

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/78_how_function_call_and_tool_use_connect_ai_to_the_real_world_deep_revised__mermaid_02.png)

> 图78-2 Function Call 的本质，不是写代码，而是走一条结构化调用协议

这条协议的价值，在于把“模型想做什么”和“系统允许做什么”分开。
模型负责表达意图，系统负责约束和执行。
如果两者混在一起，让模型直接拼接命令或自由生成请求，风险会迅速上升。

---

## 3. 不同工具补的是不同能力边界

Tool Use 不只是“外接功能”。
不同工具对应的是不同短板。

- 计算器补精确计算
- 搜索器补实时信息
- 数据库补结构化查询
- 浏览器补页面和环境交互
- 文件系统补读写与版本操作
- IDE 和执行环境补代码修改、运行和验证
- 企业系统接口补订单、工单、日程、客户记录等业务动作

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/78_how_function_call_and_tool_use_connect_ai_to_the_real_world_deep_revised__mermaid_03.png)

> 图78-3 不同工具的重要性，不在“插件更多”，而在它们分别补足不同边界

从这个角度看，工具不是模型能力的装饰，而是能力边界的一部分。
模型不需要把所有外部能力都内化进参数，但需要学会何时借用外部系统，并把结果纳入下一步判断。

---

## 4. Schema 是工具调用进入生产前的协议边界

工具系统能否进入生产，不取决于“工具已经接上”这件事本身。
更需要看调用协议是否足够清楚。

Schema 至少要说明：

- 工具有哪些参数
- 参数类型是什么
- 哪些字段必填
- 哪些值不合法
- 哪些动作只读，哪些动作可写
- 哪些动作需要人工确认
- 返回结果采用什么结构

没有 schema，工具调用很容易退化成自由文本解析。
模型说一句“帮我查一下这个用户最近三个月的消费额”，系统还要猜用户是谁、时间窗口怎么算、查哪张表、字段叫什么、输出什么格式。

有 schema 以后，模型需要交出更明确的调用：

- 工具名
- 参数名
- 参数值
- 可选字段
- 预期返回结构

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/78_how_function_call_and_tool_use_connect_ai_to_the_real_world_deep_revised__mermaid_04.png)

> 图78-4 Schema 和参数验证是可靠性闸门

Schema 的作用，不只是让调用“看起来结构化”。
它让系统有机会在执行前拦截错误：参数缺失、类型不符、范围越界、权限不足、危险动作未审批，都可以在这一层被挡住。

---

## 5. 执行器是模型和真实系统之间的隔离层

Tool Use 里有一个容易被忽略的角色：执行器。

模型不应直接操作真实系统。
它应该提交结构化意图，由执行器负责：

- 校验参数
- 检查权限
- 调用外部 API
- 处理超时和异常
- 记录日志
- 返回结构化结果
- 必要时请求人工确认

这层隔离很有必要。
因为模型生成的是概率性输出，而外部系统执行的是确定性动作。
两者之间需要一层可治理、可观测、可拒绝、可恢复的执行代理。

如果没有执行器，工具调用会变成模型自由拼接动作。
一旦涉及数据库、文件、支付、邮件、权限或生产环境，这种设计就很难承担生产责任。

---

## 6. 副作用是 Tool Use 和普通回答的分界线

普通回答错了，风险主要停留在信息层。
工具调用错了，风险可能进入现实系统。

副作用包括：

- 修改文件
- 写入数据库
- 发出邮件或消息
- 提交代码
- 创建订单
- 删除记录
- 修改权限
- 触发付款或退款

这些动作和普通文本输出不同。
它们可能被外部系统记录、传播、执行，甚至无法轻松撤回。

因此，Tool Use 的设计不能只看“能不能调通”。
更要看动作的风险等级：

- 只读动作可以更宽松
- 写入动作需要更强校验
- 高风险动作需要审批
- 不可逆动作需要额外保护
- 批量动作需要更严格的范围限制

这也是从 Prompt 到 Tool Use 的工程转折。
自然语言可以表达意图，但真实动作需要治理。

---

## 7. 失败处理比成功演示更能说明系统成熟度

工具调用的成功路径通常不难演示。
更能检验系统的，是失败路径。

真实系统里经常会发生：

- 参数填错
- 权限不足
- 工具超时
- 外部服务返回异常
- 返回格式变化
- 调用部分成功
- 重试导致重复执行
- 外部状态在执行过程中变化

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/78_how_function_call_and_tool_use_connect_ai_to_the_real_world_deep_revised__mermaid_05.png)

> 图78-5 工具系统是否成熟，往往看失败时会不会崩

成熟 Tool Use 系统需要回答：

- 失败后是否重试
- 重试是否幂等
- 是否会重复扣款、重复发信或重复提交
- 部分成功后如何补偿
- 失败信息如何回到模型和用户
- 什么时候继续自动处理，什么时候交还给人

演示只需要跑通一条顺畅路径。
生产系统需要在偏航时仍然保持秩序。

---

## 8. Tool Use 是从“会说”到“会做”的分界线

语言模型早期主要是解释、改写、总结、生成和分析。
工具调用让它开始进入执行接口。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/78_how_function_call_and_tool_use_connect_ai_to_the_real_world_deep_revised__mermaid_06.png)

> 图78-6 Tool Use 是从“会说”到“会做”的一道分界线

这一步让后面的 Agent、Harness、工作流和协作系统有了基础。
没有工具调用，很多“自动完成任务”的说法只能停留在语言层。
有了工具调用，模型才有机会把计划拆成行动，并根据外部结果继续调整。

但这并不意味着模型获得工具后就自动可靠。
它仍然可能选错工具、误填参数、误解返回值，或在不合适的时机触发动作。
因此，Tool Use 是执行能力的入口，不是可靠性的终点。

---

## 9. 工具治理决定它能走多远

工程里困难的地方，不只是把工具接上，而是把工具治理好。

一套可用工具系统通常需要：

- 工具注册：名称、职责、schema、返回格式统一
- 工具描述：让模型能理解何时该用、何时不该用
- 权限策略：区分只读、写入、高风险动作
- 参数校验：在执行前拦截错误
- 日志审计：记录谁触发、何时触发、结果如何
- 失败恢复：处理超时、部分成功和重复执行
- 人工确认：对高风险动作设置交接点

这也是第79章要继续展开的问题。
工具一旦增多，系统面对的就不只是“能不能调用”，而是工具选择、路由、权限、冲突和复杂度治理。

---

## 10. 容易误判的地方

### 误解一：Tool Use 就是给模型接几个 API

接口只是开始，可靠系统还需要 schema、校验、执行器、权限、日志和恢复机制。

### 误解二：模型会调工具，就不会产生错误

模型仍然可能选错工具、填错参数、误读返回结果，或在错误时机触发动作。

### 误解三：结构化调用会自动带来安全

结构化调用提供了校验入口，但安全还需要权限、审批、隔离、审计和回滚。

### 误解四：成功跑通一次，就说明系统可用

生产系统还要看失败路径、重试幂等、部分成功处理和人工交接。

---

## 延伸阅读建议

- 推荐起点：ReAct、Toolformer、function calling、structured output 可以连起来看，它们都在讨论推理、调用和执行之间的关系。
- 继续延伸：如果你关心生产系统，建议继续看 schema design、tool routing、side effect control、idempotency、rollback、audit。
- 检索关键词：`ReAct`、`Toolformer`、`function calling`、`tool routing`、`side effect`、`idempotency`、`rollback`、`audit`。

---

## 11. 本章小结

1. Tool Use 把模型从语言层接到受控执行层。
2. Function Call 是结构化调用协议，不是模型自由写代码。
3. 不同工具补足不同能力边界。
4. Schema、参数校验和执行器构成执行前的治理入口。
5. 副作用、幂等、失败恢复和审计决定工具系统能否进入生产。
6. Tool Use 是“会说”到“会做”的分界线，但可靠性还要靠系统治理。

核心判断：

> AI 连接真实世界，不是靠模型直接触碰外部系统，而是靠结构化调用、受控执行、权限边界和失败恢复共同组成的执行链。
