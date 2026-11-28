# @gmankab benchmarks

Run by [@gmankab](https://github.com/gmankab) on the machine below. Each result identifies its renderer and workload where recorded. The tests cover the Xbox and PS5 builds. [Benchmark index](../benchmarks.md)

## Test machine

| Component | Configuration |
|---|---|
| Operating system | Fedora Linux 45 Silverblue, deployment 45.20261112.0 |
| Kernel | 7.3.3-300.fc45.x86_64 at the November 26, 2026 snapshot |
| CPU | Intel Xeon E5-2670 v3, 12 cores and 24 threads, 2.30 GHz base |
| GPU | AMD Radeon RX 6800, Navi 21, 16 GiB VRAM, AMDGPU kernel driver |
| Memory | 48 GiB DDR4, 46 GiB reported usable by Linux |
| Desktop | Niri 26.04 on Wayland |
| Graphics | RADV for hardware tests. The Fedora 45 Toolbox snapshot had Mesa 26.2.7. Some software runs used a local Mesa 26.2.6 Lavapipe build. |
| Wine | Stock Proton Experimental, Wine 11.0 and GE-Proton were used in different runs. Each comparison identifies its configuration when recorded. |

## PS5 KytyPS5 startup

**Run by** [@gmankab](https://github.com/gmankab)

KytyPS5 official Linux release `61db2f8` booted `PPSA38500` version `01.002.001` from the read-only package view on a Xeon E5-2670 v3 and Radeon RX 6800. The test used a private 1280×720 Wayland session with Weston’s GL renderer and a virtual controller. REA captured the launcher lifecycle, Kyty’s overlay measured presentation rate, and AMDGPU counters confirmed graphics work. This run used SDL’s dummy audio driver. [KytyPS5 revision](https://github.com/KytyPS5/KytyPS5/tree/61db2f8874d4a94fd2908a87cefa973ddc48ef6e) · [Package path](../projects.md#ps5-runtime-checks)

A virtual controller advanced the 30 FPS initial setup screen to the eagle intro. The stock CPU path presented the intro at about **1.8 FPS**. With `--amd-cpu`, the same intro presented at **30.0 FPS** and passed through to the Rockstar logo. The loader reported 22,260 native replacements for `VRSQRTPS`, six for `EXTRQ`, and ten for `CLWB`. The flag still uses traps for some instructions. The flag changes several instruction families together. [CPU patch selection](https://github.com/KytyPS5/KytyPS5/blob/61db2f8874d4a94fd2908a87cefa973ddc48ef6e/src/loader/runtimeLinker.cpp#L1612)

A 10-second sample in the 720p setup menu had 300 Wayland surface commits, **30.00 FPS**, a 33.33 ms median interval and a 33.48 ms p99. AMDGPU attributed 3.6% graphics-engine activity to the emulator over that window.

The first run through setup streamed assets for about 2½ minutes and started compiling opening-scene shaders. Kyty then reported **no game frame for 31 seconds** before a guest exception at `0x908bd3a40`. The emulator averaged 1.2% graphics-engine activity during the final 26 seconds before the fault. The compositor still received occasional overlay refreshes. The kernel logged no GPU reset. [Upstream report](https://github.com/KytyPS5/KytyPS5/issues/2557)

The repeat run seeded the shader and pipeline caches and loaded 203 shader programs and 188 pipelines. It reached the illustrated loading screen and reproduced the same guest exception. Opening black frames and program startup are the furthest verified stock results so far.

The prologue autosave produced during the Xbox tests passed the importer’s header checksum. The importer copied the story blob byte for byte into a separate PS5 profile, retaining the PS5 settings. Stock Kyty reproduced the same guest exception, and a file watch recorded no read of the imported story blob during its observed window. A separate repeat using `--guest-tsc` also reached the same exception. [Save importer](https://git.disroot.org/flo/kyty/src/commit/46ee35c30decd065db378233ad9a600c9f10bbc3/docs/xbox-saves.md)

The Disroot Kyty fork at `46ee35c3` presented about 30 guest flips per second on the illustrated loading screen. Its first launch exited with a userspace `SIGTRAP` on the graphics thread during 3D startup. The repeat recorded no guest flips across 11 consecutive 10-second windows while shader and pipeline compilation continued. One pipeline wait lasted 8.85 seconds. Over a 65.2-second sample from that stall, AMDGPU attributed 0.20% graphics-engine activity to Kyty and reported up to 7.79 GiB of VRAM. The figures describe the loading-to-3D transition. [Tested fork revision](https://git.disroot.org/flo/kyty/src/commit/46ee35c30decd065db378233ad9a600c9f10bbc3)

The cold run later produced three guest flips after the long loading gap. A visible 3D night scene appeared after the first of those flips, and player control remained unverified. The run ended at the 390-second test cap after the emulator reported holding the guest clock for 201.6 seconds while GPU queues waited for compilation.

On the same GPU, Kyty fork revision `8072a783` precompiled the cold run’s saved recipes offline with 12 workers. It translated 1,092 modules and created 984 pipelines in 35.2 seconds, with zero reported failures and zero recipe pipelines missing a stage. The following title launch reported zero recipe modules left to translate. [Offline precompile change](https://git.disroot.org/flo/kyty/commit/8072a7834520353e0a79acd7f5f9d059471f1711)

Warm-cache retries of `8072a783` reached 3D startup and raised `SIGTRAP` inside RADV on Mesa **26.2.6** and **26.2.7**. The graphics stack reached `Addr2ComputeSurfaceInfo` during Vulkan image creation. A Vulkan-validation retry confirmed that Kyty requested **12 mip levels for a 512×512×1 image**, whose complete chain has **10** levels. The 30 FPS samples still describe loading. [Vulkan image creation limits](https://docs.vulkan.org/refpages/latest/refpages/source/VkImageCreateInfo.html#VUID-VkImageCreateInfo-mipLevels-00958)

A local Kyty change reused a compressed backing for writable mip views with identical guest layouts. The 390-second run reached the night scene without the earlier mip-count trap and presented six 3D frames after the loading gap. Their five intervals ranged from **1.70 to 16.53 seconds**. AMDGPU attributed **0.68%** graphics-engine activity to Kyty over 214.3 seconds around that transition, with a **peak of 8.28 GiB** of VRAM. The emulator reported holding the guest clock for 235.2 seconds while its queues waited for compilation. Camera and movement input were sent, and control remained unverified. The test overlapped local C++ builds. [Kyty validation report](https://github.com/KytyPS5/KytyPS5/issues/2557#issuecomment-7220779480) · [Kyty mip-storage PR](https://github.com/KytyPS5/KytyPS5/pull/2591)

A replay seeded with **1,529 shader modules and 1,358 pipelines** reached sustained presentation in the night scene. Guest flips 2,881–3,056 ran at **2.56 FPS over 68.27 seconds**, including compilation gaps. AMDGPU attributed **16.36%** graphics-engine activity to Kyty over the first 105.2 seconds after the scene was captured. The virtual controller opened the pause menu, moved the selection from Map to Game, and closed it with Start. The pause-menu overlay showed 10.8–12.0 FPS. In-scene camera and movement remained unverified. The run ended at the 460-second cap with the guest clock held for 80.0 seconds on compile waits. [Kyty mip-storage PR](https://github.com/KytyPS5/KytyPS5/pull/2591)

The next Disroot revision, `cdb6f374`, changed the shader resource walker's memo table. An REA-captured cache verification regenerated **1,820 of 1,820** modules in **66.8 seconds**, with zero differences and zero missing recipes. The resource-tracking suite passed with the old and new memo tables compared. [Disroot shader resource walker change](https://git.disroot.org/flo/kyty/commit/cdb6f374a3776e23e2173dad7857b5a366c897d2)

A CPU profile of Kyty's GPU command thread attributed **20.0% of weighted sampled instructions** to scanning the image heap for changed images in one 17-second stretch. A local patch indexed the affected entries by image serial. REA captured the passing resident image-heap fixture with Vulkan validation, including image slot reuse and layout transitions after an image was freed. Two 630-second title runs started with the same **2,716 shader modules and 2,423 pipelines**. AMDGPU attributed **31.30%** graphics activity to the baseline and **34.25%** to the indexed variant during the nearly 445-second sample starting 185 seconds into each run. Both reached parts of the opening cinematic with incomplete geometry. They entered 3D at different guest flip counts and ended in different scenes, so these figures do not establish a frame-rate gain.

In the same baseline run, held-image retries and DCC metadata retries accounted for **26.9 million of 37.6 million** texture lookups across 76 five-second intervals with at least 1,000 image-heap rebinds. The next Disroot revision restricts those retries using the changed guest memory ranges. [Maintainer benchmark](external.md#disroot-kyty-image-heap) [Range-aware heap change](https://git.disroot.org/flo/kyty/commit/d1b1ba83eff534f582dbdd15fc04e40b674c7087)

On Disroot revision `b02adf9e` with the mip-storage fix, REA captured a full verification of the latest cinematic run's shader cache. The verifier translated **3,098 of 3,098 modules** again in **123.9 seconds**, with zero differences and zero missing recipes. The following launch reused all 3,098 modules and 2,803 pipelines. [Disroot revision](https://git.disroot.org/flo/kyty/commit/b02adf9e4735a9b56c2234c5620d983b9534e868)

That launch displayed the Continue prompt with startup notices enabled. Pressing Cross led to a read of the imported slot's metadata. The file watch saw no read of the story blob through the opening cinematic. In 19 five-second windows covering the first 3D scenes, guest flips averaged **2.25 FPS**. AMDGPU attributed **20.80%** graphics-engine activity to Kyty over a matching 94-second sample. The range-aware heap counters recorded **9.54 million skipped** and **1.45 million executed** lookups. Stick inputs did not establish camera or character control during the cinematic. Holding Cross brought up Skip, and a second hold advanced to a loading screen. The Molier build was compiling in the background during part of this run. [Range-aware heap change](https://git.disroot.org/flo/kyty/commit/d1b1ba83eff534f582dbdd15fc04e40b674c7087)

The run opened and read the imported story blob about **386 seconds** after launch. When 3D returned, frames showed an outdoor street and a later cinematic with missing geometry. In the sampled **185 seconds** of 3D, Kyty presented **348 guest flips (1.88 FPS)** and used **23.60%** of the RX 6800 graphics engine over a 183.9-second hardware sample. The graphics command thread averaged **77.2% of one CPU core**. Opposite stick inputs did not establish camera or movement control. The 30 FPS black loading stretch immediately after the skip averaged **2.91%** graphics-engine activity. The run reached its 875-second cap with the guest clock held for **130.5 seconds** during shader compilation.

During that black loading stretch, a 14-second profile of the busiest guest thread attributed **33.66% of sampled instructions** to Kyty's x86 instruction emulator and **14.38%** to its exception handler. Line-level samples included the AMD `CLZERO` handler on the Xeon, which lacks that instruction. These weights describe one thread during loading. A separate 3D graphics command-thread profile showed the image heap, resource materialization, and texture-cache work. [Kyty CLZERO trap implementation](https://git.disroot.org/flo/kyty/commit/dc69e6b4e18e021621d48d50e6c8549db77079ce)

## November 28, 2026 · Molier Kyty 3D startup

**Run by** [@gmankab](https://github.com/gmankab)

**Build and setup** Molier `bringup` at `b4957bc7`, built in Fedora 45 Toolbox. The RX 6800 ran the title through RADV 26.2.6 in a private 1280×720 Wayland session. The fork opened its own empty shader store. [Tested revision](https://github.com/Molier/KytyPS5/tree/b4957bc741631dcad798236482f14530444d0088)

**Result** The Continue prompt and illustrated loading screen rendered at about 30 FPS. The fork then recorded at least **30 seconds without a frame** while processing 3D work, with a **5.27-second** compute-pipeline build among the reported waits. The emulator logged a fatal exception at about 320 seconds. REA independently decoded the failing guest instruction as a direct write to address zero. The fork already provides the VideoOut import mentioned in one report for this crash address. The logs establish a stall and subsequent guest abort, with the condition that triggered the abort still unverified. [Related Kyty compatibility report](https://github.com/KytyPS5/KytyPS5/issues/2571)

## November 28, 2026 · Kyty CLZERO and image-heap profile

**Run by** [@gmankab](https://github.com/gmankab)

**Build and setup** Disroot Kyty `b02adf9e`, with the compressed mip fix and a local loader change that lowers AMD `CLZERO` to ordinary x86-64 stores on the Xeon. The launch used host RADV 26.2.6, private 1280×720 Wayland, and a cache seeded with 3,336 shader modules and 3,036 pipelines from the preceding run. The loader reported **2,278** instructions replaced in the main executable. [Disroot revision](https://git.disroot.org/flo/kyty/commit/b02adf9e4735a9b56c2234c5620d983b9534e868)

**Result** An early 115-second sample of 3D cinematics had **130 guest flips (1.13 FPS)**. The graphics command thread averaged **87.8% of one CPU core**. AMDGPU attributed **15.20%** graphics-engine activity to Kyty over a 109.4-second DRM sample during that stretch. A later cinematic sample had **120 guest flips in 100 seconds (1.20 FPS)** and **19.41%** graphics-engine activity over 93.9 seconds. The sequence showed a night street and continued into the credits. Stick tests left player control unverified. The run reached the 945-second cap during a black loading screen. The earlier run followed a different scene sequence, so the results leave any speedup from replacing `CLZERO` unmeasured.

**Profile** REA captured a report from 1,273 weighted instruction samples on the graphics command thread near the night street. Four source-line entries for the full image-heap scan accounted for **24.20% of sampled instruction weight**. The profile measures CPU work on that thread over 20 seconds. [Disroot image-heap source](https://git.disroot.org/flo/kyty/src/commit/b02adf9e4735a9b56c2234c5620d983b9534e868/src/graphics/host_gpu/renderer/pipeline/imageHeap.cpp#L563)

**Follow-up fixture** On Disroot Kyty `d054552d` with the image-serial index applied, the resident image-heap fixture passed on the RX 6800 with Toolbox RADV 26.2.7 and Vulkan validation. The fixture checked descriptor behavior and allowed the GPU's coarse robust-buffer reporting. REA captured the test exit and fixture result. [Disroot base revision](https://git.disroot.org/flo/kyty/commit/d054552ded4f5a4e26b5c5a83b507d1838925fd6)

## November 28, 2026 · Disroot Kyty with the shader loop limit

**Run by** [@gmankab](https://github.com/gmankab)

**Build and setup** Disroot Kyty `d054552d` with the compressed mip fix and the ordinary-store `CLZERO` change, using host RADV 26.2.6. Kyty loaded 3,336 modules and 3,036 pipeline entries from a cache rebuilt for the new default shader loop limit. Linux reported a read-only package mount. [Disroot revision](https://git.disroot.org/flo/kyty/commit/d054552ded4f5a4e26b5c5a83b507d1838925fd6) · [Shader loop limit](https://git.disroot.org/flo/kyty/commit/17b25950d3d1a6144174ec2e2922499b5603c48f)

**Result** One stretch of the street cinematic presented **605 guest flips in 155 seconds (3.90 FPS)**. AMDGPU recorded **33.50%** graphics-engine activity over the corresponding 154.3-second hardware window. The graphics command thread averaged **78.4% of one CPU core**. The imported save opened and was read about **503 seconds** after launch. The street lacked most solid geometry and the scene did not respond with visible camera or locomotion changes during the stick tests. A later close-up cinematic presented **77 flips in 40 seconds (1.93 FPS)** with **30.31%** graphics-engine activity over 41.1 seconds.

**Capture and profile** The four-frame recording completed during illustrated loading, before the first visible 3D sequence. A later 18-second instruction profile of the street cinematic collected 1,017 samples. REA captured a report showing **1.86% of weighted instructions** on one line of the full image-heap scan. The scan's earlier 24.20% result covers a different scene and build.

**Loading replay** REA captured runs in baseline, indexed, indexed, baseline order, with three passes per run. All 12 passes completed four flips and four submissions with zero pacing timeouts. All four final PNGs matched byte for byte. The warm passes took **0.518–0.542 s** on the baseline and **0.513–0.524 s** with the index. These ranges overlap, and the capture covers illustrated loading.

## November 28, 2026 · Kyty 3D street capture and shader cache verification

**Run by** [@gmankab](https://github.com/gmankab)

**3D workload** A second launch of Disroot Kyty `d054552d` with the same compressed mip fix and Xeon loader change used host RADV 26.2.6. Before the recorder started, it issued **328 guest flip requests in 80 seconds (4.10/s)** during the night street cinematic. In the corresponding 79.1-second sample, AMDGPU attributed **29.28%** graphics-engine activity to Kyty. The graphics command thread used **76.56%** of one CPU core. REA captured the calculation from the runtime and DRM counters. The four-frame recording ended after 189.6 seconds of recording overhead, with 20,359 call records. Those timings describe the recorder, whose per-call memory comparisons slow the title. The later skip reached black loading before the time cap. The story save watch saw no read in this run.

**Cache verification** The newer Disroot revision `ee39d796` switches the shader loop limit off by default. On this machine it retranslated **3,465 of 3,465 stored modules** from an older uncapped store in **123.9 seconds** and retained each module and all **1,719** stored pipeline records. Its shader-store fixture passed under REA in Fedora 45 Toolbox. [Default loop setting](https://git.disroot.org/flo/kyty/commit/f5281f6020f4b7b04d0305a91f6868a9a2d59958) · [Partial store carry-over](https://git.disroot.org/flo/kyty/commit/ee39d796f9e5e826e46eafdbda5062cb3e2d2ee4)

**3D replay** The cold baseline reached its 150-second limit during shader and pipeline compilation. A retry started with copies of the title run's shader and driver caches and reached its 115-second limit before completing a pass. REA captured both exits. These attempts yielded no comparison between the scan and index in 3D.

## November 28, 2026 · Kyty zero-copy import failures on the RX 6800

**Run by** [@gmankab](https://github.com/gmankab)

**Build and setup** Disroot Kyty `ee39d796` with the compressed mip fix and the reviewed streaming-store `CLZERO` patch. The 1280×720 Wayland session ran host RADV 26.2.6. Kyty reported a 4K output and the title registered two 3840×2160 buffers. Zero-copy guest-memory promotion was enabled. [Disroot revision](https://git.disroot.org/flo/kyty/commit/ee39d796f9e5e826e46eafdbda5062cb3e2d2ee4) · [Zero-copy implementation](https://git.disroot.org/flo/kyty/commit/a89247f3dcd0f7ed72194c01eea527e6f92c3894) · [CPU patch](https://github.com/KytyPS5/KytyPS5/pull/2599)

**Result** REA counted **3,973** reported import refusals and zero imports in the observed log prefix. The most frequently refused range accounted for **2,505** attempts. The opening cinematic issued **222 flip requests over 105 seconds (2.11/s)** and used **18.58%** of the graphics engine over 103.1 seconds. After the title read the imported save at about **389 seconds**, a subsequent 3D sample recorded **69 flip requests in 60 seconds (1.15/s)** and used **26.08%** of the graphics engine over 59.1 seconds. The command thread used **87.28%** and **80.93%** of one CPU core in those windows. This run used a different executable and settings from the earlier guarded-shader run.

The Linux kernel killed the emulator during a global out-of-memory event about **862 seconds** after launch. The last sampled 3D window recorded seven flip requests in five seconds. The run had reached the credits and additional 3D scenes. Camera and locomotion remained unverified.

## November 28, 2026 · Kyty with 1080p video-out and a failed-import fallback

**Run by** [@gmankab](https://github.com/gmankab)

**Build and setup** The same Disroot base and shader-cache seed, with the local image-heap index toggle and a change that keeps the resident buffer after Vulkan refuses a zero-copy promotion. `--output-1080p` produced two **1920×1080** registered video-out buffers and the texture log included a **1280×720** target. Host RADV was 26.2.6. The initial Continue prompt occupied the upper-left quarter. The following 3D scene filled the window. [Disroot 1080p mode](https://git.disroot.org/flo/kyty/commit/1e9c7b99cc8d2fbca594c06449d397584ee5468b)

**Result** REA counted **61 refused imports**, each with a different logged address and size, and no successful import in that sample. The night street cinematic issued **231 flip requests in 90 seconds (2.57/s)**. AMDGPU recorded **22.43%** graphics-engine activity over 89.1 seconds, and the command thread averaged **83.08%** of one CPU core. Output resolution, the loaded frames, and the image-heap mode differ from the 4K run, so its flip-request rate serves as a separate observation.

**Within-run image-heap comparison** In three consecutive 15-second samples, the scan, index, and scan used **192,102**, **157,506**, and **172,168** command-thread instructions per draw or dispatch. The three samples averaged **1,680**, **2,128**, and **2,592** operations per flip request. This shows lower CPU instruction cost per operation in the indexed sample, with a changing cinematic workload. Disroot subsequently landed its own serial index and a fix for membership when an element resolves twice. [Disroot index](https://git.disroot.org/flo/kyty/commit/94275a4ed7076dfb1a6abd00d0b281aae88c60cf) · [Membership fix](https://git.disroot.org/flo/kyty/commit/d2d63985ad20924c416f674508888e594a772681)

**Regression test** The RX 6800 Vulkan fixture forced rejection of a mapped guest buffer. It requested promotion twice and verified one import attempt, zero copies and the same Vulkan buffer still registered for the page. REA captured the passing exit with Toolbox RADV 26.2.7 and Vulkan validation.

**Later story sequence** After dismissing a “Couldn't save” prompt, the run reached a daylight scene with characters, buildings, and missing geometry. The next recorded 150-second window averaged **1.15 flip requests/s**. A later 195-second window averaged **0.63 flip requests/s**, including shader compilation. Opposing camera inputs, forward and backward movement, and a late pause attempt did not establish control. The emulator wrote save-memory files earlier in the run. The cause of the save prompt remains undetermined.

## November 28, 2026 · Kyty 1080p presentation retest

**Run by** [@gmankab](https://github.com/gmankab)

Disroot Kyty `70f82dbb` includes a dedicated Vulkan copy queue for buffer readbacks. A local candidate also restricted the prepared frame to the resolution registered by the title. With host RADV 26.2.6, the 1920×1080 Continue screen still occupied the top-left **640×360** of the 1280×720 Wayland output. The extent change failed the title test. REA captured the readback batch, detached submission, and refused-import fixtures passing with Vulkan validation on Toolbox RADV 26.2.7. These fixtures exercise the readback and fallback behavior. [Disroot copy queue](https://git.disroot.org/flo/kyty/commit/70f82dbb504a73cfb06b869a4e9ec70ae7ca2196)

**Dedicated copy queue comparison** The title opened and read the imported story save during loading. In the first 3D cinematic, I switched the queue off, on, off, and on in the same process. REA summarized five complete five-second intervals per setting after each switch. No new pipelines were created in these intervals. Kyty's performance summary counts accepted guest flip requests when they enter the video-out queue.

| Sample | Dedicated queue | Flip requests/s | Draws and dispatches per flip request | Readback handler µs/call | Graphics engine | Compute engine |
|---|---|---:|---:|---:|---:|---:|
| 1 | Off | 2.56 | 1,730 | 397 | 17.74% | 0.00% |
| 2 | On | 2.68 | 1,894 | 463 | 16.19% | 2.82% |
| 3 | Off | 2.72 | 1,881 | 400 | 19.40% | 0.00% |
| 4 | On | 1.96 | 2,267 | 474 | 14.76% | 2.53% |

On the dedicated path, each readback took **296–308 µs** on average. With the queue off, the fused path took **413–433 µs** on average. The handler spent more time overall with the queue on, and the remaining readbacks that flushed the graphics queue took longer. The cinematic workload changed across samples, so the throughput effect remains unresolved.

**CPU write-fault window comparison** Later in the same run, I alternated the write-watch window between **64 KiB** and **256 KiB**, with the dedicated queue enabled. REA summarized four 25-second windows. Write faults per 10,000 draws and dispatches were **2,829 → 2,599 → 3,935 → 1,471**. Buffer uploads were **40.8 → 66.2 → 59.8 → 80.3 MB/s**. Summed fault-handler time across threads rose in both 256 KiB samples. Scene changes and shader compilation prevent attributing the differences in flip-request rate to the window size. The run returned to 64 KiB. A lower-priority build was running during both comparisons.

**Later opening scenes** Without a cinematic skip, the 1080p run issued **246 flip requests in 215 seconds (1.14/s)**. AMDGPU recorded **52.94%** graphics-engine activity and **1.17%** compute-engine activity over the corresponding 212-second sample. The command thread used **393.9 ms of CPU time per flip request**. The next 90-second window averaged **1.67 flip requests/s**, with **35.27%** graphics-engine activity over 87 seconds and **331.9 ms** of command-thread CPU time per flip request. Later Right Stick, Left Stick, and Start probes during the twilight sequence and scene transitions did not establish camera or movement control.

## November 28, 2026 · Vulkan guest memory on the RX 6800

**Run by** [@gmankab](https://github.com/gmankab)

Disroot Kyty `968870c8` can allocate guest memory in Vulkan DMA-BUF chunks and import eligible buffers into the renderer. I tested the 4 GiB cap and the 0 MiB cap for chunked memfd fallback. On the first validation run, the alias checks passed, then the test aborted at shutdown because its static subsystem owner destroyed the allocation device after the validation layer had released its dispatch state. Moving ownership into the test program's main scope fixed the abort.

REA captured successful full virtual-memory suite runs with legacy backing, DMA-BUF backing, and chunked memfd backing under Khronos Vulkan validation in Fedora Linux 45 Toolbox. The RX 6800's DMA-BUF run passed the fixture for aliased reads and writes across chunk boundaries. The separate Vulkan fixture also passed for a refused host-pointer import and detached readbacks. These tests cover backing and fallback behavior. [DMA-BUF allocator](https://git.disroot.org/flo/kyty/commit/374f99fd0c4ffd23bf032534a9cc336b1e173845) · [Renderer import](https://git.disroot.org/flo/kyty/commit/fe9b11f3602f3ea6f424d1ee9f6d4342141166ab) · [Read fences](https://git.disroot.org/flo/kyty/commit/968870c8de73a4817b8d6bf810d47c5291e9f499)

**Title run** Kyty on host RADV 26.2.6 imported all four 1 GiB DMA-BUF chunks into the renderer. The log recorded **62 successful buffer promotions and two refused attempts** through the first 64 numbered entries. Later successful promotions are sampled in the log. After the profiling interval, VideoOut recorded **139 presentation events in 70 seconds (1.99/s)** during the opening cinematic, with **13.40%** graphics activity over 67 seconds and **85.1%** use of one CPU core by the command thread. It averaged **10 buffer readbacks per 1,000 draws and dispatches**. The following 35 seconds recorded **66 presentation events (1.89/s)**. The previous copy-queue comparison used different scene windows, so a frame-rate gain remains unverified.

**Profile** REA summarized a **24.1-second** Tracy capture from the beginning of the 3D cinematic. Zero-copy read fences recorded **8,752** waits and **9.54 seconds of summed thread time**. The two busiest threads spent **2.19** and **1.31 seconds** there. Threads can wait concurrently. The indirect-dispatch handler used **5.31 seconds** of exclusive instrumented time across the trace. The profile covers a cinematic and the start of buffer promotion.

**Follow-up fixture** The local candidate keeps neighboring pages watched when speculative write-window widening would wait for an outstanding GPU read. The exact write fault retains its wait. REA captured the passing Vulkan fixture and the failure of its speculative-window check with the original behavior selected. REA also captured the following alternating title test on host RADV 26.2.6. Each row uses complete five-second intervals in the opening 3D cinematic, after a settling interval. Presentations are completed VideoOut events. The GPU columns are per-process AMDGPU counters.

| Speculative write-window wait | Window | Presentations / s | Zero-copy read waits per 1,000 draws and dispatches | Graphics busy | Compute busy |
|---|---:|---:|---:|---:|---:|
| Original | 25.03 s | 3.64 | 126.47 | 16.46% | 0.24% |
| Skipped | 25.02 s | 3.56 | 107.46 | 18.16% | 0.21% |
| Original | 25.02 s | 3.28 | 113.71 | 17.03% | 0.20% |
| Skipped | 20.06 s | 3.29 | 109.04 | 17.11% | 0.19% |

The skipped intervals had fewer read waits per draw and dispatch. The scene changed throughout the comparison, and the presentation rates showed no repeatable gain. The run restored the original setting after measurement. Player control remains unverified.

## November 28, 2026 · Disroot Kyty writer-thread fence waits

**Run by** [@gmankab](https://github.com/gmankab)

I built Disroot Kyty `ac1d9e55` with our Intel CLZERO changes and buffer import fallback as local revision `dbf3c1af`. REA captured passing memory-tracker and DMA-BUF memory suites. The refused-import fixture passed with Khronos validation on Toolbox RADV 26.2.7 and the fixture's coarse-bounds opt-in. RADV reported four-byte robust bounds in the strict fixture. The opt-in run logged zero Vulkan validation errors. [Memory-protection change](https://git.disroot.org/flo/kyty/commit/8128b52c50751bd51ef5c0fdc67462ffc7299289) · [Writer-thread wait change](https://git.disroot.org/flo/kyty/commit/ac1d9e55dd0a4ac826c1a4598a97eb78dfa9c0aa)

The warm 1080p title run used host RADV 26.2.6 with DMA-BUF backing on and the default zero-copy policy. REA counted **193 completed presentations in 50.02 s (3.86/s)** across two adjacent 25-second windows in the opening 3D cinematic. The windows recorded **3.44** and **4.28** presentations per second. The process averaged **19.33%** graphics-engine activity and **0.24%** compute-engine activity. Kyty's command thread used **40.56 CPU seconds** in the interval, and its buffer cache recorded **10.58 readbacks per 1,000 draws and dispatches**.

Guest page protection calls covered about **836 MB/s** in this interval. The earlier build's four alternating cinematic intervals covered **6,324 to 10,715 MB/s**. The cinematic progressed between the samples, so the difference describes the measured intervals. Player control and 30 FPS gameplay remain unverified.

**Follow-up profile** REA summarized a separate **24.1-second** Tracy capture. It recorded **24,639** zero-copy read-wait zones with **20.07 seconds** of summed exclusive time across threads. The two busiest threads spent **4.08** and **3.82 seconds** in these waits. The indirect-dispatch handler used **4.35 seconds** of exclusive instrumented time. The profile was collected after the presentation-rate samples.

**Later 3D sequence** The same run reached its 29-minute cap after rendering the opening credits and later exterior scenes. A late 29.99-second window recorded **106 completed presentations (3.53/s)**, **18.00%** graphics-engine activity, and **0.16%** compute-engine activity. The camera checks did not establish player control, and the imported story file showed reads without a subsequent write.

**Indirect draw-count candidate** Molier's patch from Disroot passed four focused host tests and the PM4 multi-draw packet fixture. The GPU exposes Vulkan `drawIndirectCount`. The broader rasterization fixture stopped with a missing guest GPU context and validation faults on both candidate and preceding build. The native compute fixture's Vulkan teardown leak also reproduced on both builds. The performance candidate has a local switch and packet counter for the title comparison. [Disroot indirect-count change](https://git.disroot.org/flo/kyty/commit/f85f0e82f758e6b135609ff983d5124365e355cc)

**Counted-draw title run** I enabled Vulkan indirect draw counts in local revision `a3e36c7f` and kept the same warm 1080p setup. REA counted **162 completed presentations in 49.99 seconds (3.24/s)** in the first 3D sequence. Graphics-engine activity averaged **19.28%**, and the command thread used **39.37 CPU seconds**. The instrumented Vulkan counted-draw path submitted **zero packets** in the interval, so the planned A/B had no workload to compare there. A separate 24.1-second REA/Tracy profile contained **31,521 indirect draw parser calls** and **zero multi-draw packet calls**. That sample also recorded **35,940 indirect compute-dispatch calls**.

## November 28, 2026 · Disroot Kyty Vulkan recorder

**Run by** [@gmankab](https://github.com/gmankab)

Disroot Kyty `3ae51388` includes a separate Vulkan command-recording thread. I built it as local revision `24abcdb4` with the Intel loader changes. The recorder and memory tracker suites passed. The full virtual-memory suite initially found that the parallel instruction scanner did not aggregate its CLZERO counts. After adding that aggregation, the suite passed on the RX 6800 with the DMA-BUF fixture and Khronos Vulkan validation, with zero VUID errors. [Disroot revision](https://git.disroot.org/flo/kyty/commit/3ae5138853513d126e8d5b8b2b8bea3076e102da) · [Recorder change](https://git.disroot.org/flo/kyty/commit/c0795c55e30e2054f0d397f0d65bbab48cbff36f)

The warm title run reached its first 3D sequence after about 7 minutes 20 seconds. It then exited at **468 seconds** during the first recorder-off interval. REA decoded the fault as a 16-bit guest memory read through an indexed pointer in the main module. The kernel logged no GPU reset. The timing establishes when the fault occurred, and the recorder comparison remains incomplete.

### Recorder-only comparison

**Run by** [@gmankab](https://github.com/gmankab)

I backported the recorder toggle, counters, and draining changes to the previously tested `dbf3c1af` base as local revision `0531ec55`. REA captured a passing recorder suite and four alternating windows in the same warm 1080p title run on Fedora Linux 45, RX 6800, and host RADV 26.2.6. The graphics command thread was measured separately from the recorder. I paused the other build before 3D and used complete five-second bins after each toggle settled. [Recorder toggle and counters](https://git.disroot.org/flo/kyty/commit/4ef7f7b5) · [Draining change](https://git.disroot.org/flo/kyty/commit/89638ad1)

| Recorder | Window | Main output presents / s | RX 6800 graphics busy | Command-thread CPU µs / draw or dispatch |
|---|---:|---:|---:|---:|
| Off | 29.99 s | 1.73 | 18.21% | 164.75 |
| On | 25.02 s | 2.60 | 21.32% | 152.54 |
| Off | 29.98 s | 1.90 | 21.12% | 159.30 |
| On | 25.01 s | 1.56 | 26.98% | 146.59 |

The recorder reduced command-thread CPU time per draw or dispatch by about 8% in both pairs and used another 38–41 µs per operation on its own thread. The title progressed through different cinematic shots, and completed presentation rates showed no repeatable gain. The four intervals had zero new graphics or compute pipelines in the captured counters. Player control remains unverified. [Per-window data](../../data/benchmarks/gmankab/2026-11-28-kyty-recorder-backport.csv)

The backport continued with the recorder off and reached the opening credits. Paired camera and movement inputs did not establish player control. It exited after **12 minutes 24 seconds**, at the same guest instruction as the newer build. REA reconstructed the packed index `0xffffffff` from both fault contexts. The code used the hash lookup's miss value as a slot index and attempted an out-of-range read. The reason the expected entry was absent remains unresolved. The kernel logged no GPU reset during the run.

## PS5 AnyPS5 startup cache

**Run by** [@gmankab](https://github.com/gmankab)

With the nested reservation patch applied, the cold run recorded **104 presents over 13.09 seconds** in its saved trace and then continued shader preparation until the 125-second cap. The shader-cache replay recorded **270 presents over 10.03 seconds** and **3 more over 31.82 seconds** before stalling. The median present interval in its first window was **33.37 ms**. Sampled screenshots in both runs were black. By the 205-second cap, the replay registered 7,575 shader-cache hits and 2,800 misses. Its 139.9-second AMDGPU process sample added 11.7 ms of graphics-engine time and zero compute-engine time. [Linux reservation test](https://github.com/boykopovar/AnyPS5/pull/27563#issuecomment-7220622753) · [Related shader-preparation issue](https://github.com/boykopovar/AnyPS5/issues/27705)

Adding the AnyPS5 REWIND preparation patch to the same cached run produced **5,667 presents over 190.21 seconds**, or **29.79 presents/s** across the recorded intervals, before the 205-second test cap. Every sampled screenshot was black. AMDGPU attributed **1.62%** graphics-engine activity to AnyPS5 over a **164-second** process sample. Its focused shader-preparation CTest passed with Khronos validation inserted and zero VUID errors. Both title runs used host RADV 26.2.6. The focused test used Toolbox RADV 26.2.7. [AnyPS5 REWIND patch and Linux result](https://github.com/boykopovar/AnyPS5/pull/27821#issuecomment-7221266032)

## November 28, 2026 · AnyPS5 Intel conversion and packed-color retest

**Run by** [@gmankab](https://github.com/gmankab)

**Relinker** REA captured the stock AnyPS5 relinker rejecting an AMD-only instruction during Intel conversion of `PPSA38500`, on upstream `a2d8d409`. Applying the current short-instruction fix to that revision passed `amd64_only_converter` and `to_intel_linux_stubs`. It also completed the title dry run, produced a Linux ELF and both bundled modules in a private conversion, and passed the dynamic-loader check with the local startup runtime. [Upstream base](https://github.com/boykopovar/AnyPS5/commit/a2d8d409dba36d4cc0d531d49b828f808bc93a16) · [Intel conversion PR](https://github.com/boykopovar/AnyPS5/pull/27560)

**Packed color** Both synthetic tests rejected color format 19, number type 0, component swap 1 on the Linux startup stack with its existing decoder. With PR `fc78d6bb` applied to the same stack, both tests and `agc_driver_graphics` passed on the RX 6800. Both packed tests loaded Khronos validation 1.4.350 on Toolbox RADV 26.2.7. The logs contained zero VUID entries. [Upstream test report](https://github.com/boykopovar/AnyPS5/pull/27966#issuecomment-7230420443)

**Updated AnyPS5 PR** On upstream `750a5234` with the Linux startup patches and packed-color PR revision `e2e97886`, the three packed-color Vulkan fixtures passed with the validation layer loaded and zero VUID errors. The multisample fixture passed at 2×, 4×, and 8×. The prepared-shaders fixture also passed. The broader graphics suite passed when rerun with the candidate libraries pinned after an initial missing `vkCreateRenderPass` error. [Updated upstream test report](https://github.com/boykopovar/AnyPS5/pull/27966#issuecomment-7230420443)

**Warm-cache title** The newly relinked executable ran with the existing Linux startup patches and the packed-color candidate on host RADV 26.2.6. It recorded **6,756 presents in 230.23 seconds (29.35/s)** before the 240-second cap. All **17** captured images matched the same black frame. AMDGPU recorded **1.50%** graphics-engine activity and no measurable compute-engine activity over **169.8 seconds**. The log showed **19** deferred geometry-stage preparations and no packed-color swap-1 refusal. The run leaves the cause of the black output unresolved.

**Updated warm-cache title** AnyPS5 local revision `fba1ef8387` relinked the main executable and both bundled modules and passed a Linux dynamic-loader check. In the 240-second warm-cache run, REA counted **6,438 presentations in 220.28 recorded seconds (29.23/s)**. All **17** window captures matched the previous black image byte for byte. The RX 6800's process graphics engine averaged **1.52%** across a contiguous **104.0-second** sample. The log recorded five deferred geometry preparations and no packed 4_4_4_4 swap-1 refusal. These measurements cover startup before any rendered gameplay.

## November 28, 2026 · AnyKyty Intel relinker

**Run by** [@gmankab](https://github.com/gmankab)

AnyKyty `eca3fe54` configured and built in Fedora Linux 45 Toolbox. REA captured passes for `amd64_only_converter` and `to_intel_linux_stubs`. The fork's stock relinker with `--to-intel --no-patches` produced a Linux executable and both bundled modules for the local PS5 title. The release tool printed a dirty suffix because three nested development submodules had checked out different commits after a recursive fetch stopped. Their pins were corrected and the source tree was clean before the runtime build. This result covers conversion. [AnyKyty source](https://github.com/simnJS/AnyPS5/commit/eca3fe546dd8381bf615d0f634ac62d7af76568d) · [Short CLZERO fix in the fork](https://github.com/simnJS/AnyPS5/commit/175a273a79cbce2da940ce3ab1ee9c7d72115ce5)

**Cold title run** After the Vulkan backend and the 58 required runtime libraries built, REA verified the Linux loader resolved them. On host RADV 26.2.6 the run rendered the Continue prompt in the upper-left quarter of the Weston output. The first **3,202 completed flips** spanned **112.13 seconds**, or **28.55 intervals/s**, with a **33.37 ms** median interval. AMDGPU attributed **1.71%** graphics-engine activity and **0%** compute-engine activity to the title over an 85-second startup sample. Pad telemetry confirmed the Continue input. The process then exited on a guest `CLWB` that the Xeon E5-2670 v3 does not support. An AnyPS5 issue identified this instruction in the title's error path on a separate Windows run. [Instruction investigation](https://github.com/boykopovar/AnyPS5/issues/27914) · [Existing relinker PR](https://github.com/boykopovar/AnyPS5/pull/26606)

**Cache-flush PR retest** I applied the four relinker commits through `0dd34404` to the same AnyKyty revision. REA captured passes for `amd64_only_converter`, `to_rosetta`, and `to_intel_linux_stubs`. The title conversion replaced the four-byte `CLWB` at the recorded fault site. With the same runtime libraries and a cold cache, the game advanced through the setup screens and entered story loading after the legal notices. [Linux retest on the upstream PR](https://github.com/boykopovar/AnyPS5/pull/26606#issuecomment-7232011970)

**Shader preparation** The cold run recorded **14,474 completed flips** through 492.89 seconds and ended during shader compilation at about 11 minutes. The last captured stall summary counted 311 reports totaling 114.43 seconds. REA found zero fatal SIGILL entries in the patched run. An eight-second thread sample used **459.4% of one CPU core** across the title, including **99.3%** on the GPU command thread.

**Warm loading** The retry restored **689 manifest shader modules** and **369 cached compute pipelines**. The illustrated loading screen completed **2,067 flips in 70 seconds (29.53/s)**, with **2.12%** graphics-engine activity and **0%** compute-engine activity over the matching hardware sample. Completed flips stopped at about 188 seconds. An early sample counted **68 synchronous compute shader stalls totaling 266.92 seconds**, for shaders that write images or buffers. Over 99 contiguous seconds during those stalls, the process averaged **1.50%** graphics-engine activity. The warm run was still preparing new shader variants at this checkpoint.

**First outdoor frame** The warm run completed one outdoor presentation after a **964.28-second** gap. Within seconds the kernel logged an RX 6800 graphics-ring timeout and a successful ring reset. AMDGPU attributed close to 100% graphics-engine activity to the title in the final two monitored seconds around the timeout. REA decoded the title's final fault as a direct write to address zero. The process exited and Niri stayed running with its original PID. This run did not establish controllable gameplay or a sustained 3D frame rate. [Linux report on the AnyKyty issue](https://github.com/simnJS/AnyPS5/issues/314#issuecomment-7232431320)

## November 28, 2026 · VIcky Blue flip-wait port

**Run by** [@gmankab](https://github.com/gmankab)

I applied VIcky Blue's timed flip-wait behavior to the local Disroot baseline as `7babb002`. REA captured passing PM4 flip and flip-rate fixtures under an isolated Weston session. The 1080p title smoke test reached illustrated loading and the opening 3D cinematic on host RADV 26.2.6. The test enabled processing of queued GPU commands during pending flips. [Source PR](https://github.com/wololo-wololo/VIcky-Blue/pull/42)

REA counted **81 completed presentations over 39.94 seconds (2.03/s)** during the opening 3D sequence. The title averaged **20.58%** graphics-engine activity and **0.12%** compute-engine activity, with **149.15 µs** of command-thread CPU time per draw or dispatch. Separate builds ran during this smoke test. The result establishes that the port reached the cinematic and leaves its startup-speed effect unmeasured.

The title continued through the exterior opening credits. Camera and movement probes did not establish player control. A separate 24.1-second Tracy capture recorded **17,491 zero-copy read waits** and **20.06 seconds of summed exclusive thread time** in those wait zones. These waits can overlap across threads. The indirect-dispatch handler accounted for **3.08 seconds** of exclusive time in the same capture.

## November 28, 2026 · Later PS5 branch builds

**Run by** [@gmankab](https://github.com/gmankab)

- Disroot Kyty `af5e20d8` built as `1455a3d0` with the existing Intel loader and guest-memory compatibility commits. REA captured passes for the readback-wait, readback-batch, materialization-memo, memory-tracker, and recorder fixtures. [Disroot source](https://git.disroot.org/flo/kyty/commit/af5e20d8ecc8b67544d98dc31a41ceb94d97691f)
- AnyKyty's `arch/30fps` revision `3636f61ddf70` built with the same cache-flush PR. All three focused relinker tests passed. REA captured a successful conversion of the title and both bundled modules, followed by a Linux loader check with the 58 launch libraries. These checks cover build and launch preparation. [Performance branch](https://github.com/simnJS/AnyPS5/commit/3636f61ddf70) · [Cache-flush PR](https://github.com/boykopovar/AnyPS5/pull/26606)

**Disroot title run** Local revision `1455a3d0` reached the first exterior cinematic with shader compilation still in progress. The comparison of the writer-wait setting stopped before all four measurement windows completed. After **681 seconds**, the guest read through an invalid resource index. REA compared this run with the earlier recorder runs at **468** and **744 seconds** and reconstructed the same missing-entry value in all three. The reason for the missing resource is still unknown. The kernel logged no GPU reset in the latest run. [Disroot readback change](https://git.disroot.org/flo/kyty/commit/7db618b9)

**AnyKyty title startup** The same performance-branch build stopped during Vulkan startup on host RADV 26.2.6 in both a launch using the preceding build's cache and a cold-cache launch. REA matched both stacks to the driver reporting a failed Vulkan allocation during guest host-memory import. Both crashes occurred before the Continue screen. [Performance branch](https://github.com/simnJS/AnyPS5/commit/3636f61ddf70) · [Host-memory import change](https://github.com/simnJS/AnyPS5/commit/20249ba6caa)

**AnyKyty callback workaround** I rebuilt with a local switch to disable Vulkan memory-report callbacks. A cold run passed Continue and showed the illustrated loading screens. RADV still refused host-memory imports, and the existing sparse mirror supplied those reads. In a 45-second loading window, REA counted **1,003 completed presentations (22.29/s)**, **61.11 ms** p95 between completions, and **3.84%** graphics-engine activity for the title. In a later 125-second shader compilation window it measured **2.17%** graphics-engine activity and **3.31 CPU core equivalents**. This run used an empty shader cache. [Linux native test report](https://github.com/simnJS/AnyPS5/issues/168#issuecomment-7236154260)

The cold run reached its **25-minute** limit during first-scene pipeline compilation. Its last completed flip was at about **306 seconds**. The renderer reported **676** pipeline wait events totaling **822.64 seconds** and saved **226** Vulkan compute pipeline binaries. Across a continuous 549-second sample late in the run, the title averaged **0.44%** graphics-engine activity and **2.10 CPU core equivalents**. The kernel logged no graphics ring timeout.

The same build's warm run seeded the cache from that cold run. Its startup manifest reported **509** cached compute pipelines and **13** newly created ones before releasing the guest after **22.93 seconds** of preparation. In a separate 45-second illustrated loading window it completed **1,063 presentations (23.62/s)** with **62.27 ms** p95 and **6.58%** graphics-engine activity. These windows measure loading throughout different portions of the animation.

The warm run stopped after **299 seconds**. REA verified that the reported failure came from a guest software assertion. The kernel logged no graphics-ring timeout or reset in this run.

## November 28, 2026 · VIcky Blue shader compile-time branch

**Run by** [@gmankab](https://github.com/gmankab)

I built PR #52 with the Intel loader changes and started a 1280×720 Weston run with an empty pipeline cache and the existing setup save. REA captured passing resource-materialization and CPU compatibility fixtures. The overlay showed **1.8 FPS** during the animated intro and **30.0 FPS** during an illustrated loading sample. The title used **0.55%** of the graphics engine and **1.12 CPU core equivalents** in a 159-second intro window, then **4.80%** of the graphics engine and **4.45 core equivalents** in a 29-second loading window.

The run exited after **486 seconds** when a fresh Vulkan descriptor pool failed to allocate a set during first-scene preparation. REA counted **27** unoptimized compute pipeline creations logged over the branch's slow threshold, with a maximum of **2.407 seconds**. The failure corresponds to the fresh-pool allocation path addressed by Disroot's dedicated descriptor pool change. The exact requested descriptor counts were unavailable in this run. [VIcky Blue shader PR](https://github.com/wololo-wololo/VIcky-Blue/pull/52) · [Disroot descriptor pool change](https://git.disroot.org/flo/kyty/commit/b02adf9e4735a9b56c2234c5620d983b9534e868)

**Dedicated-pool retest** I applied the Disroot fix with its original author and adapted its test fixture to VIcky Blue's semaphore API. On the RX 6800 with Toolbox RADV 26.2.7 and Vulkan validation, REA captured successful allocation and recycling of sets with **9,000 storage buffers and 24,252 storage images** each. The title retry on host RADV 26.2.6 went past the earlier stop and allocated multiple layouts with **65,546–65,547 storage buffers** each using dedicated pools. [Disroot descriptor pool change](https://git.disroot.org/flo/kyty/commit/b02adf9e4735a9b56c2234c5620d983b9534e868)

During the intro in that retry, REA counted **40 presenter calls in 23.58 seconds**, with a **600.01 ms** median interval, while the flip loop ran every **16.67 ms**. During first-scene compilation the guest stopped at **421 seconds**. REA matched the stop instruction and its caller to the path in the earlier AnyKyty run that ended around a GPU reset. The caller checks forward progress with one-second sleeps and a 30-count limit. The kernel logged no GPU reset in the VIcky Blue retry. [Earlier AnyKyty report](https://github.com/simnJS/AnyPS5/issues/314#issuecomment-7232431320)

## November 28, 2026 · Frozzy AnyVI outdoor cinematic

**Run by** [@gmankab](https://github.com/gmankab)

I built Frozzy's AnyVI branch with the same seven Intel loader and Vulkan guest-memory compatibility commits used in the Disroot comparison. The Linux native build ran on the RX 6800 with RADV 26.2.6, the 1080p guest option, and a 1280×720 Weston window. It loaded a 344.27 MB driver cache from a different revision and rebuilt the incompatible shader store. REA captured passing tests for guest time during compilation and the pipeline binary store. The branch defaults to its regular Vulkan pipeline cache on RADV. [AnyVI revision](https://git.disroot.org/frozzy/kyty/commit/eed0c578eab39717030a9890cc0d3b0220b6e8c5) · [RADV cache selection](https://git.disroot.org/frozzy/kyty/commit/e197a16dc6404167202ae71be5591f68f77ae4d1)

During first-scene preparation, the title averaged **0.04% graphics-engine activity** and **8.76 CPU core equivalents** from 600 to 720 seconds after launch. In a 24-second REA capture within that period, 18 compute pipeline creations each spent **5.06–7.89 seconds** in the Vulkan driver, overlapping across workers. The first outdoor image was visible by **15 minutes 17 seconds**, followed by multiple rendered frames and a cutscene. At 915–935 seconds the title used **5.99%** of the graphics engine and **0.83%** of the compute engine. The outdoor cutscene overlay briefly showed **3.2 FPS** with no active pipeline builds. Nearby REA captures found substantial buffer-readback time on caller threads and continued shader compilation. Camera and pause inputs did not establish player control in that sequence.

The run reached its **22-minute 30-second** time limit during a later part of the outdoor cutscene. The kernel logged no graphics reset or timeout during this run. I kept its driver and shader caches for a repeat on the same executable.

**Warm-cache repeat** The launch used the first run's cache and save. It loaded **491.69 MB** of Vulkan cache data, **2,236 shader modules**, and **1,954 pipeline records**. The first outdoor screenshot was available by **8 minutes 1 second**, versus **15 minutes 17 seconds** in the first run. The Continue prompt was also advanced about 74 seconds sooner. The illustrated loading screen completed **1,800 main-output flips in 60 seconds** with **33.58 ms** p95 spacing.

The 20-second exterior cutscene sample at 505–525 seconds produced **66 main-output flips** at **3.27/s**, with **363.09 ms** p95 spacing. The title averaged **18.00%** graphics-engine activity, **2.73%** compute-engine activity, and **4.22 CPU core equivalents**. In a nearby 24-second REA capture, **11,373** buffer readbacks took **10.35 seconds** of summed caller-thread time. The corresponding profiling zone on the emulated GPU command thread totaled **3.17 seconds**. These times can overlap across threads. Flip timing measures guest main-output completions, so emulator overlay refreshes are excluded.

The warm run went on to the opening credits. The 35-second credits sample at 690–725 seconds produced **38 main-output flips** at **1.07/s**, with **1,123.85 ms** p95 spacing. The title averaged **44.03%** graphics-engine activity, **1.69%** compute-engine activity, and **3.15 CPU core equivalents**. Summed graphics-engine busy time over that window was **406 ms per completed flip**. A nearby REA capture recorded **6.26 seconds** of summed readback caller time, **4.23 seconds** in command-scheduler waits, and about **0.79 seconds** across the two graphics pipeline creations.

The warm run reached its **25-minute** limit during the opening credits. Some later views showed missing geometry and a large green rectangle. REA counted **74 main-output flips in 50 seconds** at 1280–1330 seconds. Camera and movement tests in that sequence gave no reproducible player control, and the story save matched its prelaunch digest. The kernel logged no graphics ring timeout or reset.

## November 28, 2026 · PaN1C27 Kyty indirect-dispatch comparison

**Run by** [@gmankab](https://github.com/gmankab)

I built the fork with the tested Intel loader changes and Disroot's 1080p option. The CPU-extension fixture passed after adding the existing CLZERO trap fallback. The native-indirect fixture passed on the RX 6800 with the test harness opted into RADV's four-byte robust bounds. REA captured both results. The 1080p title run initialized Vulkan and stopped **42 seconds** after launch, before Continue. The log showed missing AGC, VideoOut, and AMPR imports, followed by a guest write fault. The unresolved AMPR import was the operation that backs PRT reservations. [Fork revision](https://github.com/PaN1C27/KytyPS5/commit/d36709870f1d8b85fd8e116ee2b1aab7b7108a21) · [AMPR implementation in Kyty](https://github.com/KytyPS5/KytyPS5/commit/c9122a1cfdb8326dd2858ceca27e9a84550744ad)

## Title runs

**Run by** [@gmankab](https://github.com/gmankab)

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
| `cool_fox` `f4b48ad`, RX 6800/Wine/Wayland | Loading and setup at about **30 FPS**. Breakpoint on the legal terms screen. | Standalone Lavapipe run stopped before any capture. Details in [projects](../projects.md). |
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

## Linux build checks for Foxhound and Belobug AMD

**Run by** [@gmankab](https://github.com/gmankab)

Both Fedora Linux 45 release builds failed in `victory-loader` on Windows API references. [Published benchmarks from the project authors](external.md#windows-fork-source-comparison)

## Cool Fox under the CGBJ reupload name

**Run by** [@gmankab](https://github.com/gmankab)

The public `cgbj-reupload` resolves to the original Cool Fox `f4b48ad` commit. Both runs used the locally built Windows-target binary, the `pareto-residency` Series S profile, Wine/Wayland, RADV on the RX 6800, and a read-only title directory. The rerun granted Wine access to PulseAudio. [Source check](../projects.md#cgbj-source-checks)

| Setup | Result |
|---|---|
| Fresh data and scripted input, 155 s | Rendered the intro and advanced to Legal Notices. The last capture, frame 3,900, still showed Legal Notices. Median **30.0 FPS** across 27 startup and setup samples. |
| Copied checkpoint profile and adjusted input, 155 s | Advanced beyond Legal Notices to the illustrated loading screen, which remained visible in the last capture at frame 3,600. Median **30.0 FPS** across 26 startup and loading samples. |

Both runs reached their time caps and the kernel logged no GPU reset. Player control and an in-world frame rate remain unverified for this commit on the RX 6800. [Run index](../../data/benchmarks/gmankab/runs.csv)

## Native compute shader validation

**Run by** [@gmankab](https://github.com/gmankab)

A 285-second Lavapipe run with the default watchdog provided 1,296 shader containers. A lane-mask candidate passed its RX 6800 fixture and left every result in this set unchanged. Defaulting reads of written but uninitialized general-purpose scalar registers to zero cleared three compute translation failures. Those shaders then reached an unsupported explicit-mip gather, which the second port implemented. Together the changes increased validated compute programs from **650 to 657** and preserved all **579** previously validated vertex and pixel programs. [Register rule](https://github.com/Marvisak/VIctory/pull/1261) · [Gather implementation](https://github.com/Marvisak/VIctory/commit/0b35b7fe) · [Native ports](https://github.com/spidercraft/VIctory/pull/28)

The exact Spidercraft contribution branch passed the register test and created all three targeted culling pipelines on the RX 6800 with native vkd3d-proton and RADV. A 120-second title smoke test on the integrated native build reached frame 2,048. Two captures showed the account picker prompts. The other 32 were black. The median of 20 startup FPS samples was 29.55, and the kernel logged no GPU reset during the test. Player control in the world remains unverified for this variant. [Run index](../../data/benchmarks/gmankab/runs.csv)

A longer checkpoint-profile run used the same native binary with adjusted setup input. It reached the illustrated loading screen and then began heavier 3D submissions, with one sample at **5.6 FPS** and 260 draws per frame. The whole card was **3% busy at the median and 99% at the peak** across 89 readings. VRAM use peaked at 7,614 MiB. After 224 seconds the run exited following a graphics-ring timeout and an AMDGPU reset. Niri aborted and systemd restarted Niri and the terminal recovery service. The last captured frame still showed loading. [Run index](../../data/benchmarks/gmankab/runs.csv)

The installed Vulkan layer reported an embedded-sampler count that matches a known validation-layer false positive. It also reported eight storage-buffer descriptor alignment warnings during heavier submissions. The timing establishes that those warnings preceded the timeout. The cause of the reset remains unresolved. [Khronos sampler-count fix](https://github.com/KhronosGroup/Vulkan-ValidationLayers/pull/13673)

The run also logged Vulkan's overlapping-occlusion-query warning. vkd3d-proton tracks the same warning in its virtual-query test. On Lavapipe with no `/dev/dri`, that test passed its readbacks both as shipped and with the warning unmuted. The unmuted build logged the same query index and pool overlap as the title run, while all 28 checks passed. The descriptor fixture separately created aligned and odd-offset typed buffer views on Lavapipe's descriptor-buffer path. The packed heap path used on RADV remains unverified by that fixture. [vkd3d-proton virtual-query issue](https://github.com/HansKristian-Work/vkd3d-proton/issues/2381)

## Native occlusion query measurements

**Run by** [@gmankab](https://github.com/gmankab)

A 4×4 draw fixture reproduced the title run’s overlapping-query warning on Lavapipe. The fixture used two title heaps and crossing counted and binary queries. Splitting the host measurement into intervals reduced the warnings from **three to zero** and preserved counted results **20 and 12** and binary results **1 and 1**. The native integration passed 63 regular GPU tests. The exact Spidercraft PR head passed the draw fixture and 44 regular GPU tests. Both ran with Vulkan validation and `/dev/dri` hidden. The native title’s reset and open-world frame rate still need separate verification. [Upstream patch and test](https://github.com/spidercraft/VIctory/pull/29) · [Test index](../../data/benchmarks/gmankab/test-results.csv)

## Linux native descriptor-buffer checks

**Run by** [@gmankab](https://github.com/gmankab)

On the RX 6800, vkd3d-proton selected Vulkan descriptor buffers with `VKD3D_CONFIG=vk_debug,no_descriptor_heap`. The Linux native occlusion PR’s draw fixture returned the same counted and binary results with zero overlap warnings. The integrated culling shader probe validated all three programs and created all three compute pipelines. The first analysis of the odd-offset R16 fixture checked the descriptor-heap warning ID `12351`. Its retained log records the equivalent descriptor-buffer warning `12266` for both views. [Occlusion hardware check](https://github.com/spidercraft/VIctory/pull/29) · [Culling patch](https://github.com/spidercraft/VIctory/pull/28)

The first checkpoint run with the combined Linux native build exited with status 5 after 90 seconds. It loaded its archives through the DirectStorage implementation and logged a 41-second semaphore wait. The title executed a deliberate null write on its heartbeat timeout path before Vulkan initialization or the first presented frame. The USB drive was under heavy contention, with a system-wide full I/O pressure average of 67% over one sampled 10-second window. The kernel logged no AMDGPU fault or reset during this run. It produced no gameplay timing. [Tested build](https://github.com/gmankab/victory/commit/40552f6e9d583da9f098c628bb5433d000cdc3f9) · [Run index](../../data/benchmarks/gmankab/runs.csv)

A retry with best-effort I/O priority 0 reached Vulkan and presented its first frame after 28 seconds. The live Linux native process loaded RADV and vkd3d-proton. All 25 sampled frames were black, and the log reported up to 583 pipelines pending. The title hit the same watchdog after 165 seconds and Victory exited with status 5. Vulkan validation logged no VUIDs, and the kernel logged no AMDGPU fault or reset. The retry saved pipeline cache data for a follow-up run. [Marvisak watchdog analysis](https://github.com/Marvisak/VIctory/pull/454) · [Run index](../../data/benchmarks/gmankab/runs.csv)

The warm-cache run reused the retry's Victory, Mesa, and vkd3d-proton caches. vkd3d-proton mapped its saved cache, the first reported shader-cache sample showed 79 hits and zero misses, and the first completed pipeline batch loaded ten host pipelines from the library. The first presentation took 95 seconds under USB I/O contention. All 10 sampled frames were black. The title reached the same watchdog with 589 host pipelines pending and exited after 215 seconds. The kernel logged no AMDGPU fault or reset. [Run index](../../data/benchmarks/gmankab/runs.csv)

With the same binary, cache seed, and I/O priority, moving writable run files to the internal NVMe reduced the observed time to first presentation to **2.1 seconds**. The read-only game directory stayed on USB. Victory reached the illustrated loading screen and began heavier 3D submissions before the 260-second cap. The last capture, frame 5700, still showed the loading artwork. The kernel logged no AMDGPU fault or reset. [Run index](../../data/benchmarks/gmankab/runs.csv)

The heavier phase reported **0.9–3.8 FPS**. In the slowest sample, Victory spent **851 ms preparing** a frame with 2,160 draws, including 272 ms refreshing 244 MB of shadow buffers. The same sample reported 94 ms of GPU time and 119 ms of copy-back. Across the run, 130 whole-card samples showed 2% median GPU busy and a 17% sampled peak. Vulkan reported `VUID-VkDescriptorGetInfoEXT-type-12266` for a storage-buffer descriptor address that was not four-byte aligned. [Run index](../../data/benchmarks/gmankab/runs.csv)

An RX 6800 read-back fixture reproduced the same warning using an odd-offset `R16_UINT` SRV and UAV. vkd3d-proton wrote a packed storage-buffer alias at the typed view's two-byte-aligned address. The driver patch writes a null alias for that case and preserves the typed texel descriptor. The validation result changed from **two warnings to zero**. Both builds returned **1235 and 5679** with the surrounding elements unchanged. On the current upstream base, the patched driver also passed the aligned raw descriptor fixture. [Tested driver branch](https://github.com/gmankab/vkd3d-proton/tree/fix/typed-ssbo-alias-alignment)

## Linux native offline pipeline preparation

**Run by** [@gmankab](https://github.com/gmankab)

The Linux native port of Marvisak's prebuilder uses the same creation-stream reader and host pipeline keys as a title run. The contribution branch passed the release build, 15 format tests, and eight pipeline tests. The Lavapipe run used `VKD3D_FEATURE_LEVEL=12_0` so the pipeline-library fixture could create its D3D12 device. The read-only census found 27,160 nameable records and 26,918 distinct keys out of 28,164 records. [Linux native contribution](https://github.com/spidercraft/VIctory/pull/31)

The separate integrated RX 6800 prebuild processed the same pack in **345.4 seconds** with 12 workers. It created **20,830** host pipelines, found **2,976** in the library as the pass progressed, and skipped **4,358** records. Shader translation failures accounted for 3,354 skips. [Marvisak prebuilder](https://github.com/Marvisak/VIctory/commit/9c58a1c35a384552bde9b4c9b169731f282a15a1)

The RX 6800 run with vkd3d-proton `076775d` rejected the library built under `1fa8fc0`. Rebuilding with the Linux native integration and the `076775d` driver with breadcrumbs enabled created **20,848** host pipelines in **272.1 seconds** using 12 workers. A separate offline process reopened the **3.9 MB** library in **7 ms** and reused its entries for **23,806** pack records. Its index covered **342 of 413** pipeline keys used by the earlier title run. The other 71 keys had no exact match in the offline input. [Tested Victory integration](https://github.com/gmankab/victory/commit/c0503c24a65a995c8b8e1c078e5b6992b271e3d3) · [Tested driver](https://github.com/gmankab/vkd3d-proton/tree/test/strict-host-import-typed-alignment)

The first title run with the `1fa8fc0` build and its matching library reached the first presentation after **39.4 seconds**. Victory loaded the 3.9 MB host pipeline library and reached frame **8,192** in the 360-second run. The final capture showed the illustrated loading screen. The 62 frame reports had a 30 FPS median, with at most 11 draws per frame and one pipeline pending. Median sampled GPU activity was 2%, and Vulkan validation and the kernel recorded zero warnings or resets for the run. This establishes cache loading and startup behavior. The heavier 3D phase from the earlier run remained outside the observed workload. [Run index](../../data/benchmarks/gmankab/runs.csv)

A longer run with DirectStorage diagnostics reached heavy 3D submissions after **296 seconds**. Across 50 reporting windows, **99.1%** of occupied storage-worker time was spent in reads, peak queued requests reached **3,329**, and staging waits totaled zero. The two heavy samples reported **1.0 and 0.9 FPS**. The first copied **316 MB in 318 ms per frame** and still had 957 pipelines queued. The final library save counted 364 loads and 52 creations. [Run index](../../data/benchmarks/gmankab/runs.csv)

The longer run logged zero Vulkan VUIDs. AMDGPU reported a graphics-hub page fault and a successful graphics-ring reset. Niri and the Codex recovery service stayed active. The title then hit its watchdog and exited at **336 seconds**. The last captured frame, 7000, still showed the illustrated loading screen. The kernel record identifies the fault as an SQC data read. The originating resource or shader remains unidentified. [Run index](../../data/benchmarks/gmankab/runs.csv)

A local follow-up deferred release of old resources when a directly indexed descriptor slot changes. The Lavapipe fixture held a resource through two completed submissions and released it after the third, with zero Vulkan VUIDs. Its effect on the RX 6800 title fault is unverified. [Upstream lifetime patch](https://github.com/Marvisak/VIctory/pull/609)

## Linux native write-watch bitmap scans

**Run by** [@gmankab](https://github.com/gmankab)

In the heavier RX 6800 loading sample, Victory requested one 174 MiB shadow range 31 times per frame. The range took 93.48 ms in refresh calls. Those calls include tracking, copies, and other shadow work. The Linux native loader patch walks one bitmap word at a time for private memory. Physical aliases keep their group-aware checks. [Loading run](../../data/benchmarks/gmankab/runs.csv) · [Loader PR](https://github.com/spidercraft/VIctory/pull/32)

| 174 MiB private range | Baseline | Candidate |
|---|---:|---:|
| All blocks clean | 11.03 µs | 0.158 µs |
| Four blocks opened and re-armed | 22.32 µs | 11.21 µs |

Each result is the median of seven paired process medians from the integrated release build. The fixture verified the exact dirty block list. The integration passed 44 loader tests, and the contribution head passed 35, including the partial-word boundary regression. The measured timings cover the guard API and its protection calls. Open-world frame timing remains unverified. [Individual measurements](../../data/benchmarks/gmankab/benchmark-logs.csv)

## Linux native sparse guest copies

**Run by** [@gmankab](https://github.com/gmankab)

The heavier loading sample reported 912 MB per frame of guest bytes that could not be read by copy probes. The loader now lets the copy fallback zero a whole known unreadable run after a failed read. Ranges outside the loader's table still use the existing page probes. [Loading run](../../data/benchmarks/gmankab/runs.csv) · [Sparse-copy PR](https://github.com/spidercraft/VIctory/pull/33)

A 174 MiB fixture with 87 MiB mapped and 87 MiB reserved measured **78.95 → 29.20 ms** per copy across seven paired process medians. The contribution head measured **28.37 ms** in its own fixture and passed six regular host tests. The integration passed 13 regular host tests. Both verified the bytes after a hole that ended inside a 64 KiB copy piece. The integration also verified both hole cases with guarded reads enabled. These timings measure stand-alone CPU copies. [Individual measurements](../../data/benchmarks/gmankab/benchmark-logs.csv)

A Linux native build with both changes presented its first frame after **4.21 seconds** on Lavapipe, with hardware render devices hidden. Its five-minute run reached capture **512**, which showed the illustrated loading screen. The 47 reports had a median of **0.4 FPS** and at most 11 draws per frame. Vulkan validation flagged a vertex shader using a subgroup operation that the software adapter did not list for that stage. The run ended at the time cap. [Run index](../../data/benchmarks/gmankab/runs.csv)

## Linux native repeated GPU writes

**Run by** [@gmankab](https://github.com/gmankab)

The heavy RX 6800 loading sample requested 2,241 shadow refreshes per frame. Each declaration of a writable range previously flushed the refresh batch. The patch keeps the first declaration for each exact range and uses the same order for copy-back. A regression across two command lists verified that an unrelated read stayed queued and separate CPU and host writes were both preserved. [Run index](../../data/benchmarks/gmankab/runs.csv) · [Victory PR](https://github.com/spidercraft/VIctory/pull/35)

| Preparation fixture, 48 requests for one 4 MiB read range | Baseline | Candidate |
|---|---:|---:|
| RX 6800, untracked pages | 16.87 ms | 0.33 ms |
| Lavapipe, untracked pages | 17.35 ms | 0.36 ms |
| RX 6800, clean write-watched pages | 80.48 µs | 6.76 µs |
| Lavapipe, clean write-watched pages | 67.24 µs | 6.07 µs |

Each row is the median of 21 paired samples. Preparation copied 192 MiB before the patch and 4 MiB afterward for untracked pages. The clean write-watched case copied zero bytes in either mode. The change passed all 44 regular GPU tests on Lavapipe, and the new regression also passed on the RX 6800. The Lavapipe descriptor-slot validation warning was reproduced on the unmodified base. These are synthetic preparation timings. Open-world frame time remains unmeasured for the patch. [Victory PR](https://github.com/spidercraft/VIctory/pull/35)

The later Linux native GPU snapshot port saves each declared byte on first use and reconstructs the original 64 KiB page when readback spans saved ranges. A 174 MiB CPU fixture measured **440.23 → 90.04 ms** and **4,116 → 156 MiB** copied for 49 growing ranges. Two distant 1 MiB ranges took **19.18 → 0.16 ms** and copied **175 → 2 MiB**. Times are medians of nine warmed trials. The stand-alone contribution passed all 44 regular GPU tests on RX 6800 and Lavapipe. The integration passed all 74 on both backends. The fixtures measure snapshot preparation and writeback correctness. Title frame time remains unmeasured for this patch. [Linux native PR](https://github.com/spidercraft/VIctory/pull/45) · [Marvisak source](https://github.com/Marvisak/VIctory/pull/3318) · [Individual measurements](../../data/benchmarks/gmankab/benchmark-logs.csv)

## Linux native overlapping GPU writebacks

**Run by** [@gmankab](https://github.com/gmankab)

A synthetic 174 MiB shared buffer received 49 growing declarations. The existing copy-back repeatedly visited the same bytes. The patch remembers the portions that have already been compared or written and leaves failed portions available for a later retry. On the RX 6800, the fixture measured the following times after submission preparation. Each figure is the median of two process medians, with seven timed trials per process.

| Declarations | Previous | With overlap tracking |
|---|---:|---:|
| Growing, overlapping ranges | 1,085.1 ms | 45.9 ms |
| Adjacent, disjoint ranges | 49.6 ms | 49.5 ms |

The fixture verified GPU-written bytes, nearby CPU stores, and untouched neighboring bytes. A separate regression kept a newer CPU store through a second overlapping writeback. Another retried a four-byte write after a 64 KiB read crossed an inaccessible page. The integration passed 78 regular GPU tests each on RX 6800 and Lavapipe. These timings measure the fixture's copy-back stage. [Integration](https://github.com/gmankab/victory/tree/test/linux-native-reconcile-writebacks) · [Marvisak source](https://github.com/Marvisak/VIctory/pull/3412) · [Individual measurements](../../data/benchmarks/gmankab/benchmark-logs.csv)

The stand-alone contribution passed 45 regular GPU tests on each backend. The RX 6800 Series S title run ended at its 245-second cap with frame 5,900 captured and a **30.0 FPS** median across 43 loading samples. The control, with the same seed, was stopped at 157 seconds on request. It captured frame 3,000 and logged a **29.0 FPS** median across 23 loading samples. Both runs logged at most 11 draws per frame and 0.0 ms of copy-back in every timed sample. The kernel logged no AMDGPU fault or reset in either interval. This pair covers startup and loading, so open-world frame time remains unverified. [Linux native PR](https://github.com/spidercraft/VIctory/pull/48)

## Linux native pipeline requests and prewarming

**Run by** [@gmankab](https://github.com/gmankab)

A synthetic compute pipeline stayed queued until its draw's wait ended. The runtime recorded it for prewarming on the next launch. The stand-alone contribution passed all 44 regular GPU tests on the RX 6800 and Lavapipe. The integration passed all 75 on both backends. [Linux native PR](https://github.com/spidercraft/VIctory/pull/46) · [Tested integration](https://github.com/gmankab/victory/tree/test/linux-native-remember-requested-pipelines)

The first 1080p Series S run on Lavapipe started with 292 pipeline keys and saved 1,420. All 1,128 additions were requested by the title, and 1,119 of them never became available to a draw or dispatch during the recorded run. The process exited after 421 seconds on a guest null write. The last captured frame was 1,300. The last image showed the illustrated loading screen.

The repeat used the same executable, Vulkan driver build, console profile, and original cache files with just the learned pipeline list substituted. It built or loaded **658 of the added keys**. Both added keys requested before the cap had finished before their first request. Frame 1,300 was captured at **193 seconds**, versus **354 seconds** in the first run. The repeat reached frame **1,750** and hit its **450-second** cap. Its last capture still showed the loading artwork. The runs took place at different times and used relocated tool paths in the repeat. Gameplay frame time and player control remain unverified.

Fxceless's queue policy moves foreground requests ahead of prewarm work. The Linux native regression checked order, promotion, and that the workers claim a promoted job only once. All 44 regular GPU tests passed on each backend on the stand-alone contribution. The integration built a Linux native executable and passed 76 regular GPU tests per backend. [Tested integration](https://github.com/gmankab/victory/tree/test/linux-native-prioritize-requested-pipelines) · [Linux native PR](https://github.com/spidercraft/VIctory/pull/47) · [Fxceless source](https://github.com/fxceless/victory-fork/commit/578d459c344aa44753e9a61ea6ba10a8c68f6ea6)

Both title runs seeded with the learned list timed out at 450 seconds. The previous run reached frame 1,750 and the priority build reached frame 1,900. The last images showed the same loading artwork. The nine-draw loading phase had a median of **2.3 FPS** in each run, and each built or loaded **830 unique pipeline keys**. The priority change's effect on gameplay frame time remains unverified.

## Linux native dynamic descriptor exhaustion

**Run by** [@gmankab](https://github.com/gmankab)

The two preceding RX 6800 runs reported exhaustion of dynamic descriptor slots shortly before AMDGPU reset the graphics queue. The Linux native port of the merged fix drops a draw or dispatch if its required dynamic table cannot be created and preserves the smaller reserve on capped heaps. A test exhausted the reserve, verified that no table offset was returned, then released the reserve and verified successful allocation. The regression passed on the RX 6800 and Lavapipe. The integrated Lavapipe build passed 68 regular GPU tests. [Original Victory PR](https://github.com/Marvisak/VIctory/pull/336) · [Linux native contribution](https://github.com/spidercraft/VIctory/pull/36)

The integrated RX 6800 title run loaded the driver-compatible library in **10 ms**. It reached heavy 3D work at about **199 seconds**. Eleven heavy samples ranged from **0.8 to 1.5 FPS**, with a **1.5 FPS** median, **196.2 ms** median preparation, and up to **639** pipelines pending. Vulkan validation emitted no VUIDs, and the kernel logged no AMDGPU fault or reset. Dynamic allocation succeeded throughout the title run. Whole-card GPU activity was **2%** at the median and **19%** at the peak. [Tested branch](https://github.com/gmankab/victory/tree/test/linux-native-dynamic-descriptor-exhaustion) · [Run index](../../data/benchmarks/gmankab/runs.csv)

Victory exited with status 5 after **259 seconds** following a guest invalid read. The last capture, frame 5000, showed the illustrated loading screen. REA and the saved stack confirmed that a guest lookup tried three alternatives and dereferenced the result of the last call, which was null. Two raw DirectStorage requests logged generic file-read failures within ten seconds before the exit. Pipeline tracing recorded **678** library loads and **76** creations. **73 of 710** used pipeline keys had no exact match in the archive census. [Run index](../../data/benchmarks/gmankab/runs.csv)

## Linux native guarded DirectStorage reads

**Run by** [@gmankab](https://github.com/gmankab)

The DirectStorage fixture confirmed that a raw file read into two blocks rearmed by write watch returns `EFAULT`. The retry staged up to 64 KiB at a time and held the VM lock only while transferring bytes already read. The fixture verified all 132 requested bytes, the two neighboring bytes, and that both blocks were marked dirty for the next refresh. The contribution branch passed 12 DirectStorage tests and 14 VM tests. The integrated build passed 12 DirectStorage tests and 16 VM tests. The older run's generic status leaves its original Linux error unknown. [Victory PR](https://github.com/spidercraft/VIctory/pull/37) · [Linux file read semantics](https://man7.org/linux/man-pages/man2/pread.2.html) · [Linux process memory transfers](https://man7.org/linux/man-pages/man2/process_vm_writev.2.html)

XCL's Windows file path reads into a private staging buffer before writing to the guest. Leonida's direct-read path pins the destination pages until the read finishes and can reopen them on `ERROR_NOACCESS`. [XCL source](https://github.com/xcl-dev/xcl/blob/8b957bec6f82495aec940944c6bf47faa02db590/crates/xcl/src/hle/dstorage.rs#L902) · [Leonida source](https://github.com/ubiquitarianism/leonida/blob/ba626820ec54776fb0faffa2413727285befe85b/runtime/src/title_page_watch.h#L899)

The integrated RX 6800 run recovered **seven** file reads through staging, delivering **132,768 bytes**. The title passed the earlier 259-second guest-crash point and reached the **330-second** test cap with no guest exception or reported file-read failure. Its last capture, frame 4096, was black. The dynamic descriptor reserve filled and the runtime counted at least **4,096** commands skipped because their tables were unavailable. [Tested branch](https://github.com/gmankab/victory/tree/test/linux-native-directstorage-guarded-reads) · [Run index](../../data/benchmarks/gmankab/runs.csv)

The 26 heavy samples ranged from **0.8 to 2.1 FPS**, with a median of **1.4 FPS** and **278 ms** of median preparation. The last sample with zero pipelines pending reached 2.1 FPS and spent **300 ms** preparing the frame, **45 ms** on the GPU, and **112 ms** copying data back. Whole-card GPU activity had a median of 2%. The kernel logged a graphics-ring timeout, PCIe errors, and a successful ring reset about **311 seconds** into the run. Niri and the Codex recovery service kept their process IDs. The driver breadcrumbs bounded an incomplete interval containing root bindings and a resource transition. The cause of the GPU stall remains unresolved. [Run index](../../data/benchmarks/gmankab/runs.csv)

## Linux native dynamic descriptor allocation

**Run by** [@gmankab](https://github.com/gmankab)

The cursor backport found all 41,472 expected results in a CPU harness that checked every occupancy pattern, cursor, and request size for a nine-slot heap. The original search missed 996 valid allocations. An RX 6800 host-heap test also verified that a free run can cross the search cursor and that separate slots at the physical ends of the heap do not form a run. [Original cursor fix](https://github.com/Marvisak/VIctory/pull/974)

For bursts of 4,096 failed requests against 65,536 occupied slots, the median of seven synthetic bursts fell from **668.45 ms** with the cursor fix alone to **0.256 ms** with failed searches cached. A release that joined free runs let a previously impossible request succeed immediately. Three focused checks passed on each of the RX 6800 and Lavapipe for the contribution and six passed per backend for the integration build. The integrated Linux native executable built successfully. Gameplay performance and GPU stability with these allocator changes remain unmeasured. [Failure-cache source](https://github.com/Marvisak/VIctory/pull/2394) · [Linux native contribution](https://github.com/spidercraft/VIctory/pull/38) · [Tested integration](https://github.com/gmankab/victory/tree/test/linux-native-descriptor-allocator)

## Linux native descriptor sharing across graphics stages

**Run by** [@gmankab](https://github.com/gmankab)

Victory's test recorded graphics stages that needed 64 and 104 slots from the same table. Sharing reduced the reservation from **168 to 104 slots**, about **38%**, and passed the second stage's deferred GPU write through to copy-back tracking. It also checked that a stage using the mirrored table keeps that binding and that sharing does not extend beyond the guest heap. The fixture passed on RX 6800 and Lavapipe on the exact contribution branch. [Linux native PR](https://github.com/spidercraft/VIctory/pull/39) · [Fxceless source](https://github.com/fxceless/victory-fork/commit/5d659e0c8c64cb4e8628d6b7b5d8f953bd16b457)

The first software launch failed to create a D3D12 device because its harness enabled hardware-only breadcrumbs. After removing those flags, the integrated Linux native run reached the illustrated loading screen and ran to its **360-second** cap. Its last ten timing samples were **0.4–0.5 FPS**, and the last capture was frame **850**. Dynamic descriptor exhaustion did not occur in that run. The pre-sharing build reproduced both Vulkan validation message IDs under the same launch settings, `VkWriteDescriptorSet-dstSet-04611` and `RuntimeSpirv-None-06343`. Open-world performance and descriptor pressure under RADV gameplay remain unmeasured for this build. [Tested integration](https://github.com/gmankab/victory/tree/test/linux-native-shared-stage-tables)

Leonida uses a Vulkan descriptor hash table and retires replaced generations after in-flight submissions finish. Its benchmark passed the CPU/GPU lookup checks and all **99** timing cases on the RX 6800 with Vulkan validation enabled. For 524,288 divergent lookups against 100,000 entries, the median of seven runs was **0.126 ms** in device-local memory and **0.403 ms** in host-visible system memory. The divergent-miss case took **0.360 ms** and **14.412 ms** respectively. Host-visible device-local memory tracked device-local performance on this card. XCL uses a shared shader-visible descriptor heap and maps pages on first use. The measurements apply to Leonida's lookup path. [Leonida benchmark](https://github.com/ubiquitarianism/leonida/blob/bdbc7e9236465c5d6a1305de6edd3fc09c2d6fd9/runtime/test/vk_descriptor_bench.cpp) · [Leonida descriptor lifetime](https://github.com/ubiquitarianism/leonida/blob/bdbc7e9236465c5d6a1305de6edd3fc09c2d6fd9/runtime/src/vulkan_bindless.h) · [XCL descriptor heap](https://github.com/xcl-dev/xcl/blob/3867e7bc6889707959fb61fafc11dc5851ef3610/crates/xcl/src/hle/d3d12x/console_heap.rs)

## Linux native missing shader pipelines

**Run by** [@gmankab](https://github.com/gmankab)

The last RX 6800 timing sample with an empty pipeline queue logged **778** draws or dispatches per frame skipped for unavailable pipelines. The five largest groups accounted for about **483** of those skips. The runtime refused their shaders during scalar memory reads, 64-bit image atomics, structured-buffer stride inference, compute root-argument loads, and reading a lane mask as data. A comparison of those exact five inputs on the November 26 source snapshots found that both Fxceless and Marvisak emitted DXIL that passed DXC validation for all five. Those translators' integration and gameplay output remain unverified on the Linux native branch. [Fxceless snapshot](https://github.com/fxceless/victory-fork/commit/52ac4ec0f8a03f103c146e60228e53612759d435) · [Marvisak snapshot](https://github.com/Marvisak/VIctory/commit/63bcca43c6a00a758510398fa43096ce7e4f7df0)

The 64-bit texture atomic backport passed synthetic add and compare-exchange tests. The earlier lowering failed DXIL validation because its heap atomic feature flag was missing. The corrected shader, which accounted for **116** skipped dispatches per frame in the RX 6800 sample, passed DXIL validation and created a Linux native compute pipeline on the contribution and integration branches. A separate synthetic shader performed an exchange and addition on a one-texel R32G32_UINT texture. Both RX 6800 and Lavapipe read back `[0, 7]`, confirming carry into the high dword. The profiled shader was checked through pipeline creation. Its dispatch output and gameplay effect remain unmeasured. [Linux native PR](https://github.com/spidercraft/VIctory/pull/41) · [Atomic lowering source](https://github.com/Marvisak/VIctory/pull/305) · [Heap feature source](https://github.com/Marvisak/VIctory/pull/1899) · [DirectX capability queries](https://microsoft.github.io/DirectX-Specs/d3d/HLSL_SM_6_6_Int64_and_Float_Atomics.html#capability-queries)

The scalar SRV fix preserves the guest record count when the shader uses the same descriptor for a scalar load and as data. A 32-byte synthetic input returned **32** for a raw view and **8** for a four-byte stride on RX 6800 and Lavapipe. Of **20** captured shaders that the pre-fix Linux native build refused, the integration translated **six**, and all six passed DXIL validation and host pipeline creation on RX 6800. They comprised four vertex and two compute shaders. The largest skipped pixel-shader group still fails when a descriptor word from one path reaches a branch merge. The six-pipeline check did not measure frame time. [Linux native PR](https://github.com/spidercraft/VIctory/pull/42) · [Tested integration](https://github.com/gmankab/victory/commit/e82d6043a9a22effb0b63273ccbb892343e996e8) · [Scalar-load source](https://github.com/Marvisak/VIctory/pull/953)

The runtime stride-table backport recovered **14 of 31** previously refused compute shaders. All 14 passed DXIL validation and created RX 6800 pipelines. Ten accounted for **105 skipped dispatches per frame** in the last profiled sample, including **84** for the most frequent shader in this group. Fifteen of the remaining shaders exposed lane-mask refusals and two exposed descriptor-data refusals. Synthetic GPU readback passed for 12- and 20-byte structured views, raw views, and unavailable slots on RX 6800 and Lavapipe. The runtime fixture verified slot replacement and deferred refresh on both backends. Its Lavapipe run reported the same descriptor-write validation message seen in earlier tests. Gameplay dispatch results, frame timing, and heap refresh cost remain unmeasured. [Linux native draft PR](https://github.com/spidercraft/VIctory/pull/43) · [Runtime stride source](https://github.com/Marvisak/VIctory/pull/193) · [Heap refresh source](https://github.com/Marvisak/VIctory/pull/1544)

The stale scalar phi backport recovered the pixel and Wave64 mesh shaders associated with **157** and **52** skipped draws per frame in the earlier RX 6800 trace. Both shaders passed DXIL validation and created host pipelines on RX 6800 in the independent contribution and the integration. The regression accepted the case where the missing scalar word cannot reach a read and refused two cases where it can. The full integration shader suite passed **104 of 105** tests on each backend. The sole raw-offset assertion also failed on the earlier build. The uniform-join backport recovered none of the 16 tested lane-mask failures and stayed local. [Linux native draft PR](https://github.com/spidercraft/VIctory/pull/44) · [Tested integration](https://github.com/gmankab/victory/tree/test/linux-native-stale-phi-values)

The Linux native stride build reached the illustrated loading screen with both Lavapipe drivers at 1080p. Over seconds **155–300**, the stock run logged a median **0.4 FPS** across 22 samples and the run with the local BC7 cache logged **2.2 FPS** across 27 samples. The driver builds differ, so this run measures their combined difference. The stock run reached frame 320 and hit its 540-second cap. The cached run captured frame 1200, then exited with a segmentation fault around **404 seconds**. The kernel put the fault in `libVkLayer_khronos_validation.so`. The underlying trigger remains undetermined. [Tested runtime branch](https://github.com/gmankab/victory/tree/test/linux-native-heap-view-strides)

The repeat with the cached driver and the validation layer disabled logged a median **2.3 FPS** and **420.2 ms** of software rendering per frame across 26 samples over seconds **155–300**. The title exited with status **5** at **371 seconds**. It hit the same watchdog null write recorded in earlier runs. The last capture, frame **1100**, showed the illustrated loading screen. The kernel logged no fault during the run. The cause of the stall that triggered the watchdog remains unknown. [Run index](../../data/benchmarks/gmankab/runs.csv) · [Earlier watchdog analysis](https://github.com/Marvisak/VIctory/pull/454)

The older compute spill-table patch moved the ray-tracing builder shader's refusal from offset `0x30` to `0x74`. The compiler still could not resolve a root address, so that trial stayed local. [Spill-table source](https://github.com/Marvisak/VIctory/pull/302)

The Linux native descriptor lifetime backport carries a host heap identity in every recorded slot reservation. A stale release routed to a full replacement heap left its slots reserved on RX 6800 and Lavapipe. A release with the replacement's identity made the slot available. Both backends also passed the integrated table-sharing and exhaustion fixtures. This verifies the allocator behavior in isolation. Whether heap address reuse occurred in the earlier title run is unconfirmed. [Fxceless source](https://github.com/fxceless/victory-fork/pull/1324) · [Linux native PR](https://github.com/spidercraft/VIctory/pull/40)

## Linux native host heap import

**Run by** [@gmankab](https://github.com/gmankab)

The RX 6800 reports `VK_EXT_external_memory_host` and a 4 KiB minimum host-pointer alignment. A Linux native fixture allocated 64 KiB through the guest VM table and imported it with `ID3D12Device13::OpenExistingHeapFromAddress1`. Two GPU transfers read the exact new CPU byte pattern after the original guest mapping changed. Vulkan validation reported zero VUIDs. This verifies direct guest reads on the hardware. The title path still uses its shadow buffers. [Existing in-place proposal](https://github.com/Marvisak/VIctory/pull/2112) · [Driver fixture](https://github.com/HansKristian-Work/vkd3d-proton/blob/71cae091b06571e51446cf2a937f0b04c237c04d/tests/d3d12_win32_exclusive.c)

The opt-in `VICTORY_IMPORT_UPLOADS=1` path applies to renderer-owned placed upload buffers. A one-time GPU readback checks that the imported heap sees the original allocation. The RX 6800 fixture verified reads at a 64 KiB offset after the guest released its buffer and heap. When the driver substituted its own backing, the fixture verified the fallback shadow copy. The contribution branch passed six regular resource tests plus the direct and fallback fixtures. [Victory draft PR](https://github.com/spidercraft/VIctory/pull/34) · [Tested driver branch](https://github.com/gmankab/vkd3d-proton/tree/test/strict-host-import-typed-alignment)

The 230-second RX 6800 title run logged zero imports. It reached heavy 3D preparation about 200 seconds in and reported **1.1 FPS** with 844 draws per frame, 721 pipelines pending, and **134.7 MB copied in 182.4 ms per frame**. Whole-card GPU activity had a median of 2% across 78 samples. The final captured frame, 5000, showed the illustrated loading screen. [Run index](../../data/benchmarks/gmankab/runs.csv)

AMDGPU reported the same SQC data-read fault class as the previous hardware run, followed by a successful graphics-ring reset. Niri and Codex kept their process IDs. The slow submission near this reset contained 29 lists and 65 logged pipeline entries. Three entries also appeared in the previous run's final slow batch. Batch membership leaves the faulting command unidentified. Vulkan validation logged zero VUIDs, and the release driver used for the run had tracing compiled out. [Run index](../../data/benchmarks/gmankab/runs.csv)

A separate build of the same driver with Meson `-Denable_trace=true` enabled `AMD_buffer_marker` breadcrumbs on the RX 6800. The placed-buffer readback passed on that build with Vulkan validation active. The check used a normal transfer fixture. [Tested driver branch](https://github.com/gmankab/vkd3d-proton/tree/test/strict-host-import-typed-alignment)

The three compute shaders shared by the two slow batches passed offline DXIL validation. All three also created D3D12 pipeline objects using Linux native vkd3d-proton and Lavapipe with `VKD3D_SHADER_MODEL=6_6` and hardware render devices hidden. The shaders use descriptor tables and inline constants. The test ended at pipeline creation, so the runtime bindings and the faulting command remain unverified. [Tested driver branch](https://github.com/gmankab/vkd3d-proton/tree/test/strict-host-import-typed-alignment)

## XCL startup preparation under Wine

**Run by** [@gmankab](https://github.com/gmankab)

XCL `8b957bec` ran under Wine 11.21 and Lavapipe with the Series S device profile. It started preparing **26,503 shaders** before guest execution. The last reported count was **400 at 55 seconds**. The 85-second cap expired during preparation, before a gameplay frame was reported. [XCL startup preparation](https://github.com/xcl-dev/xcl/pull/2555) · [Run index](../../data/benchmarks/gmankab/runs.csv)

## Linux native parallel shadow refreshes

**Run by** [@gmankab](https://github.com/gmankab)

The heavier RX 6800 title sample reported 42.8 ms in the copy preparation counter, including 28.28 ms in memory moves and 6.56 ms in write-watch queries. The Linux native port of Fxceless's parallel refresh approach gives each worker a separate host mapping and joins all jobs at the existing flush boundary. It is opt-in with `VICTORY_WIDE_REFRESH=1`. [Fxceless proposal](https://github.com/fxceless/victory-fork/pull/38) · [Tested integration](https://github.com/gmankab/victory/tree/test/native-parallel-refresh)

| Copy fixture | 512 KiB serial → parallel | 44 MiB serial → parallel |
|---|---:|---:|
| RX 6800 mapped buffers, RADV | 69.90 → 31.92 µs | 5.97 → 3.86 ms |
| Lavapipe, kernel page probes | 68.78 → 28.06 µs | 5.94 → 3.89 ms |
| Lavapipe, guarded reads enabled for smaller ranges | 41.73 → 23.28 µs | 6.04 → 3.92 ms |

Each table entry is the median of five process medians. Each process timed 21 flushes after six warmups, using the same eight host mappings and alternating test order. The fixtures verified every byte and checked that disjoint requests left the intervening bytes untouched. A separate RX 6800 pair on the exact Linux native PR head measured **6.80 → 4.53 ms** at 44 MiB and passed the same byte checks. That head also passed 46 regular GPU tests on Lavapipe. Open-world frame time and the effect on write-watched gameplay ranges remain unverified. [Upstream PR](https://github.com/spidercraft/VIctory/pull/30) · [Individual measurements](../../data/benchmarks/gmankab/benchmark-logs.csv)

## Fxceless startup controls

**Run by** [@gmankab](https://github.com/gmankab)

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

**Run by** [@gmankab](https://github.com/gmankab)

The RX 6800 runs used Wine and RADV with PulseAudio available, the same checkpoint, and the same scripted input. Each captured the same 25 frames through frame 1,024 and reached its time cap without the startup breakpoint. The newest Fxceless baseline used the read-only stream probe.

| Build and configuration | Result |
|---|---|
| `ceb9e1f7`, 90 s | All 25 captures were black. Host draws and dispatches executed. |
| Published `d56316bb`, 90 s | 13 of 25 captures had visible content. Frame 200 showed the interface, and frame 1,024 showed the illustrated loading screen. |
| Published `d56316bb`, `VICTORY_PRESENT_PLANES=0`, 60 s | All 25 captures were black again when only the first present plane was shown. |

Toggling the compositor on the same build isolated the second plane as the source of the visible Series S loading screen in this test. The captured sequence ended during loading. The raw frame-rate samples in the run index cover startup and loading. [Related Fxceless report](https://github.com/fxceless/victory-fork/issues/425) · [Second-plane proposal](https://github.com/fxceless/victory-fork/pull/188)

### Second-plane proposal on `ceb9e1f7`

**Run by** [@gmankab](https://github.com/gmankab)

Six commits from the proposal were rebased onto `ceb9e1f7`, with a follow-up that carries the interface resource through queued presents. Wine runs used PulseAudio, RADV and the Series S checkpoint. The two runs of the port enabled asynchronous submission and presentation and used the same later Enter schedule. Their logs confirm the switches were enabled. The evidence for the compositor comes from captured frames, which use the synchronous readback path. [Tested branch](https://github.com/gmankab/victory/tree/contrib/fx-series-s-pr188-current)

| Build and capture source | Result |
|---|---|
| Published `d56316bb`, 200 s, original input schedule | The last capture at frame 2,400 showed loading artwork. Whole-card busy samples were **1% median and 25% peak** across 101 readings. System-wide VRAM use peaked at 7,985 MiB. |
| Rebased proposal, 180 s, first-plane readback | All 30 captures were black, although the host logged initialization of the overlay pass. |
| Rebased proposal with composed-buffer readback, 135 s | **26 of 28** captures were nonblack. The same 28 frame numbers in the first-plane run were all black. Frame 1,300 still showed loading. |

The port restored visible output during startup and illustrated loading. Both GPU readback tests ran on the RX 6800 and passed, as did the plane parser test. The recorded frame rates cover startup and loading under different configurations. [Upstream test report](https://github.com/fxceless/victory-fork/pull/188#issuecomment-7191270104)

## Isolated measurements and build times

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

## Evidence index

- [Run observations](../../data/benchmarks/gmankab/runs.csv) extract timestamps, exit codes and explicit frame/FPS counters from retained logs. An exit of 124 records a harness timeout. A missing exit means the available log had no exit sidecar.
- [Frame timings](../../data/benchmarks/gmankab/frame-timings.csv) contain the retained runtime timing CSV rows. Read rows in the context of the test configuration and stage.
- [Benchmark log measurements](../../data/benchmarks/gmankab/benchmark-logs.csv) index timing lines in saved benchmark output. They include trials and repeats. Labels and units use the source file’s format.
- [Test results](../../data/benchmarks/gmankab/test-results.csv) index suite summaries. Multiple rows can come from repeated runs of the same suite.
- Filenames in the index identify local evidence. Figures reported only in historical replies or reviews appear in the tables on this page. Captures and title assets are kept out of the public repository.
