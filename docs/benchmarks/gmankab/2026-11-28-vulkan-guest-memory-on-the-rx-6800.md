# November 28, 2026 · Vulkan guest memory on the RX 6800

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
