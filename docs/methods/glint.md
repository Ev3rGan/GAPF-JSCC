# GLINT：生成器原生 latent 传输接口

> 状态：最终 Stage 1 方法。正式证据支持所评估工作点上的 generator-native 通信接口；因果归因范围仍窄于完整系统性能结论。

## 1. GLINT 定义的问题

GLINT 首先不是一个新 backbone，而是来源图像、带噪无线链路和固定接收端生成器之间的通信接口。

发送端应传输冻结生成器本来就理解的表示。接收端应恢复一个 latent anchor，使其：

- 使用生成器原生坐标系；
- 与来源保持空间对齐；
- 可由冻结 VAE 直接解码；
- 满足精确 CBR 与功率约束；
- 不使用源相关 side information；
- 按解码后真正重要的误差训练。

## 2. 数据流

```text
来源图像 x
  → 冻结 VAE 编码与官方 scale/shift
  → 原生 latent z
  → 固定逐通道 calibration
  → channel-tied separable lifting
  → 学习式 analysis transform
  → 复符号归一化与 AWGN
  → 学习式 synthesis transform
  → inverse lifting 与 inverse calibration
  → 接收端可观察 anchor a_s
  → 直接冻结 VAE 解码，或进入 GAPF 接收端
```

输出 `a_s` 不是推理时需要发送端参与的内部特征。它完全由接收符号和公开接收端信息重建。

## 3. 为什么生成器原生坐标重要

许多生成式通信系统先重建 pixel 或 task-specific latent，再学习到生成器的另一层映射。这引入第二个表示边界：信息可能先在通信系统中丢失，再在向生成器的转换中丢失。

GLINT 直接传输冻结 VAE decoder 期望的 latent。这样不会消除信道噪声，但会在进入生成式接收端前移除一次可避免的跨表示转换。

## 4. 组件与职责

### 4.1 固定 calibration

逐通道 calibration 避免数值尺度较大的通道仅因幅值而主导优化。该标定由训练数据固定，并在接收端求逆，因此它是数值契约，而不是隐藏的源相关 side information。

### 4.2 channel-tied separable lifting

`2×2` polyphase split 将表示分为一个 coarse field 和三个 detail field。predict/update map 暴露局部多尺度结构，并在 latent 通道间共享变换。

channel-tied 约束十分关键：早期无约束 learned lifting 不稳定。最终结论被刻意收窄为：受约束的 learned lifting 相较匹配 fixed-lifting route 带来小幅、可重复提升。项目不声称任意 learned lifting 都更优，也不把完整 GLINT 增益归因于 lifting。

### 4.3 学习式 analysis 与 synthesis transform

analysis transform 将 lifting 后的表示映射为复符号的实部与虚部。逐样本功率归一化执行通信契约。synthesis transform 在 inverse lifting 前重建 lifting 系数。

最终验证的 GLINT 实例使用紧凑 Gated Axial Conv backbone。科学贡献不在于声称该 mixer 普遍最优；等预算 CNN/Swin/Mamba 的任意可交换性尚未建立。

### 4.4 decoder-aligned 复合失真

GLINT 通过冻结 VAE decoder 训练，并组合图像失真、成对感知距离、decoder feature 一致性与 latent 稳定性：

\[
\mathcal L_{\mathrm{GLINT}}
=\lambda_x\mathcal L_x
+\lambda_p\mathcal L_{\mathrm{LPIPS}}
+\lambda_f\mathcal L_{\mathrm{feat}}
+\lambda_z\lVert a_s-z\rVert_1.
\]

这里并不声称 GLINT 显式估计 decoder Jacobian 或 decoder tangent subspace。得到支持的表述更简单：被传输的表示依据冻结 decoder 之后仍有意义的误差进行优化。

## 5. 为什么预期 GLINT 有效

GLINT 对齐了四个经常被分开处理的边界：

1. **表示边界**：传输对象原生属于接收端生成器；
2. **几何边界**：lifting 暴露局部多尺度结构，而不任意混合语义 latent 通道；
3. **通信边界**：exact CBR、复功率和零源相关 side information 均为显式契约；
4. **质量边界**：优化经过用户实际观察重建结果的固定 decoder。

因此，方法主要减少接口 mismatch，而不是依赖更大的通用 encoder 自行学习绕过 mismatch。

## 6. 证据支持什么

正式 Stage 1 评测支持：

- 完整 GLINT 系统在两个已评估 CBR 上，均改善了相对正式 DNLT comparator 的 generator-native anchor；
- 所选低码率 route 是证据闭合最干净的 GLINT 工作点；
- 受约束 channel-tied lifting 的贡献为正，但幅度较小；
- nested 高码率增强相较低码率 base 有改善，但逊于匹配的 flat 高码率 route；
- exact CBR 与零源相关 side information 是实现强制执行的契约。

高码率负结果属于正式方法记录。GLINT 不声称 nested 表示具备资源最优性。

## 7. 证据不支持什么

- GLINT 尚未显式识别 decoder-sensitive tangent subspace；
- 完整系统增益不能全部归因于复合目标；
- learned lifting 尚未被证明优于所有 stride-convolution 替代；
- 已验证 backbone 尚未证明可任意替换；
- anchor 质量好不等于 GAPF 已改善完整接收端。

## 8. 输出语义

GLINT 输出最适合被理解为**带噪接收 anchor**：

- 它是源特定证据；
- 位于正确的生成器 latent 坐标；
- 保留压缩、信道噪声与 synthesis 带来的误差；
- 这些误差未必独立或服从高斯分布；
- 它不会自动成为生成器学习分布上的干净状态。

最后一点连接到 Stage 2：GAPF 必须使用 anchor，但不能假装它既是完美 ground truth，也不能把它降格为可丢弃的条件。
