# 主张状态

> 状态日期：2026-09-16。本表汇总当前允许公开表述的主张边界，不能替代私有的 artifact-level evidence manifest。

本表使用的公开安全来源类别见[来源地图](source-map.md)。

## 状态词汇

- **SOURCE-VERIFIED**：已在当前实现或冻结契约中确认；
- **FORMAL-EVIDENCE**：由声明完整的正式评测协议支持；
- **DIAGNOSTIC**：可用于机制推理，但不是论文性能结果；
- **INTERPRETATION**：与证据一致的推理解释，不是直接证明；
- **PENDING**：必要证据不完整或尚未审计；
- **REJECTED/SUPERSEDED**：更强主张或早期设计已不再控制最终方法。

## 当前主张台账

| ID | 主张 | 状态 | 公开边界 |
|---|---|---|---|
| C-001 | DNLT 在适配 FLUX VAE latent I/O 的同时，直接组合官方 MambaJSCC encoder、decoder、channel 与 adaptation 模块 | SOURCE-VERIFIED | 不得把 DNLT 写成独立重写的 MambaJSCC，也不能写成只有简单继承 |
| C-002 | GLINT 在 exact CBR 和零源相关 side information 下传输接收生成器的原生 VAE latent | SOURCE-VERIFIED + FORMAL-EVIDENCE | 只适用于已审计实现与协议 |
| C-003 | decoder-aligned 复合失真是有证据支持的 GLINT 设计方向 | DIAGNOSTIC + FORMAL-EVIDENCE | 不得把完整系统全部增益归因于该目标 |
| C-004 | channel-tied separable lifting 相对匹配 fixed-lifting route 提供小幅、稳定贡献 | FORMAL-EVIDENCE | 不推广到任意 learned lifting，也不声称优于所有 stride-convolution 设计 |
| C-005 | nested 高码率 GLINT 优于 base route，但逊于匹配 flat route | FORMAL-EVIDENCE | “nested 表示资源最优”主张被拒绝 |
| C-006 | SACRA 在完整生成路径中使用来源身份、空间对齐与信道状态 | DIAGNOSTIC | 不是最终性能结论，也不能推广到所有注入组 |
| C-007 | SACRA 使用条件不保证 endpoint 优于直接 GLINT | DIAGNOSTIC | 用于推动职责重定义；不能单独证明 GAPF 更优 |
| C-008 | GAPF 使用完整冻结 prior 轨迹、轨迹证据注入和 post-solver 有界终端动作 | SOURCE-VERIFIED | 描述所选实现，不能写成普遍最优 |
| C-009 | GAPF 终端动作相对 anchor–prior residual 具有显式 norm bound | SOURCE-VERIFIED / MATHEMATICAL | 上界限制幅度，但不证明 direction 能改善质量 |
| C-010 | 最终 GAPF graph 只训练轻量接收模块；GLINT、generator、VAE 与 text encoder 均冻结 | SOURCE-VERIFIED | 只适用于已冻结的最终训练图 |
| C-011 | GAPF 在两个 CBR 上都相对所有匹配基线改善完整 fidelity–perception trade-off | PENDING | 完整正式评测定位并审计前禁止使用 |
| C-012 | GAPF 每个机制均有独立可归因收益 | PENDING | 需要相同 anchor、seed 与 cohort 下的匹配组件消融 |
| C-013 | GAPF 改善每个 SNR 或每类图像 | PENDING | 若存在负面或无改善条件，必须报告 |
| C-014 | GLINT/GAPF 可泛化到已评估 FLUX-family 实现之外 | PENDING | 方法直觉可能更广，但当前实现证据是 FLUX-specific |

## 不得重新出现的历史主张

- GLINT 显式发现 decoder-sensitive tangent subspace；
- 无约束 learned lifting 普遍更优；
- nested GLINT hierarchy 达到 rate-optimal 或优于匹配 flat training；
- condition influence 本身即可证明 source fidelity；
- GAPF 在已知 GLINT likelihood 下执行精确贝叶斯 posterior sampling；
- training-stream、selector、smoke 或 train-holdout metric 是最终论文结果；
- 训练任务完成等于正式评测完成。

## 公开发布前仍需的证据

1. 两个所选 CBR、全部声明 SNR 上的完整 cohort GAPF 评测；
2. 在核验通信预算下匹配的重建型与生成型基线；
3. trajectory injection、coupling、direction、source gate、trust bound 与 channel controller 的组件消融；
4. 带声明 seed 与信道条件、可复现选择的定性样本；
5. 最终引文核验与文献检索冻结；
6. 每个拟发布代码文件的来源与许可审查。
