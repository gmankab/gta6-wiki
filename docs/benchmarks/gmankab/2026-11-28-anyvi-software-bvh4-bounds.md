# November 28, 2026 · AnyVI software BVH4 bounds decoding

**Run by** [@gmankab](https://github.com/gmankab)

REA matched all seven cached shader variants of the six slow AnyVI compute pipelines to the software four-child BVH helper by comparing their SPIR-V function signatures. The diagnostic credits run attributed **5.51 of 5.94 seconds** of indirect-dispatch intervals to those pipelines in a 15-second window. [Outdoor cinematic benchmark](2026-11-28-frozzy-anyvi-outdoor-cinematic.md)

I tested a local patch that selects the FP16 or FP32 bounds layout once for each node instead of once for each of its four children. The translation continues to check access to the full-width node's second block and uses the same intersection, child ordering, and depth-growth rules. The baseline was local revision `600790ee`, based on Frozzy's `anyvi` branch, and the patched build was `7a39d40b`. [AnyVI source revision](https://git.disroot.org/frozzy/kyty/commit/eed0c578eab39717030a9890cc0d3b0220b6e8c5)

**Synthetic dispatches** Both builds passed all **28** cases from `shader_recompiler_compute_tests --ray-tracing-only` on Fedora Linux 45 with the Radeon RX 6800 and RADV 26.2.6. I warmed both Mesa caches, then ran **12 paired repetitions**, alternating which build went first. Each repetition ran all 28 cases. I compared the GPU timestamps for the six ending BVH4 dispatches. Their combined median fell from **67.64 µs** to **59.86 µs**, an **11.5% reduction**. Every patched repetition was faster than its paired baseline. [Per-run timings](../../../data/benchmarks/gmankab/2026-11-28-bvh4-dispatch-pairs.csv) · [Mesa timing documentation](https://docs.mesa3d.org/u_trace.html)

RADV compiled the final barycentric sorted fixture from 1,428 to 1,375 machine instructions and from 53 to 41 branches. The shader still used 64 VGPRs and reported zero spills. The measurements cover synthetic GPU dispatches.

## PS5 title run

I ran the patched Linux native build on the same RX 6800 with RADV 26.2.6 and 1080p guest output. It reused the Vulkan driver cache and recompiled the translated shader store. REA checked the seven cached variants of the six slow compute programs. All seven used the new BVH4 helper. Its SPIR-V had **five conditional branches**, down from **eight**, with the same load and call counts. This launch omitted pipeline debug names from the timing trace, so per-shader GPU time still needs a matched run. [KytyPS5 PR #2607](https://github.com/KytyPS5/KytyPS5/pull/2607)

The illustrated loading screen completed **717 flips in 24 seconds** and averaged **2.13% graphics-engine activity**. First-scene preparation then completed zero flips in a separate 24-second sample. REA counted **83 SPIR-V compilation zones** totaling **23.01 seconds**, while 84 driver pipeline-creation zones totaled **37 ms**.

A later 24-second nighttime exterior sample completed **11 flips**, with **1,384 ms median spacing** and **34.59% graphics-engine activity**. The profile recorded **7,743 buffer readbacks** and **14.66 seconds** of summed caller time across threads. Opposite eight-second movement holds completed one flip each, and the comparisons showed the same landmarks in place. Look inputs and a later pause probe did not establish control.

The game reached dawn with missing geometry and exited after **23 minutes 27 seconds**. REA confirmed that the baseline and patched runs both executed the same instruction that writes zero directly to address zero. It followed the last completed flip by **29.46 seconds** in the baseline and **30.28 seconds** in the patched run. Graphics-engine activity fell below **0.02%** during both stalls. The kernel logged no GPU reset in the patched run, and its story save matched the baseline byte for byte. The timing is consistent with a guest watchdog. [KytyPS5 compatibility thread](https://github.com/KytyPS5/KytyPS5/issues/2600)

## Offline shader precompile

I ran the patched build with `--precompile-shaders --precompile-threads 12`, the recorded raw shader recipes, and the compatible cache from the title run. It processed **3,203 shader variants** and created **2,917 Vulkan pipelines** in **124.5 seconds**, with zero reported translation or pipeline failures. The next title launch loaded **3,243 shader modules** and **2,917 pipeline records** from that output. These measurements cover cache preparation and startup. The rerun enables pipeline debug names for the 3D timing comparison.

## Warm PS5 run with named dispatches

The precompiled build held **30.00 completed flips/s** during a 24-second sample of the illustrated loading screen. Graphics-engine activity averaged **2.16%**. During the nighttime exterior, REA counted **19 completed flips** in a separate 24-second sample, with **2,767 ms** p95 spacing and **22.66%** graphics-engine activity. The profile recorded no shader translation or compute pipeline creation in that window. It recorded **8,269 buffer readbacks**, totaling **10.30 seconds** of caller time across threads.

I compared two 15-second windows from the same nighttime car sequence. REA matched indirect dispatches by shader name and their ordered position in a command batch. The table shows the four target pipelines with matching batches. Pipeline ranks follow their total GPU time in the baseline window.

| Pipeline rank | Baseline dispatches | Baseline median | Patched dispatches | Patched median |
| --- | ---: | ---: | ---: | ---: |
| 1 | 9 | 70.49 ms | 9 | 58.87 ms |
| 4 | 23 | 32.70 ms | 19 | 17.07 ms |
| 5 | 16 | 22.39 ms | 18 | 15.80 ms |
| 6 | 14 | 41.96 ms | 9 | 29.70 ms |

The baseline completed **11 flips** and used **50.86%** of the graphics engine. The patched window completed **eight flips** and used **42.24%**. The camera cuts and dispatch counts differed. These GPU intervals establish the observed timings in each run, and the effect of the patch on game frame rate remains unresolved.

Across 45 five-second reporting windows from the warm exterior, REA counted **332 flip requests** and **2,344 waits** to read indirect workgroup counts for storage-backed shared memory. Those waits totaled **36.12 ms per flip request**. The related frame profiler measures completed main-output flips separately.

In daylight I held the right stick left and then right for eight seconds each. REA verified both axis directions on the virtual controller and counted **11 and 12 completed flips** while the stick was held. Two fixed background regions showed zero measured shifts after either hold. Player control remains unverified.
