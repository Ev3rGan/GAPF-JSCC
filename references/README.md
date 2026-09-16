# 参考资料语料规则

本目录只用于公开安全的 bibliographic material，不作为本地论文收藏的镜像。

## 审阅后可以纳入

- 去重后的 BibTeX database；
- 包含 title、author、year、venue、DOI/arXiv ID、official URL、review status 和项目角色的 CSV catalogue；
- 为本仓库原创的阅读笔记与综合；
- 以通信为中心的综述所需可复核检索协议。

## 默认排除

- 第三方 PDF；
- 没有明确 redistribution permission 的出版社排版文章；
- 批量提取的全文；
- 没有记录 license 或 permission 的复制 figure；
- 本地绝对路径与私有下载记录。

能够从 arXiv 或出版社页面访问论文，不等于获准重新分发所有版本。任何全文或图像进入 Git 前都必须单独核验来源许可。

## 分析结构

仓库不会强迫每篇论文使用相同信息卡：

- 核心综述围绕通信 novelty 边界组织；
- 设计思想谱系围绕塑造 GLINT/GAPF 的设计问题组织；
- 单篇论文的深度与实际相关性成比例；
- 最小 bibliographic metadata 仍标准化，以保证 citation hygiene。

## 历史启发标签

所有 influence claim 都应区分：

- 有记录的历史启发；
- 尚待确认的作者回忆；
- 后来发现的理论支持；
- 直接对比或 baseline；
- 说明方法为何不能直接迁移的 boundary evidence。
