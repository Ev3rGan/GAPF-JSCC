# 从 DNLT 到 GLINT

## 1. 这不是一次 backbone 替换

从 DNLT 到 GLINT 很容易被误解为“用另一个 encoder–decoder 替换 Mamba 网络”。这种描述遗漏了真正的科学变化。

DNLT 问的是：如何把现有 JSCC 架构适配到生成器 latent？GLINT 问的是：在精确码率和 side-information 约束下，固定生成 decoder 需要什么样的通信接口？

因此，这一转变是从**架构迁移**走向**接口与目标设计**。

## 2. DNLT 为转变提供了什么

DNLT 让问题变得具体：

- 传输对象可以是 FLUX VAE latent map；
- MambaJSCC 可以把该表示送过模拟带噪信道；
- 接收结果可由冻结 VAE 解码；
- exact CBR 与 latent-domain head 的选择可以被测量。

没有这份真实实现，后续关于 generator-native 通信接口的主张就缺少真实基线和已知集成路径。

## 3. 暴露出的概念 mismatch

普通架构调参之后仍存在三类问题。

### 3.1 目标 mismatch

latent-space reconstruction loss 把坐标误差当作目标；下游 decoder 却会把不同 latent 方向转换为感知重要性不等的图像误差。因此，改善通用 latent metric 不保证改善最终解码图像。

### 3.2 表示 mismatch

适配输入/输出 head 可以让 backbone 的 tensor shape 兼容，但 shape compatibility 弱于 interface alignment。通信变换还应保护冻结 decoder 所期望的空间与通道组织。

### 3.3 主张 mismatch

一个可工作的 latent-domain MambaJSCC 证明可行性，却不能证明增益来自 Mamba、某一学习式层级最优，或输出已经是冻结生成器的最佳 anchor。

## 4. GLINT 如何重定义每个问题

| 以 DNLT 为中心的问题 | 以 GLINT 为中心的问题 | 对应设计 |
|---|---|---|
| 如何让 MambaJSCC 适配 latent tensor？ | 什么表示应穿过信道？ | 传输冻结生成器的原生 latent |
| 如何重建 latent 数值？ | 哪些误差在解码后真正重要？ | decoder-aligned 复合失真 |
| 如何压缩 latent map？ | 如何暴露局部多尺度结构而不任意混合通道？ | channel-tied separable lifting |
| 名义输出大小是否正确？ | 运行时通信预算是否精确？ | 显式 complex-use 与 CBR 契约 |
| 辅助信息能否帮助？ | 究竟有哪些源相关信息跨过了信道？ | 零源相关 side-information 边界 |

## 5. 证据驱动的收窄

最终方法比早期 GLINT 设想更窄：

- 无约束 learned lifting 不稳定，最终保留 channel-tied separable 形式；
- 受约束 lifting 只有小幅补充贡献，不能解释完整增益；
- nested 高码率增强优于低码率 base，却逊于匹配 flat 高码率模型；
- decoder-aligned 训练得到支持，但 GLINT 不声称识别了显式 decoder tangent subspace；
- 所选紧凑 backbone 有效，但任意 backbone 可交换性尚未测试。

这些负面与局部结论属于最终方法定义。GLINT 的权威性来自主张被收窄到证据实际支持的范围。

## 6. 保留了什么，替换了什么

### 从 DNLT 保留

- 以生成器 VAE latent 作为传输域；
- 真实无线信道与复符号预算；
- 接收端 VAE 解码；
- 信道状态感知；
- DNLT 作为匹配重建 comparator。

### 被 GLINT 替换

- architecture-first 的问题表述；
- 以通用 latent fidelity 作为主要质量定义；
- 主要由图像域 backbone 继承的 latent topology；
- 隐式而非显式的 side-information 与 exact-rate 主张。

## 7. 最终解释

GLINT 没有证明 DNLT 是无效模型，而是建立了更有用的命题：

> 当冻结生成 decoder 定义接收端图像空间时，通信系统应围绕该 decoder 的原生表示、精确信道契约和解码图像失真设计，而不只是围绕一个强 JSCC backbone 是否可用来设计。
