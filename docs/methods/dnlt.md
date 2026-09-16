# DNLT：将 MambaJSCC 迁移到生成器 latent

> 状态：历史 Stage 1 方法与重建型 comparator。DNLT 不是最终 Stage 1 贡献，但它是真实实现，也是方法演进链不可缺少的一环。

## 1. DNLT 要解决什么问题

DNLT 研究：能否把已经成立的图像域 JSCC backbone 迁移为大型接收端图像生成器原生 latent map 的传输系统。

其源码直接组合官方 MambaJSCC 的 encoder、decoder、channel 与 channel-adaptation 模块。它既不是只在外形上模仿 MambaJSCC，也不是继承一个名义接口后替换内部计算。DNLT 改变进入和离开模型的表示，同时保留原通信 backbone 作为主变换。

## 2. 端到端数据流

```text
来源图像
  → 冻结 FLUX VAE encoder
  → 缩放后的二维 latent map
  → latent-aware MambaJSCC encoder
  → 功率归一化复符号
  → AWGN 信道
  → 带 latent 输出头的 MambaJSCC decoder
  → 重建 FLUX latent
  → 冻结 FLUX VAE decoder
  → 重建图像
```

实现保留二维 latent map，而不是将其打包成无关的 token 序列。官方 MambaJSCC 路径只在图像域假设不再符合 latent 接口的位置进行适配。

## 3. 主要改动

### 3.1 生成器原生输入与输出

来源图像由冻结 FLUX VAE 按官方 scale/shift 约定编码。因此，DNLT 传输的是接收生成器所消费的同一类 latent 对象。

原 decoder 的终端图像投影被 latent-domain 输出头替代，重建 latent 可直接交给冻结 VAE 解码。

### 3.2 latent-aware 边界头

迁移后的实现包含可选 latent normalization、局部卷积输入/输出头、latent-aware 信道边界投影与 channel power loading。这些改动处理 RGB 图像与多通道生成 latent 的差异，但没有抛弃 MambaJSCC backbone。

### 3.3 通信契约

实现显式计算复信道使用数，在运行时检查目标 CBR，执行功率归一化，并支持信道状态适配。因此 DNLT 是等预算的真实 comparator，而不是名义上的 latent autoencoder。

### 3.4 训练目标

实现可通过冻结 VAE 同时监督 latent fidelity 与解码图像质量。历史版本曾探索 latent loss、image loss、感知项以及不同 latent-domain head。

## 4. DNLT 已经建立的事实

DNLT 建立了四项重要能力：

1. 官方 MambaJSCC 计算可以复用于生成器 latent 的无线传输；
2. FLUX VAE latent map 经过带噪复符号链路后仍能支持图像重建；
3. 可在不替换完整 backbone 的前提下整合 latent-domain 输入/输出适配与 exact CBR 计量；
4. 可独立于后续生成式接收端评估生成器 latent 通信基线。

这些是正面能力。DNLT 不应被描述为一个失败模型。

## 5. 为什么 DNLT 不是最终 Stage 1 定义

DNLT 从架构迁移问题出发：如何把 MambaJSCC 适配到 VAE latent map？后续研究问题更窄也更科学：在精确信道预算下，必须保留哪些信息，才能让固定生成 decoder 保持来源忠实？

这个区别体现在三方面。

### 5.1 latent 不是均匀的欧氏目标

不同 latent 方向上数值相同的误差，未必产生相同的解码图像误差。以 latent distance 为主的 loss 可能优先保留数值较大的坐标，却低估冻结 decoder 更敏感的方向。

### 5.2 图像域层级不自动等于生成器对齐

对 RGB 重建有效的架构仍然携带关于空间降采样、通道混合与输出头的假设。适配后这些假设可能有效，但它们与生成器原生 latent topology 的关系并不显式。

### 5.3 证明可行不等于证明接口最优

DNLT 证明迁移可行，但不能单独证明其表示、变换 topology 或训练目标就是通信系统与冻结生成器之间最合适的接口。

## 6. 在最终项目中的角色

DNLT 继续承担三项职责：

- Stage 1 设计的历史起点；
- 匹配通信预算下的重建型 comparator；
- 展示研究问题从 backbone 适配转向 generator-native 通信接口后发生了什么变化的 counterfactual。

完整转折见 [DNLT 到 GLINT](../lineage/dnlt-to-glint.md)。

## 7. 主张边界

允许表述：

- DNLT 是基于 MambaJSCC 的 latent-domain 实现；
- 它保留官方 encoder/decoder/channel 路径，同时适配 latent I/O 和相关契约；
- 它为 GLINT 提供真实重建基线。

尚未建立：

- 所有 DNLT 局限都由 Mamba 或 state-space modeling 引起；
- 仅更换 backbone 即可解决 generator-interface 问题；
- GLINT 的全部增益都能归因于某一个孤立目标或 topology 变化。
