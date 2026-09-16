# SACRA：空间通信残差适配

> 状态：历史 Stage 2 实现与生成式 comparator。SACRA 证明接收条件可以因果影响完整冻结 prior 路径，但没有充分保护直接 GLINT anchor。

## 1. SACRA 试图解决的问题

Stage 1 重建通信失真的 latent 后，接收端希望借助强冻结生成器恢复感知细节。最初的工程问题是：如何在不重新训练完整 backbone 的前提下，让生成器响应接收到的图像条件？

SACRA 将 GLINT 重建和信道状态编码为空间条件，再通过 residual attention path 注入多个 transformer block。low-rank adaptation 修改部分生成器映射，而主生成器保持冻结。

## 2. 简化计算

对条件 encoder `E_cond`、block state `H_l` 和 residual attention adapter，核心可写为：

\[
C=E_{\mathrm{cond}}(a_s,s),
\qquad
H_l=T_l(H_{l-1})+\alpha_l\operatorname{Attn}(Q_l,K(C),V(C)).
\]

GLINT 输出作为外部条件出现。生成器仍执行完整生成轨迹，条件可在多个深度影响该轨迹。

## 3. SACRA 成功建立的事实

受控诊断支持数项重要结论：

- condition path 确实活跃，而不是被忽略；
- 图像身份与空间对齐会影响响应；
- 模型确实使用显式信道状态；
- 多个注入区域对完整生成路径具有可测的因果影响。

这是实质性成果。它排除了“Stage 2 失败只是因为冻结生成器没有看到或没有使用接收条件”的简单解释。

## 4. 中心局限

使用条件不等于保护证据。

SACRA 让生成器能够访问 GLINT 重建，但没有把该重建设为显式 state anchor，也没有限制生成器可以偏离它多远。因此，强条件响应可以与更差的最终 endpoint 同时出现。

推动下一阶段的诊断结论是：

> SACRA 可以强烈使用来源与信道条件，同时产生比直接解码 GLINT anchor 更差的最终重建。

这改变了科学问题。问题不再是如何增强 condition path，而是如何在允许强 prior 生成缺失细节的同时，阻止它覆盖来源证据。

## 5. 为什么继续搜索注入位置不足以解决问题

SACRA 同时耦合了多项不确定性：

- anchor 的哪些部分可靠；
- 冻结 prior 应补充什么细节；
- 哪些 block 应接收条件；
- 每处注入应该多强；
- low-rank adaptation 是否改变 prior 本身；
- 更低 flow-prediction loss 是否改善解码 endpoint。

若输出退化，继续增加 attention 位置或 condition encoder 容量并不能说明究竟是哪项职责出错。条件编码、位置、强度与 backbone 适配共同变化，使机制归因变得困难。

## 6. 在最终项目中的角色

SACRA 仍然有四项价值：

- V1 的 Stage 2 方法；
- 证明接收条件可以因果驱动完整冻结生成路径；
- 最终接收端的生成式 comparator；
- 区分“condition influence”与“source-constrained restoration”的 counterfactual。

因此，转向 GAPF 不是因为 SACRA 无效，而是因为提出了更严格要求：接收 anchor 必须在推理状态和 endpoint 中获得明确、受保护的角色。

## 7. 主张边界

允许表述：

- 受控诊断中，SACRA 条件会影响完整生成器路径；
- 空间对应与信道状态均参与该影响；
- 强因果影响并不保证 endpoint 优于直接 GLINT。

不允许表述：

- 把诊断子集当作最终论文性能；
- 声称每个 SACRA 注入位置都有益；
- 推断所有冻结 prior conditioning 都无效；
- 在匹配正式评测前，用 SACRA 的局限证明 GAPF 更优。
