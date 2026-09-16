# 证据与来源规则

## 1. 目的

本仓库需要保持权威，但不能变成私有路径、原始日志或瞬时实验输出的堆积。权威来自可追溯主张与显式状态，而不是发布所有 artifact。

## 2. 证据层级

### 公开安全的主张记录

仓库保存：

- 稳定 claim ID；
- 面向读者的命题；
- 状态与适用范围；
- 支撑它的证据类型；
- 已知排除项与反例；
- 最近核验日期。

### 私有 artifact manifest

独立的非公开 manifest 可以把 evidence ID 映射到：

- source revision 或 file hash；
- configuration 与 checkpoint hash；
- dataset cohort 与 sample count；
- CBR、SNR、seed 与 metric definition；
- 正式 decision file 与 audit record；
- 负责核验的人员和日期。

机器特定路径应留在私有 manifest，不进入 GitHub 面向读者的文档。

## 3. 证据优先级

记录冲突时，按以下顺序处理：

1. 最终实现与冻结 formal decision/manifest；
2. 完整正式评测 artifact；
3. 冻结方法或实验 contract；
4. 已审计 conclusion document；
5. diagnostic experiment；
6. 原始设计笔记或 retrospective explanation。

历史笔记对演进链仍有价值，但不能覆盖最终实现。

## 4. 结果准入

只有经过 artifact-level 核验后，以下内容才能支撑论文性能主张：

- 声明的完整 cohort；
- 匹配的通信预算；
- 声明的 checkpoint 与 seed；
- 全部必要 SNR/CBR cell；
- 一致 metric definition；
- 被保留的负面与缺失结果。

以下内容永远不能直接升级为论文性能：

- smoke test；
- 单图 probe；
- selector subset；
- training-stream summary；
- train-holdout diagnostic；
- 未完成 job；
- post-hoc best case；
- 没有打开来源 artifact、只从文字复制的数值。

## 5. 解释规则

每个解释都应区分：

- **观察事实**：实现或实验直接显示什么；
- **推断**：与观察最一致的机制；
- **设计后果**：该推断如何影响最终方法；
- **剩余替代解释**：证据尚未排除什么。

这样可以提供理论和机制解释，同时不把它冒充为形式证明。

## 6. 替代与废止规则

当后续实现改变早期 contract 时：

- 保留早期文档作为历史 provenance；
- 添加 supersession 记录；
- 明确指出哪些职责或参数化发生变化；
- 更新当前 claim ledger；
- 不得把旧证据静默改写成新设计的证据。

`superseded` 不等于没有价值，也不授权删除历史材料。

## 7. 文献来源规则

对每个引文命题：

- 使用原论文或官方项目来源；
- 核验 bibliographic metadata；
- 记录它是论文直接结果、论文作者解释，还是本项目推断；
- 区分历史启发与后续佐证；
- 不把搜索摘要、另一篇论文的 bibliography 或 AI 摘要当作核验证据。

## 8. 代码来源规则

每个发布源文件都应标明：

- 从哪个内部 source revision 抽取；
- 属于原样代码、机械缩减还是 interface adaptation；
- 哪些算法行为必须保持不变；
- 哪些基础设施和参数被有意移除；
- 第三方 dependency 与 license boundary。

上游源码在 redistribution 条款核验前不得 vendoring。
