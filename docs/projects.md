# Tested projects, branches and mirrors

The status column gives the furthest stage established by the cited local run. Versions beneath the same project sometimes exercised different Wine builds, console profiles, or rendering backends. See [benchmarks](benchmarks.md) for the conditions.

| Project | Revisions or variants examined | Local result |
|---|---|---|
| [Marvisak/VIctory](https://github.com/Marvisak/VIctory) | Initial Windows-target build, Flatpak pin `8e79c80e`, update `ffe310e`, later donor and shader branches | Stock Proton booted at 4K and reached legal notices. Series S needed the display-plane compositor and additional descriptor work. |
| [fxceless/victory-fork](https://github.com/fxceless/victory-fork) | `2119b0e` and `31714de` integration bases, later `46bf5351` and `f6a09d3b` history, Wine DXC and asynchronous runtime variants | Strongest verified local control and story progression came from this lineage. Local `1c37f0ec` on `f6a09d3b` hit a startup breakpoint at frame 300, while the published build reached frame 2048 in the same smoke configuration. |
| [ZSlayerHQ/zslayer-pc-port-runtime](https://github.com/ZSlayerHQ/zslayer-pc-port-runtime) | `next`, including the relay, async submission, copy queue and Series S presentation work | Imported and exercised in Wine integration. A focused integration suite passed 156 tests. The relay build rendered the first 3D room. A controlled end-to-end speedup remained unresolved. |
| [sillynocodecat/victory-fork](https://github.com/sillynocodecat/victory-fork/tree/amd-view-heap) | `amd-view-heap` at `e8cad1e`, also compared with a local donor bundle | AMD precompile and heap-cap fixes were preserved with original authorship. The combined build passed four focused RX 6800 Wine tests. The game rendered the skyline and credits. [Source PR](https://github.com/fxceless/victory-fork/pull/98) |
| [spidercraft/VIctory](https://github.com/spidercraft/VIctory) | Wine-free Linux loader, native vkd3d-proton, RX 6800 and Lavapipe variations | Native ELF boot and rendered menus verified. Native AMD hardware tests reached 3D submissions; software tests reached story loading. See [native snapshots](#native-snapshots). |
| [JoshuuDev/victory-amd](https://github.com/JoshuuDev/victory-amd) | Local `test/rx6800-wine` at `255f5cd` | Built with the Wine compiler path and passed three remap-lock tests. The run archive contains RX 6800 timing and resource samples. |
| [avatarmanz/VIctory-7900XTX](https://github.com/avatarmanz/VIctory-7900XTX) | Base fork and [Proton PR #129](https://github.com/avatarmanz/VIctory-7900XTX/pull/129) | Base passed nine synthetic Wine/Lavapipe checks. The Proton candidate passed 205 shader tests and nine targeted runtime checks. Local boots hit an early breakpoint. |
| [cool_fox snapshot](https://github.com/gmankab/victory/tree/cool_fox) | Original `codec-d/cool_fox` at `f4b48ad`, `pareto-residency` profile | Lavapipe hit `0x80000003` before the first captured frame. Corrected Wine/Wayland setup on RX 6800 reached legal terms at about 30 FPS. Nineteen focused tests passed and the kernel logged no reset in that run. |
| [TuxBox](https://github.com/brcly/TuxBox) | `11a278a`; fresh-prefix documentation at `9d76fb3` | 125 Python tests and a hardware Lanes replay passed. The title created its D3D12 device and stopped at `CLWB` before its first frame on this Xeon. |
| [XCL](https://github.com/xcl-dev/xcl) | Earlier `ivall/xcl` checkout and `c160d86`, local `3867e7b`, later `6d051da`, `08db802` and `b49be18` | Stock Wine runs stopped at the heap-vtable or command-list path. With existing [#867](https://github.com/xcl-dev/xcl/pull/867), [#1395](https://github.com/xcl-dev/xcl/pull/1395) and adjusted Wine DLLs, RX 6800 reached the setup/terms screen at 27–29 FPS. Later patched Lavapipe builds presented a first 4K frame. |
| [LooperSalty/AnyXboxX](https://github.com/LooperSalty/AnyXboxX) | Checkout `38cc0fa` and pinned Victory `9a9eab0` | Series X still showed loading art after 3½ minutes. Series S reached black 3D frames after about 2½ minutes at 0.1–0.6 FPS. The standalone Unicorn launcher produced no visible output. |
| [ubiquitarianism/leonida](https://github.com/ubiquitarianism/leonida) | `98c2f777`, `db1a22d`, `61732ac`, `ffcfe68`, `a73a57e` and alias-guard rebases | Wine/Vulkan startup and an existing shadow guard yielded large synthetic/startup gains. Inspected the Linux-native Vulkan tools; full title boot used the PE launcher through Wine. [Comparison and upstream report](https://github.com/ubiquitarianism/leonida/pull/1867#issuecomment-7174840488) |
| [PastiiJay/klbf-lab](https://github.com/PastiiJay/klbf-lab) / [beta](https://github.com/PastiiJay/klbf-lab-beta) | Stable `1c3cc06` and `37ad2d2`, beta `67ee19c` and `f9f2419` | Wine pipeline cache and FXC selection mattered. Beta with an updated compiler rendered the courtyard at 0.1–0.6 FPS. See [the KLBF comparison](benchmarks.md#title-runs). |
| [OpenScarlett](https://github.com/californiaAlmonds/OpenScarlett), [Five-Star Recomp](https://github.com/ThatRecompDev/five-star-recomp), [Palmwave](https://github.com/fablinoo/palmwave) | Local survey refs `46d8e2c`, `12e7f30`, `ecb988c` | Source and repository survey. No verified game benchmark was recorded for these projects. |

## Published branches

- [gmankab/victory](https://github.com/gmankab/victory) is the canonical public Victory development fork. It was renamed from `gmankab/marvisak-victory`. [Tested main snapshot at `d56316bb`](https://github.com/gmankab/victory/commit/d56316bb283495181651a7528b6f0d9c625f32e2).
- [Disroot mirror](https://git.disroot.org/gmanka/victory) had 141 branches at the November 26 snapshot. `main` and `cool_fox` matched GitHub. The mirror includes original PR branches, local integration refs and distinct experimental test branches. The first mirror pass added 136 branches and updated existing refs as needed. The [branch inventory](../data/branches.csv) records the full commit ID on each public remote.
- [cool_fox](https://github.com/gmankab/victory/tree/cool_fox) preserves the original checkout’s Git history. Its first fork work follows `avatarmanz/VIctory-7900XTX` commit `0a43566`. GitHub stopped resolving the original `codec-d/cool_fox` after it was fetched. [Disroot branch](https://git.disroot.org/gmanka/victory/src/branch/cool_fox)
- [gmankab/fxceless-victory](https://github.com/gmankab/fxceless-victory) and [gmankab/zslayerhq-victory](https://github.com/gmankab/zslayerhq-victory) are older submission forks. Their historical PRs retain their original head repositories. The first published Fxceless run used `6fb11d7`; the metadata rebase used `617416b`.
- [gmankab/leonida](https://github.com/gmankab/leonida) is the public Leonida contribution fork, formerly `leonida-contrib` and `ubiquitarianism-leonida`. The original backup is private. [Flatpak packaging repository](https://github.com/gmanka-flatpaks/io.github.marvisak.victory)

## Native snapshots

| Branch or commit | Verification |
|---|---|
| [`test/native-guarded-copy` · `ca99bf8`](https://github.com/gmankab/victory/tree/test/native-guarded-copy) | Public build reference. Wine-free Wayland loading and guarded-copy fixtures on the RX 6800. |
| [`test/native-single-latches` · `80f1118a`](https://git.disroot.org/gmanka/victory/src/branch/test/native-single-latches) | Native story-load experiment. The branch is available on Disroot. |
| [`test/native-wave-emulation` · `9476c83d`](https://github.com/gmankab/victory/tree/test/native-wave-emulation) | Wave64 recovery, narrow Wave32 fallback, and first-lane testing. |
| [`test/native-console-flow` · `36c0981e`](https://github.com/gmankab/victory/tree/test/native-console-flow) | Recovered 179 loading-time compute pipelines on Lavapipe. |
| [`test/native-measured-occlusion` · `b77687b7`](https://github.com/gmankab/victory/tree/test/native-measured-occlusion) | GPU regression passed on RX 6800 with validation. The full title run did not exercise the measured readback path. |
| [`test/native-pixel-helper-lanes` · `e5cf1506a`](https://github.com/gmankab/victory/tree/test/native-pixel-helper-lanes) | Shader validation and pipeline creation passed on hardware and software Vulkan. Both full software runs hit the same late null write. |
| `test/native-empty-buffer-views` · `21990044` and `test/native-xma-ring` · `f717c2e4` | Local test variants. The published [empty-view contribution](https://github.com/gmankab/victory/tree/contrib/native-empty-buffer-views) is separate. The XMA test applied the two original commits from [Marvisak #536](https://github.com/Marvisak/VIctory/pull/536). |

The tested Flatpak names the local `openworld-rx6800` branch and pins commit `748d150` in its source definition. The [Disroot snapshot](https://git.disroot.org/gmanka/victory/src/branch/openworld-rx6800) records the mirrored ref.

## Historical project survey

These counts were observed in the first repository survey and change over time. The first survey recorded total PR counts. Later observations explicitly recorded open PRs.

| Project | Stars | Contributors | PRs |
|---|---:|---:|---:|
| VIctory | 6,882 | 46 | 612 |
| XCL | 5,630 | 58 | 710 |
| Leonida | 828 | 422 | 578 |
| TuxBox | 223 | 5 | 33 |
| Five-Star Recomp | 79 | 1 | 0 |
| OpenScarlett | 72 | 5 | 10 |
| Palmwave | 35 | 61 | 0 |

A later XCL check recorded 7,486 stars, 71 contributors and 899 open PRs. AnyXboxX then had 229 stars, nine contributors and 19 open PRs. The last `cool_fox` check before its original repository stopped resolving recorded 672 stars, 91 contributors and five open PRs.
