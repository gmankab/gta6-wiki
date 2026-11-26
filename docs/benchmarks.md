# Benchmarks

For the test machine and supported configurations, see the [readme](../readme.md). Each FPS figure belongs to a stage and renderer. Timed loading screens and repeated presents can give a high FPS reading while story progress is stalled. The 30 FPS goal corresponds to 33.33 ms per frame with a correct picture and sustained player control.

## Title runs

| Build and renderer | Stage and measured result | Scope of result |
|---|---|---|
| Initial VIctory, stock Proton Experimental, RX 6800 at 3840×2160 | **29.5 FPS** across 1,024 frames. Rendered the startup sequence and responded to setup input through legal notices. | Startup and setup. |
| Flatpak `openworld-rx6800` at `748d150`, Wine 11.0/vkd3d-proton 3.2 | Texture-safe diagnostic rendered the opening street for over 3 minutes at **5–20 s/frame**. Camera stayed fixed during the movement tests. Subsequent stock, copy and partial-texture retries presented the loading artwork at about **30 FPS**. | Visible geometry in the earlier diagnostic. Later retries stalled sooner. |
| Series S compositor, structured buffers and expanded heap, RX 6800/Wine | Menus and opening credits restored. Seven previously dropped producer shaders translated. At least **8,192** dynamic descriptor-slot drops in the earlier build fell to **0** in a run of over 3 minutes with a **262,144**-slot dynamic range. | Test scene remained black and took seconds per frame. [Compositor](https://github.com/Marvisak/VIctory/pull/211) · [Structured buffers](https://github.com/Marvisak/VIctory/pull/704) · [Heap](https://github.com/Marvisak/VIctory/pull/1548) |
| Integrated AMD changes, RX 6800/Wine | Startup reached the skyline and credits. A diagnostic that skipped the texture-remap wait brought the scene up about **90 s** sooner. | Different synchronization behavior. The attract sequence still ran at seconds per frame. |
| Published `6fb11d7` / checkpoint branches, RX 6800/Wine | Verified prologue movement, camera, pause/resume, cellmate dialogue and reaching the guard/corridor. Scripted story runs reached later prologue areas. Shader warm-up failures fell **306 → 267** on the same inputs. | Controlled prologue; partial geometry and lighting. |
| Early Series S stand-in and compositor, RX 6800/Wine | Three compositor tests passed. Early 3D samples in one run took **23–33 s/frame**. A later configuration rendered the opening room at a **7.76 s** median across 13 early 3D frames. | Separate runs with different render paths. |
| Relay and shorter pipeline waits, RX 6800/Wine | Median heavy frame **12.52 → 10.58 s**, 98 versus 19 samples. Median pipeline wait **12.00 s → 135.6 ms**. | Separate runs and scenes. A gain attributable to relay or a specific wait setting remains unmeasured. |
| Integration `1600c90`, RX 6800/Wine | In one **15.5 s** frame, about **15.2 s** had no GPU work queued. Summed worker pipeline waits were **55.8 s**. | Worker waits overlap. [Commit](https://github.com/gmankab/victory/commit/1600c900a590fd4678bd61c887325333cd55f386) |
| Integration `7c8f24c`, RX 6800/Wine | Visible in-engine prologue at **3.4–19 s/frame**. Copy preparation up to **7.1 s**. Graphics queue activity **2.4%** in the first 75 s after draw count increased. | Rendered story sequence with CPU/queue overhead. [Commit](https://github.com/gmankab/victory/commit/7c8f24c704148987486102dccc17ea1d43c14c5a) |
| Write-watch gap, RX 6800/Wine | Heavy-frame median **11.2 s** with 8 MiB gap and **14.0 s** with 1 MiB gap. | Both rendered the prologue. Shots differed, so the default 8 MiB was retained. |
| Page-mapping batching, RX 6800/Wine | Batching off **0.16 FPS**, on **0.11 FPS** after 3D began. | Different amounts of geometry were drawn. Synthetic mapping results below. [FX #1063](https://github.com/fxceless/victory-fork/pull/1063) |
| Guarded copy-back, RX 6800/Wine | **74.5 → 21.5 ms/MB** across two measured reports per build. The enabled run hit a guest watchdog null write and logged no GPU reset. | Small in-run sample. [FX #1032 report](https://github.com/fxceless/victory-fork/pull/1032#issuecomment-7171498465) |
| Parallel GPU checksum, RX 6800/Wine | **2.73 → 0.22 ms** average per measured compare list. GPU idle for over 90% of median logged frame in both runs. | Draw counts differed, so the lists establish the comparison. [FX #1014 report](https://github.com/fxceless/victory-fork/pull/1014#issuecomment-7172695962) |
| `cool_fox` `f4b48ad`, RX 6800/Wine/Wayland | Loading and setup at about **30 FPS**. Breakpoint on the legal terms screen. | Standalone Lavapipe run stopped before any capture. Details in [projects](projects.md). |
| FX upstream rebase candidate `1c37f0ec` versus canonical `d56316bb` on RX 6800 | The scripted candidate stopped on Navigation Experience by frame **300**. The scripted canonical build reached Finish Setup at frame **2048**. The published build and stock `06702fe8` hit the same exception when tested without input in follow-ups. | Setup results. [Controls](#fxceless-startup-controls) |
| XCL plus existing Wine compatibility patches, RX 6800 | **27–29 FPS** in interactive setup before legal notices. | Stock build aborted before first presentation. [XCL #867](https://github.com/xcl-dev/xcl/pull/867) · [XCL #1395](https://github.com/xcl-dev/xcl/pull/1395) |
| AnyXboxX’s pinned Victory, RX 6800/Wine | Series X loading after **3½ min**. Series S reached 3D after about **2½ min**, at **0.1–0.6 FPS** with a black window. | Presentation and renderer telemetry. |
| KLBF Lab stable `1c3cc06`, RX 6800/Wine/Wayland, Series X and FSR off | Watchdog after **7m 23s**. Intro/watchdog retry ran **9m 04s**, black with **20–48 s/frame**. | Hundreds of pending pipelines. |
| KLBF Lab stable `37ad2d2` with Series S and FSR, same hardware | FSR and warm cache initialized. Window stayed blank after **8 min** even with over **1,200 draws/frame**. Earlier cache-fix run retained **1,321 pipelines**. | Older compiler setup. [Merged cache fix](https://github.com/PastiiJay/klbf-lab/pull/1) |
| KLBF Lab beta `f9f2419` with updated FXC, same hardware | Visible 3D courtyard at **0.1–0.6 FPS** over the first 75 s of 3D, with **1.55%** graphics activity. | Cutscene reached. [Merged compiler setup fix](https://github.com/PastiiJay/klbf-lab/pull/10) |
| Leonida Wine/Baseline versus existing alias guard, Lavapipe | First matched late-startup run went **4.3–4.6 → about 44 FPS**, shadow copy **103–106 → about 3.1 ms/submission**. The `ffcfe68` comparison went **4.44 → 44.16 FPS**, eliminating about **564 MiB** aliased copies per submission. | Startup-only comparisons of the same code with the guard toggled. [Leonida #1867](https://github.com/ubiquitarianism/leonida/pull/1867#issuecomment-7174840488) |
| Later Leonida alias guard rebase, Wine/Lavapipe | Median startup **4.36 → 42.69 FPS**, median shadow work **107.91 → 3.46 ms/submission**. | Startup. Both branches used Wine. |
| Wine-free Victory on RADV | Menus **about 30 FPS**. A guarded-copy run reached **0.5 FPS** in 3D before a recoverable AMDGPU queue reset. | Title exited and Niri stayed up in this run. |
| Wine-free shader loop cap 4,096, RX 6800 | Heavy 3D telemetry reported **0.5–1.3 FPS**. One run ended at a breakpoint and one at a guest memory fault. | Both captures were black. Diagnostic cap affects render behavior. |
| Wine-free Victory on Lavapipe | Loading **5–6 FPS** early and **0.3–0.5 FPS** in a heavier story phase. A later Toolbox run completed two world-loading submissions in **57.3 s** and **18.8 s**. | Software Vulkan. |
| Native loading shader pair, Lavapipe | Slow draw up to **1.03 s**. Masking the pair in matched 100 s runs raised late median **0.45 → 21.8 FPS**, reached frame 1024, and yielded a black image. An earlier mask dropped six to seven draws/frame and lowered late median frame time **5.96 → 0.29 s**. | Diagnostic only. Both shaders passed validation. |
| Native loading sampler test, Lavapipe | Point filtering raised late median **0.4 → 1.2 FPS**, reducing the slowest observed draw from **959 → 270 ms**. Nearest-mip alone changed little. | All test captures were black. |
| Native Series S worker count, Lavapipe | Median submission **2.42 s** with 12 workers and **2.40 s** with 24. | The baseline kept 12. [Series S compositor](https://github.com/spidercraft/VIctory/pull/23) |
| Local decoded-BC7 cache, Lavapipe, same binary and first 155 s | Four-list submission median **1,972 → 392 ms** across 32 versus 146 samples. Cached run bound its first mesh pipeline at frame **516** after **111 s**. Control ran 155 s without a logged mesh bind. | Latest capture was still illustrated loading. |
| Native descriptor and XMA sequence, Lavapipe | A 425 s run captured frame **850**. Empty-buffer and XMA variants both reached the illustrated loading screen and hit **460 s** caps. The first instrumented run hit a **305 s** cap before reproducing the descriptor VUID. The **460 s** trace recorded **22 zero-byte CBV calls**, **nine** validation messages and **zero** logged zero-range SRV/UAV calls. | After the null-CBV fix, a matched **460 s** run captured frame **750** with **zero** zero-byte CBV calls and **zero** validation messages. Both runs ended during loading. [Native port](https://github.com/spidercraft/VIctory/pull/27) |

## Fxceless startup controls

These runs used Wine with isolated profiles and the same read-only game directory. The scripted comparison reached different setup pages. In the paired idle runs, the published build and stock Fxceless hit the same guest breakpoint. The faulting instruction and the saved Windows exception context showed a stream writer on the title’s main thread trying to copy 1,728 bytes with 80 bytes remaining.

| Build and configuration | Result |
|---|---|
| Stock `06702fe8`, Series S, scripted setup, RADV | Same breakpoint after 41 s. |
| Stock `06702fe8`, Series S, no input, RADV | Same breakpoint after 24 s with Relay on and after 24 s with Relay off. |
| Published `d56316bb`, Series S, no input, RADV | Same breakpoint after 23 s. |
| Stock `06702fe8` with the known checkpoint, RADV | Same breakpoint in Series S and Series X. The Series X run exited after 26 s. The Series S harness was interrupted after recording the exception. |
| Stock `06702fe8`, Series S, no input or frame captures, RADV | Same breakpoint after 24 s, including the run with intro skipping disabled. |
| Stock `06702fe8`, Series S, no input, DRI hidden and Lavapipe selected | Same breakpoint after 23 s. The runtime found llvmpipe and failed to create its D3D12 host device. No host draws or dispatches ran. |
| `06702fe8` with a read-only crash probe, Series S, DRI hidden | Same breakpoint after 23 s. The 98,304-byte stream had reached byte 98,224. |
| `ceb9e1f7` with a read-only crash probe, DRI hidden | Same breakpoint after 24 s in Series S and 26 s in Series X. Both stopped at byte 98,224 with the same 41 consecutive copies of the pending write already present. |
| Same `ceb9e1f7` Series S probe, DRI hidden, PulseAudio available | No breakpoint before the 70 s cap with Wine's PulseAudio trace enabled or before the 60 s cap with logging matched to the failing control. Both reached logged frame 1,024. |

All five DRI-hidden probe runs executed zero host draws and dispatches. The 41 byte-identical copies were spaced exactly 1,728 bytes apart and filled the end of the occupied stream. The probe compared bytes in-process and logged only lengths, match counts and offsets.

The earlier headless harness denied the PulseAudio socket. Granting it under Wine allowed a real playback stream to start and prevented the breakpoint within both test windows. The 60-second repeat changed only the socket permission from the failing Series S configuration. The normal Flatpak manifest already grants this socket. The title's internal reason for the repeated writes remains open.

## Series S presentation with audio enabled

The RX 6800 runs used Wine and RADV with PulseAudio available, the same checkpoint, and the same scripted input. Each captured the same 25 frames through frame 1,024 and reached its time cap without the startup breakpoint. The newest Fxceless baseline used the read-only stream probe.

| Build and configuration | Result |
|---|---|
| `ceb9e1f7`, 90 s | All 25 captures were black. Host draws and dispatches executed. |
| Published `d56316bb`, 90 s | 13 of 25 captures had visible content. Frame 200 showed the interface, and frame 1,024 showed the illustrated loading screen. |
| Published `d56316bb`, `VICTORY_PRESENT_PLANES=0`, 60 s | All 25 captures were black again when only the first present plane was shown. |

Toggling the compositor on the same build isolated the second plane as the source of the visible Series S loading screen in this test. The captured sequence ended during loading. The raw frame-rate samples in the run index cover startup and loading. [Related Fxceless report](https://github.com/fxceless/victory-fork/issues/425) · [Second-plane proposal](https://github.com/fxceless/victory-fork/pull/188)

### Second-plane proposal on `ceb9e1f7`

Six commits from the proposal were rebased onto `ceb9e1f7`, with a follow-up that carries the interface resource through queued presents. Wine runs used PulseAudio, RADV and the Series S checkpoint. The two runs of the port enabled asynchronous submission and presentation and used the same later Enter schedule. Their logs confirm the switches were enabled. The evidence for the compositor comes from captured frames, which use the synchronous readback path. [Tested branch](https://github.com/gmankab/victory/tree/contrib/fx-series-s-pr188-current)

| Build and capture source | Result |
|---|---|
| Published `d56316bb`, 200 s, original input schedule | The last capture at frame 2,400 showed loading artwork. Whole-card busy samples were **1% median and 25% peak** across 101 readings. System-wide VRAM use peaked at 7,985 MiB. |
| Rebased proposal, 180 s, first-plane readback | All 30 captures were black, although the host logged initialization of the overlay pass. |
| Rebased proposal with composed-buffer readback, 135 s | **26 of 28** captures were nonblack. The same 28 frame numbers in the first-plane run were all black. Frame 1,300 still showed loading. |

The port restored visible output during startup and illustrated loading. Both GPU readback tests ran on the RX 6800 and passed, as did the plane parser test. The recorded frame rates cover startup and loading under different configurations. [Upstream test report](https://github.com/fxceless/victory-fork/pull/188#issuecomment-7191270104)

## Isolated measurements and build times

| Workload | Control and candidate | What was verified |
|---|---|---|
| Intro skip on RX 6800 | Loading artwork at **63 s** without and **56 s** with skip. | One comparison. The option skips a 450-frame opening sequence. [FX #613](https://github.com/fxceless/victory-fork/pull/613) |
| Kache on fresh Victory Cargo target directories | **147 → 35 s** with a warm cache. | Windows-target build in Toolbox. |
| Kache 2.3.1, one vkd3d C compile | Direct **4.41 s**, cold cache **5.35 s**, restored **0.04 s**. | Byte-identical `resource.c` object. |
| Kache 2.3.1, one vkd3d C++ compile | Direct **13.23 s**, cold cache **13.32 s**, restored **0.05 s**. | Byte-identical `dxil_converter.cpp` object. Two fresh Meson builds with the compiler shims also produced one C and one C++ cache hit. |
| Blaze Linux checks | **61 → 21 s** on repeat. | Native build and inspect command worked. Windows cross-build stopped at a host/linker gate. |
| Synthetic 4K sample of 2048×1024 BC7 | Lavapipe about **1.24 s**, RX 6800 RADV **0.064 ms** for the recorded comparison. | Shader texture sample cost depends heavily on renderer. |
| Local BC7 decoded-block cache, software Vulkan | UNORM **458 → 88 ms** and sRGB **430 → 64 ms** in 4K synthetic draws, medians of three warm samples. | Full image bytes matched before and after overwriting the sampled texture. |
| Guarded native memory reads | 64 KiB copy roughly **2× faster**. 16 MiB was slower. | Caps selected path at 1 MiB. Protected-page regression and 14 native VM tests passed. [Spidercraft #8](https://github.com/spidercraft/VIctory/pull/8) |
| Sparse copy-back mask scan | **2.8–4.9× faster** across recorded isolated variants. | 30 FX shadow tests passed. [FX #1094](https://github.com/fxceless/victory-fork/pull/1094) |
| Native CPU copy-back word merge | **2.1–2.8× faster** across the measured cases. | Regression checked merge correctness. [Spidercraft #9](https://github.com/spidercraft/VIctory/pull/9) |
| Native disjoint refresh spans | **192 → 128 KiB copied**. | Lavapipe and RX 6800 readback fixtures. [Spidercraft #10](https://github.com/spidercraft/VIctory/pull/10) |
| XMem batch mapping under Wine | Two runs, individual **830.165 / 820.734 ms**, batched **196.987 / 196.327 ms** for 4,096 groups of four pages. | Readback passed in all cases. [FX #1063](https://github.com/fxceless/victory-fork/pull/1063) |
| Parallel checksum, 64 pieces on RADV | **1.37–1.41 → 0.027 ms** per dispatch across two runs. | RX 6800 and Lavapipe correctness tests passed. [FX #1014](https://github.com/fxceless/victory-fork/pull/1014#issuecomment-7172695962) |
| Shader SSA analysis | Largest case roughly **42 → 0.75 ms** per analysis. | CPU-side shader-analysis workload. |
| Native indirect work budget | Four expensive draws admitted before and two after, matching the budget. Clamping 16,384 valid records was **2.4× slower**. | GPU readback and saturation checks. [Spidercraft #16](https://github.com/spidercraft/VIctory/pull/16) |
| Reuse native indirect scratch | GPU time **25% lower** at one record and **20% lower** at 64 records. | Synthetic RX 6800. [Spidercraft #17](https://github.com/spidercraft/VIctory/pull/17) |
| GPU-local native indirect scratch | Further **16–18% GPU reduction** for 4,096–16,384 records. | Synthetic RX 6800. [Spidercraft #18](https://github.com/spidercraft/VIctory/pull/18) |
| Skip unavailable indirect pipeline | **2.0–3.2 → 0.076 µs/call** over 512 dropped calls. Separate barrier candidate measured within **1%** of control. | CPU recording fixture. [Spidercraft #19](https://github.com/spidercraft/VIctory/pull/19) |
| GPU-local indirect work budget | Up to **31%** less time for repeated small clamps; **13–15%** less for large batches. One small clamp was about **20%** slower. | Replay/reset tests passed. [Spidercraft #20](https://github.com/spidercraft/VIctory/pull/20) |
| Native clean-texture tracking, 52 MiB | No changes **22.4 → 0.12 ms**, 4/104 changed **22.6 → 1.32 ms**, all changed **29.9 → 31.8 ms**. | Alias writes still reached the GPU. [Spidercraft #22](https://github.com/spidercraft/VIctory/pull/22) |
| Native imported host memory | **64 KiB** anonymous memory checked byte for byte in both directions on RX 6800. Shared `memfd` did not match. A separate native vkd3d test batch passed **50** checks. | Feasibility of reducing copies for an individual buffer. |

One heavy Wine frame from the technical review had **13,094.8 ms** total time. The counters reported 10,139.8 ms preparation, 3,915.2 ms uploads, 2,091.8 ms texture preparation, 2,270.7 ms GPU execution, and 6,145.1 ms copy-back. Several timers overlap. Approximately 322 MB was uploaded and 149 MB read back.

## Evidence index

- [Run observations](../data/runs.csv) extract timestamps, exit codes and explicit frame/FPS counters from retained logs. An exit of 124 records a harness timeout. A missing exit means the available log had no exit sidecar.
- [Frame timings](../data/frame-timings.csv) contain the retained runtime timing CSV rows. Read rows in the context of the test configuration and stage.
- [Benchmark log measurements](../data/benchmark-logs.csv) index timing lines in saved benchmark output. They include trials and repeats. Labels and units use the source file’s format.
- [Test results](../data/test-results.csv) index suite summaries. Multiple rows can come from repeated runs of the same suite.
- Filenames in the index identify local evidence. Figures reported only in historical replies or reviews appear in the tables on this page. Captures and title assets are kept out of the public repository.
