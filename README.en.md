# GAPF-JSCC

[简体中文](README.md) | [English](README.en.md)

> **Public research companion.** This repository documents the GAPF-JSCC problem formulation, method lineage, evidence boundaries, and selected manuscript-support materials. It is intended for method understanding and is not a complete training or end-to-end reproduction package.

GAPF-JSCC studies perceptually faithful wireless image reconstruction by combining generator-native latent transmission with an anchor-constrained frozen generative prior:

- **GLINT** communicates the native latent expected by the receiver-side generator and reconstructs a spatially aligned, receiver-observable anchor.
- **GAPF** treats that noisy anchor as protected source evidence while a frozen rectified-flow prior supplies information that the channel did not preserve.

The authoritative documentation is written in Chinese to preserve the full problem formulation, method lineage, mathematical rationale, validation conclusions, and evidence boundaries without maintaining two drifting copies. This English page is a compact entry point.

## Status

| Component | Current status | Claim boundary |
|---|---|---|
| GLINT | Stage-1 formal evidence is closed at the selected operating points | The audited generator-native anchor improves over the formal DNLT comparator; stronger causal attribution remains bounded |
| GAPF architecture | Final training graph and component responsibilities are frozen | The implementation includes trajectory evidence injection and a bounded terminal action around a frozen prior |
| GAPF end-to-end evaluation | System-level results and a qualitative comparison are included in the working manuscript; the repository claim ledger still requires synchronization | The final paper and refreshed ledger control the claims; this release adds no per-component attribution or universal metric-dominance claim |
| Minimal code | Release boundary defined; extraction pending | No weights, full recipe, private data pipeline, or formal evaluator |

## Reading map

- [Chinese documentation map](docs/README.md)
- [Problem definition](docs/problem-definition.md)
- [DNLT](docs/methods/dnlt.md) → [GLINT](docs/methods/glint.md)
- [SACRA](docs/methods/sacra.md) → [GAPF](docs/methods/gapf.md)
- [Core related-work review](docs/related-work/core-review.md)
- [Design genealogy](docs/related-work/design-genealogy.md)
- [Claim-status ledger](docs/evidence/claim-status.md)
- [Planned minimal implementation](src/README.md)

## Scope boundary

The repository will not include model weights, private or complete data pipelines, critical unpublished training presets, full orchestration, the formal evaluator, raw experiment logs, or vendored third-party papers and upstream source. Minimal code will be extracted from the real frozen implementation after provenance and licensing review.

The repository is public. Selected manuscript-writing and notation documents are available under [`paper/`](paper/README.md); the working TeX, PDFs, figures, and internal revision records are not part of the public release.
