# 核心相关工作综述

> 状态：供作者审阅的叙事框架，尚不是完成的系统综述。公开前必须冻结检索式、数据库覆盖、去重、纳排决策与引文元数据。

双轨综述方法见 [综述协议](review-protocol.md)。

## 1. 范围

本综述用于建立 GAPF-JSCC 以通信为中心的 novelty 边界，重点关注至少改变下列一项的工作：

- JSCC 系统传输的表示；
- 接收端生成模型的角色；
- 接收信道证据影响重建的方式；
- 通信预算下的 fidelity–perception 目标。

通用扩散控制与图像恢复工作单独放入[设计思想谱系](design-genealogy.md)。它们影响了方法，但不全是可直接比较的通信系统。

## 2. 重建型 JSCC 谱系

DeepJSCC 建立端到端模拟 JSCC 范式：神经 encoder 把图像映射到带噪信道符号，神经 decoder 重建像素。后续 Transformer 与 state-space 变体，包括 WITT、SwinJSCC、NTSCC 和 MambaJSCC，扩展模型容量、码率适配或信道适配。

这些方法为 DNLT 与 GLINT 提供通信基础：学习式 source/channel transform、直接在带噪信道训练、精确或受控带宽，以及接收端重建。但它们的主要输出通常仍是图像或由重建型 decoder 优化的 task-specific representation。

GAPF-JSCC 改变的是接口问题：传输对象之所以被选择，是因为它原生属于强接收端生成器；最终接收端则显式区分接收证据与生成补全。

## 3. 生成式与感知型 JSCC

生成式 JSCC 通过 adversarial、perceptual、semantic 或 diffusion prior 提升低码率视觉真实性。相关方向包括：

- 面向语义图像传输的 generative JSCC；
- 感知导向的 learned source–channel coding；
- 可控 rate–distortion–perception 通信；
- diffusion-aided 或 diffusion-driven 图像传输。

这些工作证明低码率感知通信不能只考虑 distortion，也揭示共同风险：视觉上很强的 decoder 可能合成传输来源并不支持的内容。

GAPF-JSCC 把这一风险作为一等设计约束，而不只是评测 trade-off。

## 4. 像素重建后再做生成式 refinement

一些系统先给出传统 pixel reconstruction，再用 diffusion 模型恢复细节。Diffusion-Aided JSCC 是接近的例子：JSCC 重建为冻结 Stable Diffusion 路径提供空间和语义条件。

这类工作证明强冻结 prior 与结构化接收条件有用，但也引入中间 pixel bottleneck：信息必须先经过 JSCC 图像 decoder，再重新编码进 diffusion condition space。

GLINT 直接传输接收生成器原生 latent，从而移除该中间表示转换。

## 5. 接收信号的 posterior guidance

DiffCom 把信道接收信号视为 diffusion posterior sampling 的自然条件。CommIN 将语义图像通信建模为带 diffusion guidance 的 inverse problem。相关方法还会利用已知或学习式 observation model 在推理时引导 prior。

这组文献对 GAPF 特别重要，因为它为先验知识和通信证据分配不同角色。但许多 inverse-problem 保证依赖已知线性或可微 forward operator；学习式 GLINT encoder、带噪信道和非线性 synthesis transform 并不自动满足这些假设。

因此，GAPF 借用 prior/evidence 的**职责分离**，但不声称拥有已知线性测量系统的解析 likelihood 或 projection guarantee。

## 6. latent 与语义条件传输

CASC、语言导向 semantic latent transmission、latent-feature-guided conditional diffusion、Stable Cascade 通信和 DiT-JSCC 等工作，探索更接近生成模型内部空间的表示或条件。

它们是 GLINT/GAPF 最近的概念邻居，差异必须落到精确接口：

- 传输表示是生成器原生 latent，还是单独学习的 condition？
- condition 是否空间对齐？
- 是否需要中间 pixel reconstruction？
- 生成器是冻结、适配还是联合训练？
- 接收端显式保护来源证据，还是只鼓励条件生成？
- 哪些源相关 side information 被排除在通信预算外？

最终综述应比较这些职责，而不是只根据“diffusion”“latent”或“semantic”等名称归类。

## 7. 最接近的主张差异

GAPF-JSCC 的预期差异来自两条边界的组合：

1. **generator-native communication**：GLINT 在 exact CBR 和零源相关 side information 下，直接重建冻结生成 decoder 坐标系中的接收端可观察 latent；
2. **anchor-constrained generative completion**：GAPF 在冻结 prior 的完整轨迹中，把该 latent 作为受保护的带噪证据，并限制终端学习动作。

“使用 diffusion 模型”和“传输 latent”本身都不是 novelty。主张取决于表示、通信计量、prior 职责与来源保护如何组合。

## 8. 公开前必须完成的综述协议

在把本文档称为系统或完整综述前，仓库必须记录：

- 检索的数据库与索引；
- 精确 query 与检索日期；
- 发表年份和语言边界；
- 纳入与排除标准；
- 前向与后向引文追踪；
- 重复项与版本处理；
- 哪些论文完成全文阅读；
- 哪些命题经过人工核验；
- 覆盖范围冻结日期。

现有本地语料是强 seed set，但不能证明覆盖完整。
