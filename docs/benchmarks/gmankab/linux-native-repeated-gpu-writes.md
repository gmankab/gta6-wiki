# Linux native repeated GPU writes

**Run by** [@gmankab](https://github.com/gmankab)

The heavy RX 6800 loading sample requested 2,241 shadow refreshes per frame. Each declaration of a writable range previously flushed the refresh batch. The patch keeps the first declaration for each exact range and uses the same order for copy-back. A regression across two command lists verified that an unrelated read stayed queued and separate CPU and host writes were both preserved. [Run index](../../../data/benchmarks/gmankab/runs.csv) · [Victory PR](https://github.com/spidercraft/VIctory/pull/35)

| Preparation fixture, 48 requests for one 4 MiB read range | Baseline | Candidate |
|---|---:|---:|
| RX 6800, untracked pages | 16.87 ms | 0.33 ms |
| Lavapipe, untracked pages | 17.35 ms | 0.36 ms |
| RX 6800, clean write-watched pages | 80.48 µs | 6.76 µs |
| Lavapipe, clean write-watched pages | 67.24 µs | 6.07 µs |

Each row is the median of 21 paired samples. Preparation copied 192 MiB before the patch and 4 MiB afterward for untracked pages. The clean write-watched case copied zero bytes in either mode. The change passed all 44 regular GPU tests on Lavapipe, and the new regression also passed on the RX 6800. The Lavapipe descriptor-slot validation warning was reproduced on the unmodified base. These are synthetic preparation timings. Open-world frame time remains unmeasured for the patch. [Victory PR](https://github.com/spidercraft/VIctory/pull/35)

The later Linux native GPU snapshot port saves each declared byte on first use and reconstructs the original 64 KiB page when readback spans saved ranges. A 174 MiB CPU fixture measured **440.23 → 90.04 ms** and **4,116 → 156 MiB** copied for 49 growing ranges. Two distant 1 MiB ranges took **19.18 → 0.16 ms** and copied **175 → 2 MiB**. Times are medians of nine warmed trials. The stand-alone contribution passed all 44 regular GPU tests on RX 6800 and Lavapipe. The integration passed all 74 on both backends. The fixtures measure snapshot preparation and writeback correctness. Title frame time remains unmeasured for this patch. [Linux native PR](https://github.com/spidercraft/VIctory/pull/45) · [Marvisak source](https://github.com/Marvisak/VIctory/pull/3318) · [Individual measurements](../../../data/benchmarks/gmankab/benchmark-logs.csv)
