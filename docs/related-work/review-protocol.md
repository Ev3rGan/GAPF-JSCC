# 相关工作综述协议草案

> 状态：已批准的工作框架，但尚未完成检索闭合；不声称已达到 PRISMA 式完整性。

## 1. 两种不同的综述目标

文献工作不应强迫所有相关论文进入一个同质系统综述。

### Track A：以通信为中心的核心综述

目的：

- 建立 novelty 边界；
- 识别最近方法与公平基线；
- 支撑论文 Introduction 与 Related Work 中的主张；
- 为生成式图像语义通信提供可复核覆盖。

该轨道应采用声明过的 database/query/inclusion protocol，并可在边界明确后称为该范围内的系统综述。

### Track B：跨领域设计思想谱系

目的：

- 重建 GLINT 与 GAPF 的真实思想路径；
- 纳入 diffusion control、image restoration、inverse problem、split computing、feature compression 与 representation autoencoder；
- 保存那些重要但不足以定义完整可比系统的小思想。

该轨道是经过证据支撑的策展式谱系，应当广泛且透明，但不声称穷尽所有相邻领域。

## 2. Track A 检索问题

1. 传输的是 pixel、learned JSCC feature、semantic latent、generator latent、text，还是混合 condition？
2. 接收端在调用生成器前先重建什么？
3. 生成模型是联合训练、适配还是冻结？
4. 接收信道证据如何进入生成过程？
5. 哪些源相关 side information 被排除在声明码率之外？
6. fidelity、perception 与 distributional realism 如何评估？
7. 哪些对比在 CBR、SNR、dataset 与 receiver prior 上匹配？

## 3. Track A 检索块

最终检索式应组合以下词块：

- `joint source channel coding`、`deep JSCC`、`semantic communication`；
- `image`、`visual`、`wireless`；
- `generative`、`diffusion`、`flow`、`latent`、`semantic representation`；
- `perceptual`、`rate-distortion-perception`、`posterior`、`received signal`。

必须为每个数据库调整语法，并保存检索日期与返回数量。候选索引包括 IEEE Xplore、arXiv、OpenAlex、Crossref、Semantic Scholar，以及从最近邻论文进行引文追踪。最终协议必须区分数据库发现与元数据核验。

## 4. 纳入原则

当工作实质改变以下至少一项时，纳入核心综述：

- 传输表示；
- 通信预算或信道模型；
- 接收端生成式重建；
- 证据 conditioning 机制；
- fidelity–perception 目标；
- GLINT/GAPF 最近直接对比。

排除或降级：

- 没有提供本文所用 method-level 命题的通用 semantic-communication survey；
- 除非提供明确可迁移机制，否则不纳入非图像模态；
- 没有通信表述的通用 diffusion-control 工作，应进入 Track B；
- 无法核验、不能支撑具体命题的不可访问主张。

## 5. 可变深度综合

不要求统一内容卡片，深度随相关性变化：

- **最近系统**：问题、完整数据流、码率计量、训练范围、结果、局限与直接差异；
- **机制先例**：相关机制、证据、迁移条件与不可迁移假设；
- **概念启发**：真正改变项目推理的单个思想；
- **边界证据**：阻止直接复用的失败模式或假设；
- **背景**：只给简明上下文。

引文元数据可以标准化，但科学分析不应被迫使用相同模板。

## 6. Track B 获取方式

设计思想谱系应综合：

- 带日期的项目笔记与实现历史；
- 作者回忆，并在获得同期证据前标为 recollection；
- 前向与后向引文追踪；
- 围绕机制而非只围绕项目术语的定向检索；
- 设计形成后才出现的解释性论文，并明确标为 later support。

Track B 可以纳入晚于原设计发表的论文，但没有同期证据时不能称为历史启发。

## 7. 核验记录

每个进入任一轨道的命题都应记录：

- paper identifier 与 version；
- 支撑命题的具体 section、equation、figure 或 experiment；
- 是否阅读全文；
- 该命题是论文原结论，还是本项目推断；
- 相关角色与迁移边界；
- 人工核验状态与日期。

## 8. 最终输出

综述工作最终应产生：

- 可复核 search log；
- 去重后的 literature catalogue；
- 以通信为中心的叙事综合；
- 以问题为中心的设计思想谱系；
- 仅在重复字段确实可比时建立的最近邻对比表；
- 带显式 include/exclude 决策的候选列表；
- 经核验的 BibTeX 文件。
