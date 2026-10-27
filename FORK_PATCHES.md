# MLX fork integration at v0.32.4

Upstream cutoff: `36969c094354086e15d2a327a9c50feb20b9cf11` (v0.32.4).
Previous fork cutoff: `11da2b33a51772c023e2f7d7bc4ba9b3ff7e03ef`.
The integration retains both histories so owned changes remain auditable.

| Previous fork commit | Disposition at v0.32.4 |
| --- | --- |
| d2d7138cf | Replace shared CPU/Metal 1-bit support with upstream; retain CUDA quantize/dequantize extensions. |
| 5743cb8a7 | Retain 1-bit fast-dispatch alignment guards. |
| 3e0fa1fa1 | Retain one-pack-per-lane 1-bit occupancy geometry. |
| 564c1160f | Retain the generation-17 NAX restriction only in quantized dispatch; restore upstream availability for floating-point matmul, attention, and gated delta. |
| 629f09185 | Retain bit-width/batch-size affine QMV dispatch. |
| 3ebf6207f | Retain native CUDA 1-bit QMV. |
| dde9c8359 | Retain formatting of surviving low-bit code. |
| f8afc8b3e | Retain CUDA low-bit routing through native QMV. |
| d285f7248 | Replace with upstream's equivalent process-lifetime CUDA encoder map. |
| 9190776d7 | Retain opt-in quantized headers for custom Metal kernels. |
| 4d88c9d87 | Retain packed NVFP4 staging for non-global-scale paths; use upstream floating-point global-scale handling otherwise. |
| 0dfe6cbd9 | Retain M4/H3 GEMM tuning. |
| 3a0382c22 | Retain matching host/kernel 1-bit fast alignment and regression. |
| b34c2353b | Retain formatting of surviving code. |
| 2ccca66fc | Retain quantized custom-kernel AOT source linkage. |
| 1fb504ca7 | Retain source-level NAX attention optimization and adapt activation preloading to upstream expert tiling and inactive-simdgroup guards. |
| 3f6e7728e | Replace old QMV template-arity special cases with upstream's uniform template parameters; retain execution regression coverage. |
| b57bd7640 | Retain Linux matmul compatibility. |
| 3322ebda0 | Retain non-macOS Apple CPU-JIT availability guard. |
| 11da2b33a | Retain formatting of surviving Metal code. |

The quantization suite retains owned packed-bit-order, dtype/group/odd-row,
large-M, fast-alignment, and host/template execution regressions. Upstream's
CUDA 1-bit skips are not carried over: this fork implements that backend.
CUDA execution requires a CUDA host; Metal results do not validate CUDA.

The v0.32.4 integration additionally fixes the retained CPU 1-bit transposed
matmul path to accumulate FP16/BF16 products in float. Its existing
dtype/group/odd-row regression keeps its original tolerances. Wider affine
bit widths retain their previous arithmetic.

The Linux SwiftPM compiler additionally requires an explicit `typename` for
the dependent CPU reduction accumulator alias. This syntax-only portability
fix retains the upstream reduction type and arithmetic.

The generation-17 NAX workaround is scoped to all four quantized dispatch
and alignment decisions. Floating-point operations use upstream device
availability. The matched 127,242-token BF16 ablation fails with the global
guard and matches stock upstream token-for-token with upstream availability.
The quantized regression covers FP16/BF16 activations at the historical
64-by-9216 threshold across 1/2/4/8-bit ordinary and gathered matmul.
