# @sillynocodecat benchmarks

Run by [@sillynocodecat](https://github.com/sillynocodecat) on the machine below. All runs are Xbox Series builds of the title (`game_scarlett_master.exe` 1.0.2.1) through Victory-family layers on native Windows, no Wine. [Evidence directory](../../data/benchmarks/sillynocodecat/) · [Benchmark index](../benchmarks.md)

## Test machine

| Component | Configuration |
|---|---|
| Operating system | Windows 11 IoT Enterprise LTSC, build 26100 |
| CPU | AMD Ryzen 5 5600, 6 cores and 12 threads |
| GPU | AMD Radeon RX 7800 XT, 16 GiB VRAM |
| Driver | Adrenalin 26.8.1 on November 24; driver 32.0.32029.2010 for the later runs |
| Memory | 32 GiB DDR4-3200, 64 GiB page file on NVMe, Windows memory compression off |
| Storage | NVMe SSD |

## How these runs were measured

- **FPS** comes from the layer's own log lines (one average every 5 seconds) unless an entry says otherwise. "Median" and "worst 10%" are taken over those lines.
- **Stages** are labelled separately: loading, menus, cinematics and controllable gameplay. Scripted runs classify the window picture as black, letterboxed cinematic or gameplay.
- **Saves.** The prison prologue autosave is used for November 24–26. Two named saves are used from November 26: save 01, Lucia in the cell, and save 02, Jason on the boat at the start of the first boat mission.
- **Builds.** "Local" builds are made from the named public revision plus the listed changes. "Clean" means the public revision as is.

## November 24, 2026 · First launches: VIctory, Palmwave, AnyXboxX

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

**Build and setup** Adrenalin 26.8.1, Series X profile, shader caches made ahead with a local `precompile` command, which is our only change.

| Build | Renderer | Furthest stage | Result |
|---|---|---|---|
| [Marvisak/VIctory](https://github.com/Marvisak/VIctory) `d2a4bae` + precompile | D3D12 hardware, 2 runs | Menus at 30 FPS, entered the world | World at 0.2–1.7 FPS. Both runs ended with a GPU page fault at a low address (`0x27ab000`, `0x3813000`) and `DXGI_ERROR_DEVICE_REMOVED`, 94 s and 54 s after launch. The second run hung the PC. 95–106 world shaders did not translate. |
| [fablinoo/palmwave](https://github.com/fablinoo/palmwave) `b739d34` | D3D12 hardware | One frame at 3 s, then black | The driver built compute pipelines for 18 minutes, 602 in total, at 1–26 s each. Closed by hand. |
| [LooperSalty/AnyXboxX](https://github.com/LooperSalty/AnyXboxX) with Victory `9a9eab0` + precompile | WARP | Menus at 6–15 FPS, about frame 1282 | Device removed on WARP during a 36×1×1 compute dispatch (`3cf39f4495b908d0`). |
| Same | D3D12 hardware | First cinematic with titles, partly lit, with sound | **9.5 minutes in the world without a device loss**, at 0.1–1 FPS. The title stopped on its own `int3` at `+0x63966f`. Its crash report showed 1.4 GB of 55.7 GB commit left. 473 shaders did not translate. |

## November 25, 2026 · victory-gta6 on AMD: the view-heap fault and its fix

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

**Build and setup** Clean [fxceless/victory-gta6](https://github.com/fxceless/victory-fork) `578d459` plus precompile, Series X.

**Result** The first run lost the device at the opening cinematic, around frame 2230. DRED recorded a page fault reading view-heap slot 2^23: NVIDIA caps the view heap at 1M slots and clamps to a null slot, and AMD does not. A local change forcing that cap and clamp on AMD, `VICTORY_PORTABLE_HEAP=1`, gave the first lit, textured picture on this card in the prologue. The change was proposed upstream and later superseded by the maintainer's own AMD clamp. [PR #98](https://github.com/fxceless/victory-fork/pull/98) · [Branch](https://github.com/sillynocodecat/victory-fork/tree/amd-view-heap)

## November 25, 2026 · Victory fork integration night, prison prologue

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

**Build and setup** Local builds on fxceless/victory-fork `1684989`, later `4e17478`, `2119b0e` and `c96b4f2`, each with the AMD heap fix and the listed open PRs. Later lines use local stacks on Trexies/VIwork, which is now removed from GitHub, on [ZSlayerHQ/victory-world-runtime](https://github.com/ZSlayerHQ/victory-world-runtime) and on fork `main` `9376fd6`. Both console profiles. Speed switches are `VICTORY_LATE_WAIT`, `VICTORY_WIDE_BACK` and card buffers, unless a line says otherwise.

**Workload** Automated runs: launch, Enter through the menus, then 5 minutes of the prologue at the cell window. FPS is the gameplay average from the log. "Dark share" is the share of near-black pixels in the gameplay shots, a rough check for missing geometry.

**Result** 55 runs. Highlights:

- **Reference picture** `807d506`, Series X: **3.36 FPS**. With `VICTORY_RELAY=2` instead of the speed switches: 3.74 FPS, +11%.
- **Series S black screen.** Series S presents two planes: a 1920×1080 game plane and a 3840×2160 UI plane with alpha. The layer showed plane 0 only. Fork PR #258, which translates FidelityFX, gave Series S a picture, at 3.94 FPS but with screen-door noise. Our `v_permlanex16_b32` lowering translated one more FSR program and removed the noise: **4.61 FPS**, +28% over Series X.
- **Missing walls and ceiling** came from fork issue #168. They were intermittent under every switch set tried. A missed-write check found 1,919 writes the layer did not see: 1,549 from page guards and 370 from the journal. Fork PR #115 cut guard misses to 97, and walls and ceiling were present in 2 of 2 runs.
- **Our change: list resets on a background thread.** List reset time went from 27 to 5.3 ms per frame, and the run gave 3.50 FPS against 3.22–3.24 before, about +8%.
- **Trexies stack** `bd34087` with our changes. Series S at 4.14 FPS gave the best picture so far. Series X was at 0.58 FPS on the first launch, because graphics pipelines were made during play, and at 3.17 FPS on the second launch.
- **durov2 stack.** Fork PR #786 raised copy-back from 55 ms to 1.5–3 s per frame. The "VIctory bridge" PRs darkened the cell. With both removed: **2.82 FPS** and a lit cell.
- **Ray tracing (`VICTORY_BVH_BUILDER=1`)** on `3c7110c`: 0.3 FPS, and the cell was almost black.

[All 55 runs](../../data/benchmarks/sillynocodecat/2026-11-25-victory-prologue-night.csv) · [Automated run results](../../data/benchmarks/sillynocodecat/2026-11-25-26-autotests.csv)

## November 25, 2026 · Other forks on the prologue

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

| Build | Profile | Result |
|---|---|---|
| AnyXboxX `essai` + Victory PRs #1215, #1407, #492 + precompile, local | Series S | 2.7 FPS in the world against 1.5 for stable AnyXboxX. Needed `VICTORY_SKIP_BVH_BUILDER=1`. |
| [PastiiJay/klbf-lab](https://github.com/PastiiJay/klbf-lab) release, pipelines prebuilt in 28 minutes | Native | About 3.0 FPS in the world, 2.2–3.6. Series S with FSR averaged 3.9 and reached 5.5. Purple and black patches, and the world did not finish loading. |
| [Belobug/VIctory-Belobug-AMD](https://github.com/Belobug/VIctory-Belobug-AMD) `71652a2`, clean | Series X | World at 181 s, then a black cinematic at about 2 FPS. 16.7 GB RAM. |
| [DexNom/VIctory](https://github.com/DexNom/VIctory) `8cca4d228`, clean, `--rt` | Series X | World at 88 s, 0.9–1.0 FPS, no crash. 25,748 shaders translated, 10 refused. Ray-traced lighting worked, with fewer artifacts. |
| durov-main `a590942`, `VICTORY_RELAY=1` | Series X | No device loss. 2.69 FPS from the log, the same as without relay. Copy-back 134–178 ms. |
| durov-main `152aabc`, `VICTORY_INPLACE=1` and `=2` | Series X | 2.6–3.1 FPS. Nothing went in place: title memory is mostly view memory from the page pool, which the in-place path refuses. |

## November 26, 2026 · Belobug, avatarmanz and cool_fox, prologue

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

**Build and setup** Diagnostics builds from source, each fork's own launch switches.

| Build | Profile | Gameplay median | Notes |
|---|---|---|---|
| Belobug `000ffdd4e`, clean | Series X | 4.1 FPS over 8.5 minutes, 5.3–5.8 in the last minute | Run 1 lost the device at about 5 minutes while about 400 pipelines were still building. Run 2 used a warm cache and had no crash. |
| [avatarmanz/VIctory-7900XTX](https://github.com/avatarmanz/VIctory-7900XTX) `39031cb`, clean | Series X | 3.3 FPS, 307 ms | 33 GB RAM, no crash |
| [codec-d/cool_fox](https://github.com/gmankab/victory/tree/cool_fox) `f4b48ad`, clean | Series S | **7.3 FPS**, 137 ms | GPU 74 ms, copy-back 27 ms, clean picture except purple papers on a wall |
| Same | Series X | 2.2 FPS, 457 ms | GPU 401 ms |
| Same, warm pipelines | Series S | 7.8 FPS | |

Shader translation: 25,790 translated and 195 refused on 7900XTX, 25,782 and 203 on cool_fox.

**cool_fox variants, local, Series S, same cell-window scene, warm cache**

| Variant | FPS | CPU ms | GPU ms | Picture |
|---|---|---|---|---|
| Picture fixes only: NaN guard, half-wave PS/VS, #1599, Belobug's purple filter | 8.2 | 46 | 71 | Complete |
| 7900XTX renderer ports | 8.3 | 76 | 36 | Photos and decals missing |
| Everything | 8.6 | 79 | 29 | Photos and decals missing |
| Ports without `PORT_DESCRIPTORS` | 6.1 | 135 | 20 | Complete |

`VICTORY_PORT_DESCRIPTORS` misses about 190 texture records per frame, which is why photos and decals disappear. On this CPU-bound machine the ports move work from the GPU to the CPU and give no net gain.

Manual play, Series S: **11.9 FPS** without ray tracing. With `VICTORY_BVH_BUILDER=1` it fell to 4.8 FPS (GPU 145 ms per frame) and had no crashes.

## November 26, 2026 · durov-test-2 and durov-test-3, boat drive

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

**Build and setup** Both are local diagnostics builds, Series S, warm pipelines.

- durov-test-2 is cool_fox `f4b48ad`, plus a full merge of [Vice-Tay](https://github.com/TaynanGT/Vice-Tay) `fab5286`, which is now removed, plus two shadow commits from XaviFortes: lock-free `has_pending` and AVX2 `word_runs`.
- durov-test-3 is durov-test-2 plus five of our changes:
  - upload buffers without a synchronous `MakeResident`;
  - a queue of changed card copies;
  - `mapped()` without a global lock;
  - higher priority for helper threads;
  - a separate pool for small scratch buffers.

**Workload** Save 02, 3 minutes of gameplay only. The throttle and camera are driven by script, with no cinematics. Two runs each.

**Result**

| Build | FPS median | Worst 10% | ms per frame |
|---|---|---|---|
| durov-test-2 | **12.0** (12.6, 11.3) | 8.7 | 84 |
| durov-test-3, helpers above normal priority | 11.4 (11.1, 11.7) | 8.6 | 88 |

- **Helper priority.** The submit thread dropped from 65 to 59 ms and its heap wait from 18 to 9.5 ms. Total CPU rose from 493 to 550 ms per frame, so the frame got slower on 6 saturated cores. The priority boost is now off by default.
- **Without the priority boost,** the first three changes gave +6% FPS in cinematics, 14.2 to 15.1, and +12% for the worst 10% of frames.
- **Run-to-run spread** of durov-test-2 was larger than the gap between builds.
- **Warm pipelines.** Making the 83,600 known pipelines ahead with `victory warm` cut the post-load slowdown from about 150 s at 3–4 FPS to about 40 s at 7–12 FPS.

## November 27, 2026 · Belobug rx9070xt branch and VIctory-RDNA

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

**Build and setup** Clean Belobug `cd8dfc06a` with its own `play.ps1` switches, diagnostics build. Series S unless noted. 25,790 shaders translated ahead. All 83,595 pipelines were made ahead from the durov-test-2 pipeline book in 16 minutes with 0 failures, because the book is portable between Victory builds.

**Workload** Save 02, about 2 minutes of manual driving.

| Setup | FPS median | Worst 10% | ms per frame |
|---|---|---|---|
| Ray tracing (`VICTORY_BVH_BUILDER=1`) | 10.2 | 8.1 | 98 |
| No ray tracing | 11.7 | 10.5 | 86 |
| durov-test-2 for comparison, no ray tracing | 12.0 | 8.7 | 84 |
| Series X, 2560×1440 output at full scale, ray tracing | 7.7 in play, 7.3 in cinematics | 6.2 | |

- **Ray tracing.** All 53 builder programs ran, and ray tracing cost about 13%. With it, reflections were correct.
- **Artifacts:** black streaks in water reflections and ghosting behind the boat.

[XaviFortes/VIctory-RDNA](https://github.com/XaviFortes/VIctory-RDNA) `2c5a395`, clean release build with its `rdna-preset.json` (base + fast), Series X, 1440p, ray tracing, save 01. It was played by hand and rated poor. FPS was not recorded. Its diagnostics build did not compile at that revision.

## November 27–28, 2026 · eikkapine/victory-amd, opening cinematics and the boat

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

**Build and setup** [eikkapine/victory-amd](https://github.com/eikkapine/victory-amd) `2b88c2e`, release build from source.

- **Switches:** the installer's RDNA3 launcher, copied one to one: FSR through the FidelityFX SDK 2.4.0 signed DLLs, `VICTORY_FPS_CAP=30`, `VICTORY_FRAME_INTERVAL_MS=33.333`. The SDK serves FSR 4.1.2 on this RDNA3 card.
- **Without the DLLs** the 4K FSR preset crashes the title's own console FSR4 with a division by zero.
- **Later builds** add our DualSense HID reader. It is input only and does not change the shader cache key.
- **Shaders and pipelines:** 25,790 shaders translated ahead. Pipelines were recorded in one cold run, 83,562 in total, then made ahead in 16 minutes with 0 failures.

**Workload** A scripted scenario that runs the same way each time:

- launch with save 02;
- Enter on the start screens;
- the opening cinematics play unskipped for about 5 minutes;
- then driving mode, throttle and camera turns.

Cinematic figures are comparable between runs. The drive path differs from run to run, so drive figures are not. Free RAM and page reads come from Windows counters every 2 seconds.

**Result, cinematics**

| Setup | FPS median | Worst 10% | Below 10 FPS | GPU busy | Free RAM, minimum |
|---|---|---|---|---|---|
| Its BEST launcher: Series X, 4K output, FSR performance | 18.8 | 11.6 | 8% | 90% | 1.5 GB |
| 2560×1440 output, FSR quality | 20.2 | 11.1 | 2% | 75% | 2.8 GB |
| 1440p + `VICTORY_RESIDENCY=1` | **20.6** | **13.2** | 2% | 76% | 4.5 GB |
| 1440p + residency + `VICTORY_STAGING_IDLE_MS=2000`, ported from DoubleGhosty/cool_fox `cf3f615` | 20.8 | 12.4 | 6% | 77% | 2.6 GB |
| 1440p + residency + `VICTORY_DSTORAGE_IO=direct`, staging 256 MB | 18.8 | 10.5 | 6% | 73% | 5.2 GB |
| Series S, residency | 22.2–24.6 | 13.1–14.6 | 6% | 67–70% | 3.9–4.4 GB |
| Series S + `VICTORY_PORT_CONSTANTS_ORDER_ALONE=all` | 21.0 | 14.8 | 4% | 74% | 4.3 GB |
| Series S + ray tracing + the same | 17.5 | 9.8 | 9% | 77% | 1.7 GB |

- **Dips in the cinematics** happen at the same scene change in every run. Pages read from disk reach 28,000–41,000 per second, 2,200–2,900 separate reads of 40–70 KB each. Meanwhile free RAM is down to 1.5–3 GB and the GPU sits half idle.
- **Direct DirectStorage reads** removed the paging but lost FPS.
- **Series S** gives the title 7.6 GB instead of 13.5 GB and keeps free RAM above about 4 GB.

**Memory** A diagnostics build split the process memory in the Series X 1440p run, with residency on. The title's own memory takes about 12 GB of RAM, counting heaps, page views and the pool pages also mapped by renderer port 1. The layer takes about 12–13 GB:

- process heap 2.8 GB;
- write-combined upload memory 3.5 GB resident of 13.9 GB committed;
- other private allocations 4.2 GB;
- title memory windows 1.3 GB.

The AMD shader cache had grown to 15 GB across several builds. About 1 GB of it was resident. [Memory map](../../data/benchmarks/sillynocodecat/2026-11-27-eik-amd-memory-map.csv)

**Tree drop-out** Before the boat ride, tall mangroves vanished for single frames: 2 drop-outs in 24 consecutive frames, with the plant behind them showing through. Renderer port 1 shares barrier-scoped batches for indirect draws whose arguments it reads in place. Those batches clamp records before the culling pass writes them. The source comment itself names vegetation flicker as the result. With `VICTORY_PORT_CONSTANTS_ORDER_ALONE=all` the same shot had 0 drop-outs in 24 frames, at no FPS cost. Clips were captured from the game window with ffmpeg `gfxcapture`, with every presented frame kept.

**Judder, unresolved** In the boat dialogue the picture moves back and forth. The time between presented frames alternates around 33/67 ms, and the motion each frame shows does not follow that time. The correlation between a frame's horizontal step and its present interval is −0.36 to 0.17, where 1 would mean the motion follows the time. The result was the same with the tree fix, with ray tracing and without the 30 FPS cap. Frames reach the screen with varying delay after the title drew them. `VICTORY_SUBMIT_FRAMES=0` is untested. [Frame pacing](../../data/benchmarks/sillynocodecat/2026-11-28-eik-amd-s-frame-pacing.csv)

**Ray tracing on Series S** The picture is nearly the same, with slightly deeper shadows under caps, inside the boat and indoors. FPS is −17% and free RAM much lower. Without ray tracing, the layer fills the title's traced indirect light with an estimate taken from the frame, `VICTORY_AMBIENT`. That is why the difference outdoors in daylight is small.

[All scenario runs](../../data/benchmarks/sillynocodecat/2026-11-27-eik-amd-scenarios.csv)

## November 28, 2026 · eikkapine/victory-amd, manual play

**Run by** [@sillynocodecat](https://github.com/sillynocodecat)

**Build and setup** The `2b88c2e` build from the entry above, with the tree fix (`VICTORY_PORT_CONSTANTS_ORDER_ALONE=all`). Ray-tracing pipelines from a Series S run were added to the Series X pipeline book: 5,007 more pipelines, made ahead in 30 s. The window was made borderless fullscreen from outside, with no change to the layer.

**Workload** Save 02, played by hand: opening cinematics, then the boat. FPS is the median of the layer's 5-second lines with at least 1,000 draws per frame, which covers the cinematics and play together.

| Setup | World time sampled | FPS median | Worst 10% | Picture |
|---|---|---|---|---|
| Series S, ray tracing, `VICTORY_SUBMIT_FRAMES=0` | 16.8 min | 17.7 | 13.9 | Good |
| Series X, 4K output, FSR 4.1.2 quality from 2560×1440, ray tracing, `VICTORY_AMBIENT_AIR=1.5`, 3840×2160 monitor | 6.8 min | 11.6 | 7.3 | Best seen on this machine |
| Series S with a 1280×720 output (`VICTORY_OUTPUT`), which draws 853×480 | Not measured | Not measured | Not measured | Too pixelated to play; the interface text was unreadable |

- **Trees still sway back and forth** in both ray-traced sessions, with `VICTORY_SUBMIT_FRAMES=0` as well. The drop-out fix does not cover it, and this matches the uneven frame presentation in the pacing data above.
- **Series S output** cannot go lower without a loss: below 1920×1080 the interface goes with it. `VICTORY_FFX_SCALE` crashed the title on Series S in an earlier fork test (division by zero at `+0x28f03e8`).
