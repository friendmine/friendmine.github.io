---
layout: book_chapter
title: "第41章 优化与训练"
description: "训练模型不是让程序多跑一会儿，而是在有限资源里不断调整参数，试图把错误压到更低的位置。"
book_active: true
permalink: /book/chapters/041-optimization-and-training/
book_kind: chapter
chapter_number: 41
chapter_slug: optimization-and-training
book_volume: volume_2
prev_url: /book/chapters/040-derivatives-gradients-and-change/
prev_title: "第40章 导数、梯度与变化"
next_url: /book/chapters/042-floating-point-and-numerical-stability/
next_title: "第42章 浮点数与数值稳定性"
---
# 第41章 优化与训练

上一章讲导数、梯度和反向传播，解决的是“方向信号从哪里来”。第41章继续向前：拿到方向信号之后，系统怎样在有限数据、有限算力和带噪梯度中，一步步调整参数。

训练模型并不是让程序多跑一会儿，而是在一个高维、非凸、带噪、受资源约束的空间里反复做决策。每一步都要回答：目标是什么，梯度来自哪一批样本，学习率多大，用什么优化器，是否需要调度，什么时候停止，以及 loss 下降是否真的对应任务变好。

因此，优化不是梯度之后的附属环节，而是把梯度变成能力形成过程的工程系统。

---

## 1. 有了方向，还要知道怎样走

梯度告诉系统当前位置附近哪个方向更像在降低损失，但它只解决了训练的一部分问题。真实训练还要决定怎样使用这个方向信号。

需要处理的问题包括：

- 每一步走多远
- batch 噪声会不会误导方向
- 更新是否在某些区域震荡
- 参数是否过早停在低效区域
- 训练预算是否允许继续探索
- 当前 loss 下降是否对应泛化提升

这些问题合在一起，构成优化。梯度像局部方向信号，优化则是围绕这个信号设计的一整套行进策略。训练能否稳定推进，取决于方向、步幅、噪声、目标、数据和资源之间能否形成可控闭环。

---

## 2. 损失函数规定“什么叫变好”

训练需要一个可计算目标，这个目标通常由损失函数表达。

可以写成：

$$
\mathcal{L}(\theta)
$$

这里 `\theta` 代表模型参数，`\mathcal{L}` 代表在当前任务定义下的损失。监督学习中常见形式是：

$$
\mathcal{L}(\theta)=\frac{1}{N}\sum_{i=1}^{N}\ell(f_\theta(x_i), y_i)
$$

它表示：模型对输入 `x_i` 给出预测，再和目标 `y_i` 比较，最后把样本误差聚合起来。

损失函数不是一个中性的分数。它规定系统把什么当作改进，把什么当作错误。分类、回归、语言建模、排序、偏好学习和对齐任务使用不同损失，背后是不同的训练目标。

这也带来一个风险：如果损失函数只是业务目标的代理，模型可能会越来越擅长优化代理目标，却没有同步提升真实任务表现。优化过程再顺，也只能沿着损失函数给出的目标前进。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/41_optimization_and_training_deep_revised__mermaid_01.png)

> 图41-1 损失函数决定系统把什么当成“更好”

---

## 3. 梯度下降把巨大搜索改写成局部更新

参数空间很大，靠穷举或盲目试探没有现实可行性。梯度下降的价值在于，它把高维搜索改写成一连串局部更新。

基本形式是：

$$
\theta_{t+1}=\theta_t-\eta \nabla_\theta \mathcal{L}(\theta_t)
$$

其中：

- `\theta_t` 是当前参数
- `\nabla_\theta \mathcal{L}` 是当前位置的梯度
- `\eta` 是学习率

这条式子的含义很直接：先计算当前位置附近的下降方向，再按给定步幅更新参数。它不保证一次到达好解，却提供了一种可以重复执行的训练闭环：前向计算、算损失、反向传播、更新参数，再进入下一轮。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/41_optimization_and_training_deep_revised__mermaid_02.png)

