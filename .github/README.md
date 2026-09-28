# dxbc-spirv for amdgpu-wddm

This fork of [doitsujin/dxbc-spirv](https://github.com/doitsujin/dxbc-spirv) carries two shader compiler fixes
used by the DXVK engine of [amdgpu-wddm](https://github.com/D-Ogi/amdgpu-wddm), a Windows WDDM driver stack for
the AMD BC-250. dxbc-spirv is DXVK's DXBC to SPIR-V compiler; the engine's DXVK branch
([D-Ogi/dxvk](https://github.com/D-Ogi/dxvk/tree/amdgpu-wddm/ddi-engine)) pins this fork as its `dxbc-spirv`
submodule. This repository builds no DLL of its own: the fixes end up inside the engine `amdgpu_wddm_dxvk.dll`.

| Branch | What it is |
|---|---|
| `main` | upstream's `main`; no project commits land here |
| `amdgpu-wddm/ddi-engine` | upstream dxbc-spirv plus two fixes; head `253c08ce` is the commit the DXVK branch pins |

`amdgpu-wddm/ddi-engine` forks from upstream commit `ed453716b717a5ed1fa6594d1ff8bd802eddd2e3` (2026-09-25).
The two fixes, not upstream yet:

- `dcf006a3` dxbc: the pass-through geometry shader emits every vertex of its input primitive instead of one
  point per primitive (with the DXVK branch's matching primitive-type change, stream output without a geometry
  program works for lines and triangles).
- `253c08ce` spirv: geometry shader outputs are decorated with their stream, so stream output on streams 1-3
  captures its own values instead of stream 0's.

Tested: the DXVK engine's offline positive control covers both, with stream output of points, lines and
triangles, a rasterized stream and a second stream of an fxc gs_5_0 program (Validation in the main repository's
[d3d11-ddi-engine.md](https://github.com/D-Ogi/amdgpu-wddm/blob/main/docs/design/d3d11-ddi-engine.md#validation)).
No fact in the main repository's
[docs/facts.md](https://github.com/D-Ogi/amdgpu-wddm/blob/main/docs/facts.md) isolates these two fixes on the
BC-250; the r7 engine that ran M755 and M756 pins `253c08ce`, but those facts record no stream output result.

License: dxbc-spirv is under the MIT license in [LICENSE](https://github.com/D-Ogi/dxbc-spirv/blob/main/LICENSE)
(Copyright 2025 Philip Rebohle).
