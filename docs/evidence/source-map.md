# 来源地图

> 本公开安全地图只标识初始文档背后的来源类别。精确路径、hash、checkpoint 与私有 artifact locator 应进入不公开的 provenance manifest。

| 来源 ID | 来源类别 | 支撑内容 | 当前状态 |
|---|---|---|---|
| S-001 | 当前 GAPF-JSCC V2 论文源文件 | 符号、系统边界、GLINT/GAPF 公式、声明的方法实例、未闭合结果字段 | 已本地审阅；论文仍在审阅中 |
| S-002 | 当前远程 DNLT 源码与固定上游 MambaJSCC revision | DNLT 组合方式、VAE adapter、latent-domain head、exact CBR 与信道路径 | 已检查源码；redistribution 审查待完成 |
| S-003 | 最终 GLINT 审计与冻结 Stage 1 正式决策 | decoder-aligned objective、受约束 lifting、exact CBR、零 side information、flat-vs-nested 结论 | 可用的已审计证据 |
| S-004 | SACRA 源码、修正执行路径与因果诊断 | condition use、空间对应、SNR response、参数化 condition injection | 仅属诊断证据 |
| S-005 | 最终 GAPF 契约、实现源码与训练决策 | 十二步冻结 prior、TSET、terminal direction、trust bound、controller、冻结/训练范围 | 架构与训练图已冻结 |
| S-006 | GAPF 完整 cohort 正式对比与匹配消融 | 最终外部性能与组件归因 | 尚未进入公开可主张集合 |
| S-007 | 本地通信文献目录与 Stage 1/Stage 2 定向综述 | novelty 边界、最近邻、设计谱系 seed set | 强 seed corpus；尚未冻结为系统综述 |
| S-008 | 原始过程笔记与研究回顾 | 历史意图、被放弃假设、作者学习路径 | 只在与最终证据校准后使用 |

## 使用规则

- S-001 与 S-005 定义当前方法词汇；
- 早期设计笔记与 S-003 冲突时，由 S-003 控制最终 GLINT 主张；
- S-004 可解释 Stage 2 为什么重定义，但不能建立最终 GAPF 性能；
- S-006 仍未闭合，任何文档不得用训练或诊断数值悄然替代它；
- S-008 对思想谱系有价值，但对当前实现事实的权威级别最低。
