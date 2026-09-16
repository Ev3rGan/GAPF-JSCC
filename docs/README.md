# 文档地图

本仓库按读者的问题组织文档，而不是按实验发生时间排列。深层文档的中文版本是当前权威文本；英文仅在根目录提供精简入口。

## 1. 理解研究问题

- [问题定义](problem-definition.md)
- [论文配套映射](paper-companion-map.md)

建议从这里开始。它们定义来源证据、通信预算、接收端先验、重建目标，以及“合理生成”与“忠实重建”的差别。

## 2. 独立理解每个方法

- [DNLT](methods/dnlt.md)
- [GLINT](methods/glint.md)
- [SACRA](methods/sacra.md)
- [GAPF](methods/gapf.md)

每篇文档独立解释方法的目标、数据流、组件、预期机制、已验证结论和局限。历史方法不会被压缩成一张缺点清单。

## 3. 理解两条设计演进链

- [DNLT 到 GLINT](lineage/dnlt-to-glint.md)
- [SACRA 到 GAPF](lineage/sacra-to-gapf.md)

两篇文档回答：为什么最终方法需要重新定义问题，而不是继续搜索参数或注入位置。

## 4. 理解相关工作背景

- [核心相关工作综述](related-work/core-review.md)
- [设计思想谱系](related-work/design-genealogy.md)
- [综述协议](related-work/review-protocol.md)
- [待作者审阅的候选论文](related-work/candidate-reading-list.md)

核心综述服务于生成式语义通信中的 novelty 与对比主张。思想谱系则记录相邻领域的异质启发，不假装它们属于同一个基准家族。

## 5. 审计当前已知结论

- [主张状态](evidence/claim-status.md)
- [来源地图](evidence/source-map.md)
- [证据与来源规则](evidence/provenance-policy.md)

这些文档区分实现事实、正式结果、诊断、解释和未闭合主张。

## 建议阅读路线

### 论文审稿者

1. 问题定义
2. GLINT
3. GAPF
4. 主张状态
5. 核心相关工作综述

### 研究方法演进的读者

1. DNLT
2. DNLT 到 GLINT
3. GLINT
4. SACRA
5. SACRA 到 GAPF
6. GAPF
7. 设计思想谱系

### 关注实现的读者

1. 问题定义
2. GLINT 与 GAPF 方法文档
3. [最小实现边界](../src/README.md)
4. 主张状态
