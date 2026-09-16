# 设计思想谱系

> 本文档记录异质的方法启发，按设计问题而不是统一论文模板组织。某篇论文可能只有一句，也可能有完整小节，深度取决于它对当前推理链的实际贡献。

## 1. 为什么传输内部表示？

### split computing 与 feature transmission

[BottleNet++](https://arxiv.org/abs/1910.14315) 联合设计 feature compression、不可训练信道与 feature reconstruction，用于 device–edge co-inference。它的相关性不在生成图像质量，而在于证明中间模型表示可以成为合法通信对象，且信道条件可以纳入其传输。

[BottleFit](https://arxiv.org/abs/2201.02693) 研究 split computing 的紧凑 bottleneck representation，帮助区分经常被混为一谈的两个目标：为下游任务保存足够信息，与为高忠实图像重建保存足够信息。

对 GLINT 而言，下游消费者不是 classifier，而是冻结生成 decoder，因此 decoder compatibility 与 spatial fidelity 成为中心。

### 生成 autoencoder 与 latent interface

[Latent Diffusion Models](https://arxiv.org/abs/2112.10752) 建立 learned VAE latent 作为大型图像生成器工作空间的实践基础。GLINT 采用更强接口约束：通信表示应留在该原生空间。

[Deep Compression Autoencoder](https://arxiv.org/abs/2410.10733) 及后续工作显示，激进空间压缩、重建质量与下游 diffusion 行为不能压缩成同一个标量目标。这支持 GLINT 对通信码率、解码忠实度与生成器兼容性的分离。

[Diffusion Transformers with Representation Autoencoders](https://arxiv.org/abs/2510.11690) 探索 semantic foundation-model feature 与 learned decoder 的组合。它提供一个边界案例：语义丰富的表示可能有利于生成，却仍需额外机制恢复低层来源细节。

## 2. 冻结 prior 如何接收新的空间证据？

[ControlNet](https://arxiv.org/abs/2302.05543) 证明 locked text-to-image diffusion model 可通过训练式 residual path 接收空间条件，并以中性初始化保护基模型。可迁移原则是一个中性的外部 control seam；仅有 zero initialization 不能保证训练后的来源忠实。

[T2I-Adapter](https://arxiv.org/abs/2302.08453) 展示多种空间控制的轻量 feature adapter；[IP-Adapter](https://arxiv.org/abs/2308.06721) 分离 image-prompt attention 与 text attention。二者支持在冻结 prior 周围建立小型 condition path 的可行性，但条件和目标不同于通信恢复。

[OminiControl](https://arxiv.org/abs/2411.15098) 复用 pretrained DiT 编码图像条件，并通过 multimodal attention 交互。它帮助形成这样的认识：大型冻结 transformer 本身已经包含有用 condition representation；但其控制任务不能证明 paired source restoration 或 anchor protection。

[OminiControl2](https://arxiv.org/abs/2503.08280) 动态压缩长 condition input，并跨 denoising step 复用 feature。它与效率和缓存相关，而不是 GAPF 科学正确性的证据。

## 3. 为什么从 observation 而不是纯噪声开始？

[SDEdit](https://arxiv.org/abs/2108.01073) 向用户图像加噪，再由生成 prior 去噪，提供清晰的 realism–faithfulness 控制先例。

[I²SB](https://arxiv.org/abs/2302.05872)、InDI 与 [ResShift](https://arxiv.org/abs/2307.12348) 建立从 degraded observation 到 clean image 的显式路径。共同启发是：结构化 observation 可以定义比从纯噪声重新生成更短、更有信息的 transport path。

[Posterior-Mean Rectified Flow](https://arxiv.org/abs/2410.00418) 先预测 posterior-mean restoration，再将其 transport 到更具感知真实性的分布。它强烈支持 fidelity-oriented anchor 与 generative transport 的分离，尽管 GAPF 的 anchor 与训练协议不同。

## 4. 为什么证据必须贯穿轨迹？

[ILVR](https://arxiv.org/abs/2108.02938) 在 diffusion state 中反复使用 reference image，说明参考信息可以贯穿 denoising，而不是只提供一次。

已知 operator 方法，如 [DDRM](https://arxiv.org/abs/2201.11793)、[DPS](https://arxiv.org/abs/2209.14687)、[DDNM](https://arxiv.org/abs/2212.00490) 与 [DiffPIR](https://arxiv.org/abs/2305.08995)，也会在 sampling 或 proximal update 中持续激活 measurement。

这些公式不能直接复制：GLINT 是学习式、非线性、随机 observation chain，并不具备若干方法假设的精确线性 operator 或 pseudo-inverse。GAPF 迁移的是 prior/evidence 分离，而不是它们的解析保证。

## 5. 为什么分离 degradation removal 与 information regeneration？

[DiffBIR](https://arxiv.org/abs/2308.15070) 把 blind restoration 明确分成 degradation removal 与 information regeneration。恢复 condition 提供内容，冻结 diffusion prior 生成真实细节；这一分解与 GAPF 职责分离高度相似。

[StableSR](https://arxiv.org/abs/2305.07015)、SeeSR 与 [SUPIR](https://arxiv.org/abs/2401.13627) 展示多种以大型冻结或近似冻结 diffusion prior 处理真实恢复的方式，也暴露计算成本与 hallucination 风险：强视觉增强不自动等于 paired fidelity。

它们对 GAPF 的意义是双向的：既支持冻结 prior 与空间证据的价值，也促使方法加入显式 source-bound trust 机制。

## 6. 如何划分传输内容与生成纹理？

[Lossy Image Compression with Conditional Diffusion Models](https://arxiv.org/abs/2209.06950) 传输 content latent，并让 diffusion decoder 合成 texture variable，是分离通信内容与接收端生成细节的直接概念先例。

[DiffEIC](https://arxiv.org/abs/2404.18820) 压缩 content variable，将其与 diffusion space 对齐并注入冻结 Stable Diffusion。它是 GLINT/GAPF 特别相关的相邻工作，因为连接了紧凑表示、latent alignment 与 frozen-prior completion。

仍需检验的区别是：接收表示究竟只是 condition，还是显式受保护的 inference anchor。

## 7. 应如何使用这份谱系

这些论文扮演不同角色：

- **历史启发**：有同期证据证明它确实影响原设计；
- **后续理论支持**：在设计形成后帮助解释它；
- **最近对比**：解决足够相似的通信任务；
- **边界或负面证据**：说明机制为什么不能无条件迁移。

最终仓库必须诚实标注这些角色。后来发现的论文不能被追溯性地写成最初灵感来源。
