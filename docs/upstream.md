# Upstream changes and reports

This list records verified contributions from the project account and links to relevant donor work. PR status is a GitHub snapshot from November 26, 2026. An open status includes drafts.

Standalone regressions and results belong with their upstream fixes. Local diagnostics such as render overrides, watchdog bypasses, system-recovery experiments and the temporary vkd3d/Mesa instrumentation stayed outside the contribution branches.

## fxceless/victory-fork

| Pull request | Change | Status |
|---|---|---|
| [#131](https://github.com/fxceless/victory-fork/pull/131) | Make the VIctory game window borderless by default | Open |
| [#174](https://github.com/fxceless/victory-fork/pull/174) | Fall back to DXC for runtime shaders under linux wine | Open |
| [#813](https://github.com/fxceless/victory-fork/pull/813) | Probe GPU capabilities before warming the shader cache | Open |
| [#958](https://github.com/fxceless/victory-fork/pull/958) | Measure kernel write-watch requests in diagnostics | Open |
| [#979](https://github.com/fxceless/victory-fork/pull/979) | GPU: walk only dirty gaps when refreshing guarded views | Open |
| [#989](https://github.com/fxceless/victory-fork/pull/989) | GPU: bound detiling slices to the upload allocation | Open |
| [#1028](https://github.com/fxceless/victory-fork/pull/1028) | Run indirect clamp tests on llvmpipe | Open |
| [#1060](https://github.com/fxceless/victory-fork/pull/1060) | XMem: coalesce split placeholders before retrying a mapping | Open |
| [#1063](https://github.com/fxceless/victory-fork/pull/1063) | Batch consecutive pool-page mappings in an opt-in mode | Open |
| [#1094](https://github.com/fxceless/victory-fork/pull/1094) | Copy-back: skip zero mask bytes a word at a time | Open |
| [#1206](https://github.com/fxceless/victory-fork/pull/1206) | GPU: cap constant view sizes before rounding | Open |

## Marvisak/VIctory

| Pull request | Change | Status |
|---|---|---|
| [#1724](https://github.com/Marvisak/VIctory/pull/1724) | Make the VIctory game window borderless by default | Closed |
| [#2244](https://github.com/Marvisak/VIctory/pull/2244) | Check live page-table allocations on capped view heaps | Closed |

## PastiiJay/klbf-lab

| Pull request | Change | Status |
|---|---|---|
| [#1](https://github.com/PastiiJay/klbf-lab/pull/1) | Fix host pipeline caching on Wine | Merged |
| [#10](https://github.com/PastiiJay/klbf-lab/pull/10) | Document the shader compiler requirement for Wine | Merged |
| [#12](https://github.com/PastiiJay/klbf-lab/pull/12) | Fix Wine pipeline worker overflows in shader translation | Open |

## PastiiJay/klbf-lab-beta

| Pull request | Change | Status |
|---|---|---|
| [#7](https://github.com/PastiiJay/klbf-lab-beta/pull/7) | Capture composited Series S frames in diagnostics | Merged |

## spidercraft/VIctory

| Pull request | Change | Status |
|---|---|---|
| [#5](https://github.com/spidercraft/VIctory/pull/5) | Make the Linux physical-page alias test deterministic | Open |
| [#6](https://github.com/spidercraft/VIctory/pull/6) | Shaders: read back a PC mesh draw without the console SDK | Open |
| [#7](https://github.com/spidercraft/VIctory/pull/7) | HLE: support the opt-in Bink intro skip on native Linux | Open |
| [#8](https://github.com/spidercraft/VIctory/pull/8) | Linux GPU: read small title buffers through a guarded copy | Open |
| [#9](https://github.com/spidercraft/VIctory/pull/9) | GPU: merge copy-back bytes eight at a time | Open |
| [#10](https://github.com/spidercraft/VIctory/pull/10) | GPU: preserve disjoint shadow refresh ranges | Open |
| [#11](https://github.com/spidercraft/VIctory/pull/11) | GPU: clamp indirect arguments in a private list buffer | Open |
| [#12](https://github.com/spidercraft/VIctory/pull/12) | GPU: keep texture detiling inside the staging allocation | Open |
| [#13](https://github.com/spidercraft/VIctory/pull/13) | GPU: upload changed textures in the native prelude | Open |
| [#14](https://github.com/spidercraft/VIctory/pull/14) | GPU: clamp only the active native indirect records | Open |
| [#15](https://github.com/spidercraft/VIctory/pull/15) | GPU: resolve native texture contents in submission order | Open |
| [#16](https://github.com/spidercraft/VIctory/pull/16) | GPU: stop the native indirect work budget from wrapping | Open |
| [#17](https://github.com/spidercraft/VIctory/pull/17) | GPU: reuse the indirect scratch state between clamps | Open |
| [#18](https://github.com/spidercraft/VIctory/pull/18) | GPU: keep native indirect scratch in device-local memory | Open |
| [#19](https://github.com/spidercraft/VIctory/pull/19) | GPU: skip indirect clamps when the host pipeline is unavailable | Open |
| [#20](https://github.com/spidercraft/VIctory/pull/20) | GPU: keep the native indirect work budget in device-local memory | Open |
| [#21](https://github.com/spidercraft/VIctory/pull/21) | Linux GPU: preserve dirty state across physical-page aliases | Open |
| [#22](https://github.com/spidercraft/VIctory/pull/22) | Linux GPU: skip hashing clean physical textures | Open |
| [#23](https://github.com/spidercraft/VIctory/pull/23) | Linux GPU: compose the Series S display planes | Open |
| [#24](https://github.com/spidercraft/VIctory/pull/24) | GPU diagnostics: report timings for single-list submissions | Open |
| [#25](https://github.com/spidercraft/VIctory/pull/25) | GPU: bound constant-buffer views before recording copies | Open |
| [#26](https://github.com/spidercraft/VIctory/pull/26) | GPU: mirror empty buffer views as null descriptors | Open |
| [#27](https://github.com/spidercraft/VIctory/pull/27) | GPU: write typed null constant buffer views | Open |

## ZSlayerHQ/zslayer-pc-port-runtime

| Pull request | Change | Status |
|---|---|---|
| [#9](https://github.com/ZSlayerHQ/zslayer-pc-port-runtime/pull/9) | Read texture sweep descriptors through the kernel | Open |

## Related contributions and verification reports

- AMD view-heap and precompile work came from the original author’s branch and bundle. [Fxceless #98](https://github.com/fxceless/victory-fork/pull/98) · [AMD branch](https://github.com/sillynocodecat/victory-fork/tree/amd-view-heap)
- Existing Series S, shader and ABI work was tested with author attribution intact. [PresentX](https://github.com/Marvisak/VIctory/pull/211) · [Guest GetProcAddress verification](https://github.com/spidercraft/VIctory/pull/2#issuecomment-7174647790) · [XMA rings](https://github.com/Marvisak/VIctory/pull/536)
- Upstream results were posted on the existing parallel checksum, copy-back and shader wave investigations. [Checksum](https://github.com/fxceless/victory-fork/pull/1014#issuecomment-7172695962) · [Copy-back](https://github.com/fxceless/victory-fork/pull/1032#issuecomment-7171498465) · [Wave behavior](https://github.com/Marvisak/VIctory/pull/189#issuecomment-7184527098)
- The Leonida alias-guard fix remained the original author’s contribution. The project reproduced and reported the guard’s effect over multiple revisions. [Leonida #1867](https://github.com/ubiquitarianism/leonida/pull/1867#issuecomment-7174840488)
- XCL’s Wine experiments used the existing vtable fixes. The observed compatibility results belong to those PRs. [XCL #867](https://github.com/xcl-dev/xcl/pull/867) · [XCL #1395](https://github.com/xcl-dev/xcl/pull/1395)
- Native benchmark reports were posted on existing PRs when the code came from their authors. [Occlusion](https://github.com/Marvisak/VIctory/pull/1731) · [Lavapipe BC7 follow-up](https://github.com/spidercraft/VIctory/pull/24#issuecomment-7185812097)
- Local Mesa and vkd3d-proton experiments were kept local. The observed timings were shared on the related Victory report. [Mesa contributor policy](https://docs.mesa3d.org/submittingpatches.html#expectations-on-contributors)

## Commit and PR conventions used here

Integration history is linear. Donor commits retain their authorship and source lineage. New commit bodies explain the failure or bottleneck addressed, with Tested-on and Assisted-by trailers where applicable. Based-on and Depends-on use full PR URLs. Review branches remain available through their forks and the Disroot mirror. Dependent native PRs link their prerequisites and layer diffs to preserve their individual review history.
