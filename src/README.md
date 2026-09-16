# 最小实现规划

> 状态：发布边界已经批准，但本阶段尚未复制实现源码。

## 目的

后续代码用于帮助读者学习真实 GLINT 与 GAPF 机制，不用于端到端复现论文，也不提供可直接部署的训练系统。

## 拟议模块

```text
src/
├─ glint.py          # generator-native analysis/synthesis 数据流
├─ lifting.py        # channel-tied separable lifting 与 inverse
├─ channel.py        # exact-CBR 符号契约与简化 AWGN 接口
├─ gapf.py           # anchor start、trajectory coupling 与 terminal action
├─ tset.py           # 紧凑空间证据接口
├─ direction.py      # decoder-aligned multiscale terminal direction
└─ contracts.py      # tensor shape、冻结/训练边界与不变量
```

从真实源码抽取并完成 dependency review 后，文件名可以调整。

## 必须保持真实的内容

### GLINT

- 原生 VAE latent 输入与输出语义；
- 固定 calibration 边界；
- channel-tied lifting 行为；
- 学习式 analysis/synthesis 路径；
- 精确复符号预算与功率归一化；
- 接收端可观察 anchor 输出；
- decoder-aligned 训练接口。

### GAPF

- anchor-noise 初始化；
- 完整冻结 prior solver 接口；
- 逐 step 空间证据注入；
- anchor-relative posterior coupling；
- decoder-aligned multiscale terminal direction；
- source gate、RMS normalization 与 trust projection；
- 单调 channel-state controller 接口；
- 精确 fallback 与冻结/训练边界。

## 将删除或抽象的内容

- 模型权重和绑定私有 artifact 的 checkpoint loader；
- 私有 dataset 与 preprocessing pipeline；
- 完整训练 orchestration；
- 未公开关键 hyperparameter preset；
- 正式 evaluation 与论文结果聚合；
- experiment manifest、server path 与 scheduler infrastructure；
- 不能重新分发的第三方源码。

## 可运行程度

目标是 **contract-runnable**：

- 模块不依赖私有 infrastructure 即可 import；
- tensor-shape 示例可用 synthetic input 运行；
- exact-rate 与 trust-bound 不变量有 unit test；
- 不捆绑完整 pretrained FLUX/MambaJSCC stack 与训练权重；
- 不把任何命令宣传为可复现论文指标。

外部读者应能理解并运行关键算法 seam，但不会获得完整实验配方。

## 源码抽取规则

最小实现必须从冻结的真实实现抽取，不能只根据论文重新编写。每个文件都应标明它属于：

- 删除非必要基础设施后的原始代码；
- 保持计算过程不变的机械缩减；
- 围绕第三方接口建立的 adapter；
- 不属于论文实现的解释性示例。

解释性示例绝不能被表述为论文结果的源码。

## dependency 与许可门槛

DNLT 依赖上游 MambaJSCC tree，最终接收端依赖 FLUX-family 实现。发布代码前必须：

1. 核验上游 redistribution 条款；
2. 不 vendoring 许可不兼容的代码；
3. 优先使用窄接口和安装说明；
4. 为本仓库原创代码选择 license；
5. 为每个抽取模块保留 source provenance。
