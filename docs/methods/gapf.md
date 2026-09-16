# GAPF：由 GLINT anchor 约束的后验流

> 状态：最终 Stage 2 架构与训练图已经冻结；完整正式端到端对比与匹配机制消融仍未闭合。

## 1. GAPF 定义的问题

GAPF 研究：当接收端已经拥有带噪、源特定 latent anchor 时，应当如何使用冻结生成先验？

anchor 包含信道送达的证据；prior 包含关于合理图像的统计知识。二者不是可互换的信息源：

- anchor 应控制来源身份和空间结构；
- prior 应在接收证据不足处补充合理细节；
- 学习式接收模块应决定二者如何组合，而不是学习一个替代生成器。

## 2. 后验职责模型

GAPF 用下式表达预期职责划分：

\[
p(z\mid a_s,s,p)
\propto p_{\mathrm{prior}}(z\mid p)\,p_{\phi}(a_s\mid z,s).
\]

该公式用于指导架构，而不是宣称存在解析 GLINT likelihood。GAPF 是围绕冻结 rectified-flow prior 构造的 anchor-constrained 学习式近似。

## 3. 单样本执行时间线

```text
1. 接收 GLINT anchor a_s 与信道状态 s。
2. 在固定 scheduler 时刻将 anchor 与接收端生成噪声混合。
3. 将状态 pack 为冻结 flow transformer 的 token 表示。
4. 在每个 solver step：
   a. 从 packed anchor、SNR 与当前噪声等级产生空间证据；
   b. 在选定 transformer block 注入该证据；
   c. 获得冻结 prior direction；
   d. 将其转换为 clean endpoint estimate；
   e. 根据信道状态，把该估计相对 anchor 重新耦合；
   f. 将耦合估计转回 velocity，并执行 scheduler update。
5. unpack 最终冻结 prior endpoint z_F。
6. 根据 a_s 与 z_F 估计 decoder-aligned multiscale direction。
7. 应用 source gate，并对动作归一化。
8. 将动作投影到有界 trust region。
9. 加上有界终端动作，再由冻结 VAE 解码。
```

当前实现使用完整十二步冻结 prior 轨迹、一个选定 TSET 注入位置、紧凑多尺度 direction body，以及四个标量 SNR controller 参数。这些是实现事实，不是普遍最优主张。

## 4. 机制一：从 anchor 开始的轨迹

GAPF 从 anchor–noise interpolation 开始，而不是从纯高斯噪声开始：

\[
r_0=P\bigl((1-\tau)a_s+\tau\epsilon\bigr),
\]

其中 `P` 是冻结 latent pack operator，`epsilon` 是接收端生成的噪声。

这样做是为了保留从生成状态返回已传输来源的显式路径。prior 从接收证据附近出发并补全它，而不是仅凭外部 condition 重新生成完整来源。

## 5. 机制二：轨迹空间证据

在每个 solver step，Tiny Spatial Evidence Transducer（TSET）接收 packed anchor、信道状态和当前噪声等级，并在冻结 transformer 内注入轻量空间 residual。

TSET 的存在是因为 endpoint-only correction 无法让 prior 的中间预测感知来源。它的职责是在 prior 形成全局与局部结构时暴露接收空间证据。

TSET 不替代 prior。完整冻结 transformer 与 scheduler 始终位于推理路径上。

## 6. 机制三：anchor–prior coupling

冻结 prior direction 首先转换为 endpoint estimate `z^F_0`，然后 GAPF 相对 anchor 对该估计插值：

\[
z^P_0=a_s+\beta(s)\bigl(z^F_0-a_s\bigr).
\]

`beta(s)` 控制在给定信道状态下接受多少 prior innovation。它不同于把 anchor 当作普通 prompt：anchor 是 prior 被允许移动时的参照点。

耦合 endpoint 再转换回 unchanged scheduler 消费的 velocity。因此证据约束作用于完整轨迹内部，而不只发生在轨迹结束后。

## 7. 机制四：decoder-aligned 终端 direction

冻结 prior endpoint 仍可能包含在 VAE 解码后被不成比例放大的误差。紧凑 direction body 将 anchor–endpoint residual 分解为低、中、高频成分，再合成与解码图像质量对齐的 direction。

该终端模块与 TSET 职责不同：

- TSET 影响 prior 如何构造轨迹；
- terminal direction 在轨迹完成后修正 endpoint 残余误差。

因此二者应通过匹配消融分别评估，不能作为可互换 condition module 处理。

## 8. 机制五：source gate 与 trust bound

source-dependent gate 抑制当前 anchor–endpoint 关系不支持的修正。原始动作先做 RMS normalization，再投影：

\[
\delta
=\widetilde\delta
\min\left(1,
\frac{\kappa\,\operatorname{RMS}(a_s-z_F)}
{\max(\operatorname{RMS}(\widetilde\delta),\epsilon)}
\right).
\]

这给出终端 displacement 相对 anchor–prior residual 的显式上界。该上界是动作的数学性质，但不能单独证明 direction 有益；后者仍是经验问题。

## 9. 机制六：单调信道状态控制

两个有界标量函数跨 SNR 协调 posterior coupling 与终端尺度，其单调参数化编码预期职责变化：

- 信道更可靠时，保留更多 anchor 证据；
- 信道较差时，在相同边界内允许 prior 提供更多帮助。

信道状态控制服务于 GAPF，但不是独立贡献，不应被写成另一个方法。

## 10. 为什么预期 GAPF 有效

GAPF 针对 SACRA 暴露的具体失败：

- 将接收 latent 从外部 condition 提升为 inference anchor；
- 让来源证据在轨迹中发挥作用，而不只在轨迹前后出现；
- 相对 anchor 表达 prior action；
- 分离轨迹构造与终端修正；
- 限制最终学习动作；
- 保持强生成器冻结，使学习式接收端轻量且可诊断。

中心思想不是“更多 conditioning”，而是明确分配接收证据与生成先验的职责。

## 11. 已证明、可解释与待验证

### 由构造直接保证

- trust projection 对终端动作施加 norm bound；
- 冻结生成器保留在完整轨迹上；
- 关闭学习动作可被定义为精确回退到选定 anchor/prior 路径；
- 最终训练图中只有声明的轻量接收模块可训练。

### 有机制证据支持或属于设计解释

- 轨迹注入可以向 prior 暴露空间来源证据；
- anchor-relative coupling 可以减少无约束 prior drift；
- decoder-aligned terminal correction 可以针对视觉重要的 endpoint 误差。

### 仍需完整正式证据

- 相对所有匹配重建型和生成型基线的优越性；
- fidelity–perception trade-off 在哪些工作条件下改善或不改善；
- TSET、terminal direction、source gate、trust bound 与 SNR control 的组件归因；
- 完整 cohort 的定性与分布性结果。

在这些评测闭合前，GAPF 应被表述为一个已经冻结、由证据推动的方法，其最终性能主张仍待验证；不能写成已经被完全证明的 state of the art。
