# 论文配套映射

## 1. 为什么需要仓库配套文档

当前论文受短文版面限制，方法部分必须把大量推理压缩为公式和组件描述。缺失的内容并不只是实现细节，还包括读者理解方法所需的概念步骤：

- 问题如何定义；
- 为什么拆成两个阶段；
- 每个机制为什么存在；
- 哪些结论由构造直接保证；
- 哪些结论依赖实验；
- 最终方法与前身究竟有何不同。

本仓库用于给出这些内容的未压缩解释。

## 2. 论文到配套文档的映射

| 论文内容 | 配套文档 | 额外价值 |
|---|---|---|
| 系统模型与目标 | [问题定义](problem-definition.md) | 分离来源证据、信道状态、接收先验与生成细节 |
| GLINT 小节 | [GLINT](methods/glint.md) | 解释表示、lifting、通信契约、目标和证据边界 |
| GAPF 小节 | [GAPF](methods/gapf.md) | 给出单样本执行时间线并拆分六项机制职责 |
| DNLT 对比 | [DNLT](methods/dnlt.md) 与 [DNLT→GLINT](lineage/dnlt-to-glint.md) | 先公平说明 DNLT，再解释 Stage 1 的问题重定义 |
| SACRA 对比 | [SACRA](methods/sacra.md) 与 [SACRA→GAPF](lineage/sacra-to-gapf.md) | 区分条件影响与证据保护 |
| 相关工作 | [核心综述](related-work/core-review.md) 与 [设计思想谱系](related-work/design-genealogy.md) | 分离最近通信系统和异质方法启发 |
| 结果与结论 | [主张状态](evidence/claim-status.md) | 防止未闭合或诊断证据被写成最终性能 |

## 3. 仍必须留在论文正文中的最小推理

仓库不能补救一篇必须离开正文才能理解中心论证的论文。短文正文仍应至少保留以下逻辑。

### GLINT

1. 冻结生成 decoder 使不同 latent 误差具有不同重要性。
2. 传输 decoder 原生 latent 可移除一次可避免的表示转换。
3. 受约束 lifting 暴露局部多尺度结构，同时保留 latent 通道身份。
4. exact CBR 与零源相关 side information 定义通信主张。
5. 输出是带噪 anchor，而不是干净的生成器状态。

### GAPF

1. 条件能够影响生成器，并不等于来源得到保护。
2. 接收 anchor 与冻结 prior 必须承担不同职责。
3. 轨迹注入在 prior 构造样本时持续提供来源证据。
4. 终端 direction 在轨迹结束后修正 decoder 敏感的剩余误差。
5. source gate 与 trust projection 限制最终学习动作。
6. posterior 表示职责模型，而不是精确解析 likelihood 主张。

## 4. 可以移出正文的内容

在版面有限时，下列内容更适合放入 companion 或正式补充材料：

- DNLT 与 SACRA 的完整历史说明；
- calibration 与 lifting 的详细推导；
- GAPF 完整执行时间线；
- 被替代或未采用的机制；
- 扩展文献综合；
- 精确来源 provenance 与 claim ledger；
- 已记录在可复核方法快照中的非关键实现常数。

只有当正文保留上一节的最小因果论证后，这种迁移才是合理的。

## 5. 建议的论文重写顺序

1. 定义 source-faithful generative communication，以及接收端的两类信息源。
2. 解释为什么 Stage 1 必须产生 generator-native 接收证据。
3. 先把 GLINT 写成接口设计，再说明其实现。
4. 解释为什么带噪 anchor 既不能被当作干净 prior state，也不能只是一个弱外部 prompt。
5. 先把 GAPF 写成职责分解，再说明各机制。
6. 将数学上界与机制预期分开陈述。
7. 明确 GLINT 证据已经闭合，而 GAPF 性能在正式评测完成前仍为待验证。

这一顺序优先服务于读者理解，而不是复述模块开发的历史顺序。
