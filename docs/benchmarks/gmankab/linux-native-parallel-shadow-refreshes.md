# Linux native parallel shadow refreshes

**Run by** [@gmankab](https://github.com/gmankab)

The heavier RX 6800 title sample reported 42.8 ms in the copy preparation counter, including 28.28 ms in memory moves and 6.56 ms in write-watch queries. The Linux native port of Fxceless's parallel refresh approach gives each worker a separate host mapping and joins all jobs at the existing flush boundary. It is opt-in with `VICTORY_WIDE_REFRESH=1`. [Fxceless proposal](https://github.com/fxceless/victory-fork/pull/38) · [Tested integration](https://github.com/gmankab/victory/tree/test/native-parallel-refresh)

| Copy fixture | 512 KiB serial → parallel | 44 MiB serial → parallel |
|---|---:|---:|
| RX 6800 mapped buffers, RADV | 69.90 → 31.92 µs | 5.97 → 3.86 ms |
| Lavapipe, kernel page probes | 68.78 → 28.06 µs | 5.94 → 3.89 ms |
| Lavapipe, guarded reads enabled for smaller ranges | 41.73 → 23.28 µs | 6.04 → 3.92 ms |

Each table entry is the median of five process medians. Each process timed 21 flushes after six warmups, using the same eight host mappings and alternating test order. The fixtures verified every byte and checked that disjoint requests left the intervening bytes untouched. A separate RX 6800 pair on the exact Linux native PR head measured **6.80 → 4.53 ms** at 44 MiB and passed the same byte checks. That head also passed 46 regular GPU tests on Lavapipe. Open-world frame time and the effect on write-watched gameplay ranges remain unverified. [Upstream PR](https://github.com/spidercraft/VIctory/pull/30) · [Individual measurements](../../../data/benchmarks/gmankab/benchmark-logs.csv)
