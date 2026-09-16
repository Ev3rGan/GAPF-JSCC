# 从 SACRA 到 GAPF

## 1. 决定性观察

从 SACRA 转向 GAPF，并不是因为 SACRA 没有使用条件。诊断恰好显示：来源身份、空间对齐和信道状态都会因果影响完整生成路径。

决定性观察是：强条件响应可以与“比直接解码 GLINT anchor 更差的 endpoint”同时存在。

这把此前混在一起的两个问题分开：

1. 生成模型是否响应接收信息？
2. 这种响应是否保护并改善了接收来源证据？

SACRA 回答了第一个问题，GAPF 围绕第二个问题设计。

## 2. 为什么更强 conditioning 不是答案

如果 prior 能覆盖可靠证据，那么扩大 condition width、增加注入 block 或训练更多 LoRA 参数，都可能只增加影响力而不增加忠实性。中心未知量不是条件容量，而是职责。

接收端需要显式规则说明：

- 哪些信息是信道送达的证据；
- 哪些信息缺失且允许合成；
- prior 可以怎样作用于证据；
- 最终结果允许偏离 anchor 多远。

## 3. 概念转变

| SACRA | GAPF |
|---|---|
| GLINT 重建被编码为外部 condition | GLINT 重建成为 inference anchor |
| 生成主要围绕 prior trajectory 组织 | 轨迹从接收证据开始并反复引用它 |
| 条件影响分布于多条 attention path | 空间证据通过 TSET 承担一个显式轨迹职责 |
| LoRA 与 condition adapter 共同改变响应 | prior 保持冻结，轻量接收模块职责分离 |
| 没有显式限制阻止 prior drift | 终端动作经过 source gate 与 trust bound |
| flow/attention response 容易主导诊断 | 最终解码质量和匹配组件 counterfactual 决定有效性 |

## 4. 探索分支留下的中间教训

SACRA 与 GAPF 之间曾考虑若干 anchor-centered 探索。它们不是最终公开方法，但共同收敛出持久要求：

- direct-anchor fallback 必须始终有意义；
- 接收模块不能脱离 prior 与 anchor 独自发明来源信息；
- source-conditioned evidence 必须区别于通用 prior improvement；
- 最终图像质量比内部 flow loss 更重要；
- prior、evidence model 与 transport 必须可分别诊断。

最终仓库保留这些原则，但不会把每个被放弃的分支包装成方法贡献。

## 5. 为什么 GAPF 有两个修正位置

GAPF 同时使用 trajectory injection 与 terminal action，因为二者解决不同问题。

### 轨迹注入

TSET 在冻结 prior 形成预测时提供空间 anchor 证据。没有它，终端模块只能修补一条中间过程从未得到来源引导的轨迹。

### 终端动作

decoder-aligned direction 处理完成 endpoint 中剩余的误差。没有它，轨迹 conditioning 必须同时解决全局生成补全和所有 decoder-sensitive 局部 residual。

trust bound 则保证最终动作不会变成不受约束的 post-generator 替代器。

## 6. posterior 语言的价值与限制

posterior 视角分配如下职责：

- 冻结生成器是 prior；
- GLINT anchor 是接收证据；
- 信道状态决定证据应多强地约束结果；
- 学习式接收端近似二者交互。

这种语言澄清设计，但不产生精确贝叶斯保证。GLINT 信道是学习式非线性链路，GAPF 也不假设闭式 likelihood 或已知线性 observation operator。

## 7. 这条演进链建立了什么

演进链支持以下设计结论：

> 接收条件不应只影响生成模型；它必须在推理状态与允许的 endpoint 动作中拥有显式、受保护的角色。

它还没有建立以下最终性能结论：

> GAPF 在所有工作点都优于所有匹配替代方案。

后一个陈述仍依赖完整正式评测与匹配消融。
