# Isolated measurements and build times

**Run by** [@gmankab](https://github.com/gmankab)

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
