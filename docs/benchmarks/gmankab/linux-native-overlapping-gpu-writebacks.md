# Linux native overlapping GPU writebacks

**Run by** [@gmankab](https://github.com/gmankab)

A synthetic 174 MiB shared buffer received 49 growing declarations. The existing copy-back repeatedly visited the same bytes. The patch remembers the portions that have already been compared or written and leaves failed portions available for a later retry. On the RX 6800, the fixture measured the following times after submission preparation. Each figure is the median of two process medians, with seven timed trials per process.

| Declarations | Previous | With overlap tracking |
|---|---:|---:|
| Growing, overlapping ranges | 1,085.1 ms | 45.9 ms |
| Adjacent, disjoint ranges | 49.6 ms | 49.5 ms |

The fixture verified GPU-written bytes, nearby CPU stores, and untouched neighboring bytes. A separate regression kept a newer CPU store through a second overlapping writeback. Another retried a four-byte write after a 64 KiB read crossed an inaccessible page. The integration passed 78 regular GPU tests each on RX 6800 and Lavapipe. These timings measure the fixture's copy-back stage. [Integration](https://github.com/gmankab/victory/tree/test/linux-native-reconcile-writebacks) · [Marvisak source](https://github.com/Marvisak/VIctory/pull/3412) · [Individual measurements](../../../data/benchmarks/gmankab/benchmark-logs.csv)

The stand-alone contribution passed 45 regular GPU tests on each backend. The RX 6800 Series S title run ended at its 245-second cap with frame 5,900 captured and a **30.0 FPS** median across 43 loading samples. The control, with the same seed, was stopped at 157 seconds on request. It captured frame 3,000 and logged a **29.0 FPS** median across 23 loading samples. Both runs logged at most 11 draws per frame and 0.0 ms of copy-back in every timed sample. The kernel logged no AMDGPU fault or reset in either interval. This pair covers startup and loading, so open-world frame time remains unverified. [Linux native PR](https://github.com/spidercraft/VIctory/pull/48)