> 图41-2 训练更新的最小闭环
> 这张图先抽出训练的核心循环：算损失、求梯度、更新参数、再继续；完整工程细节会在后面逐步展开。

---

## 4. mini-batch 让训练可承受，也让方向带噪

如果每一步都用完整训练集计算梯度，代价会很高。现代训练通常使用 mini-batch：每一步只取一小批样本估计梯度。

几个概念需要分清：

- `sample`：单个样本
- `mini-batch`：一次用于计算梯度的一小批样本
- `batch size`：mini-batch 中的样本数量
- `epoch`：训练过程完整扫过一遍训练集

mini-batch 让训练成本可承受，也让梯度变成估计量。不同 batch 会给出不同方向，样本顺序、数据增强、分布式切分和 batch size 都会影响训练轨迹。

这就是为什么训练曲线会抖动，也解释了为什么 batch size 不能只被看成吞吐参数。batch 太小，方向噪声可能过大；batch 太大，单步代价、显存压力和泛化行为都会改变。训练从一开始就是带噪声和预算约束的搜索。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/41_optimization_and_training_deep_revised__mermaid_03.png)

> 图41-3 训练不是一口气看完整个世界，而是在很多小批次上反复逼近

---

## 5. 学习率控制训练步幅

学习率决定每次沿梯度方向走多远。它是训练中敏感度很高的参数。

学习率过大，更新可能越过合适区域，引起震荡、发散，甚至出现 `Loss NaN`；学习率过小，训练会推进缓慢，昂贵算力被长时间消耗，却难以看到有效进展。

这也是为什么学习率调度会成为训练工程中的常规设计。常见做法包括 warmup、余弦衰减、分段衰减和线性衰减。它们不是装饰性技巧，而是在不同训练阶段重新安排步幅：早期避免不稳定，中期保持推进，后期更细地靠近低损失区域。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/41_optimization_and_training_deep_revised__mermaid_05.png)

> 图41-5 学习率与训练行为
> 学习率控制训练节奏。它直接影响模型是在有组织地推进，还是在昂贵地震荡。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/41_optimization_and_training_deep_revised__mermaid_06.png)

> 图41-6 学习率调度是在控制不同阶段的训练节奏

---

## 6. 优化器是在处理带噪地形

SGD、Momentum、Adam、AdamW 这些优化器，不只是框架配置项。它们是在回应同一个现实：损失地形不平坦，梯度信号带噪，不同参数方向的尺度差异也很大。

SGD 使用当前梯度直接更新，简单、透明，但对学习率和噪声敏感。Momentum 引入动量，试图减少来回摆动，让更新方向带有历史惯性。Adam 进一步维护一阶和二阶矩估计，对不同参数方向使用自适应步幅。AdamW 则把权重衰减从 Adam 的自适应更新中解耦出来，在大模型训练中很常用。

简化写法如下。

SGD：

$$
\theta_{t+1}=\theta_t-\eta g_t
$$

Momentum：

$$
v_{t+1}=\beta v_t + (1-\beta)g_t,\quad \theta_{t+1}=\theta_t-\eta v_{t+1}
$$

Adam：

$$
m_t \leftarrow \beta_1 m_{t-1} + (1-\beta_1)g_t,\quad
v_t \leftarrow \beta_2 v_{t-1} + (1-\beta_2)g_t^2
$$

这些式子不需要在这里全部推导。读者需要抓住的是：优化器不是改变学习目标，而是改变利用梯度的方式。不同优化器会影响收敛速度、稳定性、泛化行为和资源效率。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/41_optimization_and_training_deep_revised__mermaid_04.png)

> 图41-4 不同优化器的差异，更像不同的前进策略

---

## 7. 训练稳定性来自多项设置共同作用

训练是否稳定，不只取决于优化器名称。初始化、归一化、batch size、学习率、权重衰减、梯度裁剪、数据顺序和数值精度都会共同影响结果。

