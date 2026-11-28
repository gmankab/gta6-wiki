# November 28, 2026 · Disroot Kyty Vulkan recorder

**Run by** [@gmankab](https://github.com/gmankab)

Disroot Kyty `3ae51388` includes a separate Vulkan command-recording thread. I built it as local revision `24abcdb4` with the Intel loader changes. The recorder and memory tracker suites passed. The full virtual-memory suite initially found that the parallel instruction scanner did not aggregate its CLZERO counts. After adding that aggregation, the suite passed on the RX 6800 with the DMA-BUF fixture and Khronos Vulkan validation, with zero VUID errors. [Disroot revision](https://git.disroot.org/flo/kyty/commit/3ae5138853513d126e8d5b8b2b8bea3076e102da) · [Recorder change](https://git.disroot.org/flo/kyty/commit/c0795c55e30e2054f0d397f0d65bbab48cbff36f)

The warm title run reached its first 3D sequence after about 7 minutes 20 seconds. It then exited at **468 seconds** during the first recorder-off interval. REA decoded the fault as a 16-bit guest memory read through an indexed pointer in the main module. The kernel logged no GPU reset. The timing establishes when the fault occurred, and the recorder comparison remains incomplete.

## Recorder-only comparison

**Run by** [@gmankab](https://github.com/gmankab)

I backported the recorder toggle, counters, and draining changes to the previously tested `dbf3c1af` base as local revision `0531ec55`. REA captured a passing recorder suite and four alternating windows in the same warm 1080p title run on Fedora Linux 45, RX 6800, and host RADV 26.2.6. The graphics command thread was measured separately from the recorder. I paused the other build before 3D and used complete five-second bins after each toggle settled. [Recorder toggle and counters](https://git.disroot.org/flo/kyty/commit/4ef7f7b5) · [Draining change](https://git.disroot.org/flo/kyty/commit/89638ad1)

| Recorder | Window | Main output presents / s | RX 6800 graphics busy | Command-thread CPU µs / draw or dispatch |
|---|---:|---:|---:|---:|
| Off | 29.99 s | 1.73 | 18.21% | 164.75 |
| On | 25.02 s | 2.60 | 21.32% | 152.54 |
| Off | 29.98 s | 1.90 | 21.12% | 159.30 |
| On | 25.01 s | 1.56 | 26.98% | 146.59 |

The recorder reduced command-thread CPU time per draw or dispatch by about 8% in both pairs and used another 38–41 µs per operation on its own thread. The title progressed through different cinematic shots, and completed presentation rates showed no repeatable gain. The four intervals had zero new graphics or compute pipelines in the captured counters. Player control remains unverified. [Per-window data](../../../data/benchmarks/gmankab/2026-11-28-kyty-recorder-backport.csv)

The backport continued with the recorder off and reached the opening credits. Paired camera and movement inputs did not establish player control. It exited after **12 minutes 24 seconds**, at the same guest instruction as the newer build. REA reconstructed the packed index `0xffffffff` from both fault contexts. The code used the hash lookup's miss value as a slot index and attempted an out-of-range read. The reason the expected entry was absent remains unresolved. The kernel logged no GPU reset during the run.
