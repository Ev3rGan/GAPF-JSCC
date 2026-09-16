# 待作者审阅的候选论文

> 状态：初步候选队列，不是最终 bibliography。出现在本文件中不等于一定在论文中引用，也不表示认可其全部主张。

## 优先级 A：最可能改善中心方法解释

### SDEdit

- 官方来源：<https://arxiv.org/abs/2108.01073>
- 阅读问题：向 guide image 加噪如何形式化 realism–faithfulness trade-off？
- 潜在用途：解释 GAPF 的 anchor-noise 起点，并区分一次性初始化与持续证据约束。

### ILVR

- 官方来源：<https://arxiv.org/abs/2108.02938>
- 阅读问题：在反向轨迹中反复约束 reference information 带来什么？
- 潜在用途：说明为什么 GAPF 不能只依赖终端修正。

### Lossy Image Compression with Conditional Diffusion Models

- 官方来源：<https://arxiv.org/abs/2209.06950>
- 阅读问题：如何分离传输的 content variable 与 decoder 合成的 texture？
- 潜在用途：强化信道与 prior 之间的信息职责陈述。

### DiffEIC: Towards Extreme Image Compression with Latent Feature Guidance and Diffusion Prior

- 官方来源：<https://arxiv.org/abs/2404.18820>
- 阅读问题：压缩 content variable 如何与冻结 diffusion space 对齐并注入？
- 潜在用途：识别 GLINT/GAPF 在压缩方向最接近的先例，并明确剩余的 anchor-protection 差异。

### BottleNet++

- 官方来源：<https://arxiv.org/abs/1910.14315>
- 阅读问题：端到端 feature JSCC 对通过带噪信道传输神经网络中间表示建立了什么？
- 潜在用途：恢复 DNLT 与 GLINT 更早的概念祖先。

## 优先级 B：表示与生成器接口基础

### Deep Compression Autoencoder

- 官方来源：<https://arxiv.org/abs/2410.10733>
- 阅读问题：空间压缩、重建精度与 diffusion 效率如何相互影响？
- 潜在用途：解释为什么仅有 latent size 不能定义有用通信表示。

### DC-AE 1.5

- 官方来源：<https://arxiv.org/abs/2508.00413>
- 阅读问题：为什么重建更好的 latent 仍可能难以被 diffusion model 学习？
- 潜在用途：支持 decoder reconstruction 与 generative compatibility 的区分。

### Diffusion Transformers with Representation Autoencoders

- 官方来源：<https://arxiv.org/abs/2510.11690>
- 阅读问题：semantic representation encoder 替换传统 VAE 后获得与损失了什么？
- 潜在用途：将 generator-native VAE latent transmission 与 foundation-model feature space 区分开。

### Both Semantics and Reconstruction Matter

- 官方来源：<https://arxiv.org/abs/2512.17909>
- 阅读问题：如何在紧凑表示中协调语义与像素重建目标？
- 潜在用途：为 GLINT 复合质量框架提供后续支持；没有同期证据时不能写成原始历史启发。

## 优先级 C：效率与实现视角

### BottleFit

- 官方来源：<https://arxiv.org/abs/2201.02693>
- 阅读问题：哪些 bottleneck 设计能在 split-computing 约束下保留下游任务效用？
- 潜在用途：对比 task-feature transmission 与忠实 generator-latent reconstruction。

### OminiControl2

- 官方来源：<https://arxiv.org/abs/2503.08280>
- 阅读问题：何时可以压缩 condition feature 并跨 denoising step 复用？
- 潜在用途：讨论 TSET/condition path 效率；不能作为 GAPF 性能的直接证据。

## 已在本地保存但尚未充分整合

下列工作已有本地证据或全文材料，但需从任务型矩阵转入持久叙事：

- OminiControl；
- DiffBIR；
- StableSR；
- SUPIR；
- DDRM、DPS、DDNM 与 DiffPIR；
- I²SB、InDI、ResShift 与 PMRF；
- ControlNet、T2I-Adapter 与 IP-Adapter。

重点不是重新下载，而是核验具体相关命题、判断它是历史启发还是后续支持，并放入正确的设计问题。

## 阅读后的决策标签

- `CORE-CITE`：novelty、问题定义或直接对比所必需；
- `GENEALOGY`：对设计历史重要，但未必进入紧凑 Related Work；
- `BOUNDARY`：主要用于解释机制为什么不能直接迁移；
- `BACKGROUND`：有助读者理解，但不是中心论证所必需；
- `EXCLUDE`：与最终叙事没有足够具体的关系。