例如，初始化尺度不合适，前向激活和反向梯度可能很快失衡；归一化层可以缓解尺度漂移，却也引入和 batch、精度、分布有关的新约束；梯度裁剪可以限制异常更新，但裁得过重也会压制有效学习；权重衰减能帮助控制参数规模，却需要和优化器配合。

训练工程的难点在于，这些设置不是独立旋钮。调高 batch size 可能需要重新调学习率；换成混合精度可能需要关注 loss scaling；更改数据清洗和采样策略，也可能改变梯度噪声结构。

所以训练稳定性不是某个技巧单独给出的，它是目标、数据、优化器、学习率、数值精度和系统实现共同形成的状态。

---

## 8. loss 下降不等于任务完成

训练曲线下降，说明模型在当前损失函数和当前数据条件下做得更好，但它不自动说明真实任务已经完成。

loss 下降可能意味着：

- 模型更会拟合训练集
- 代理目标被更好地满足
- 当前数值过程比较顺利
- 某些高频样本被更好处理

它不必然意味着：

- 泛化能力提升
- 线上表现更稳
- 用户体验改善
- 高风险错误减少
- 长尾场景被覆盖

一个模型在训练集和验证集上的 loss 都下降，却仍可能在真实业务中频繁失手。原因可能是训练目标和业务目标不一致，数据分布已经变化，评测集覆盖不足，或者模型学会了训练数据中的捷径。

因此，成熟训练流程不会只盯 loss 曲线。它还要看验证集切片、分布外测试、鲁棒性、校准、业务指标、安全约束和线上反馈。优化是能力形成过程的一部分，不是产品质量的全部证明。

---

![插图 I-19 训练不是一条线，而是资源燃烧场]({{ site.baseurl }}/ai_ecosystem_book/images/illustration_i_19.png)

> 插图 I-19 训练不是一条线，而是资源燃烧场

## 9. 训练也是资源调度问题

到大模型训练阶段，优化已经不只是算法问题。它和显存、通信、checkpoint、混合精度、数据加载、容错和实验管理紧密绑在一起。

例如：

- batch size 会受到显存限制
- 分布式训练会引入梯度同步开销
- activation checkpointing 会用更多计算换更少显存
- 混合精度提升吞吐，也带来数值稳定性风险
- checkpoint 策略决定故障恢复成本和实验可复现性
- 数据管线如果跟不上，会让昂贵计算设备等待输入

这说明训练策略的流行，常常不只因为它在纸面上更优雅，更因为它在真实算力条件下更划算、更稳定、更容易复现。现代训练是优化理论、数值计算和系统工程共同作用的结果。

![Mermaid 图]({{ site.baseurl }}/ai_ecosystem_book/images/mermaid_png/41_optimization_and_training_deep_revised__mermaid_07.png)

> 图41-7 优化在工程里同时也是一场资源调度问题

---

## 10. 本章收束

优化解决的是如何把梯度方向信号组织成持续参数更新。损失函数规定模型把什么当作改进，梯度下降把高维搜索改写成前向、损失、反向、更新的训练闭环，mini-batch 则让这个闭环变得可承受，同时也让方向信号带上采样噪声。学习率、调度策略和优化器，决定系统怎样使用这些方向信号。

训练是否成立，还要看初始化、归一化、裁剪、权重衰减、数值精度和系统实现能否共同维持稳定。loss 下降只是过程信号，不能替代泛化、业务指标、风险和线上反馈。到了大规模训练阶段，优化同时也是资源调度：显存、通信、checkpoint 和数据管线都会参与塑造最终结果。

---

## 延伸阅读

- 推荐起点：Goodfellow 等《Deep Learning》中关于优化与训练的章节，以及 Kingma 和 Ba 的 Adam 论文。
- 继续延伸：如果你想把这一章放回大模型训练系统中，建议继续追 AdamW、learning rate schedule、batch size scaling、gradient clipping、checkpointing、generalization gap。
- 检索关键词：`SGD`、`AdamW`、`learning rate schedule`、`batch size scaling`、`gradient clipping`、`generalization gap`。
