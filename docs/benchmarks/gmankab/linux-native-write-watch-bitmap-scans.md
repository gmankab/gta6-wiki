# Linux native write-watch bitmap scans

**Run by** [@gmankab](https://github.com/gmankab)

In the heavier RX 6800 loading sample, Victory requested one 174 MiB shadow range 31 times per frame. The range took 93.48 ms in refresh calls. Those calls include tracking, copies, and other shadow work. The Linux native loader patch walks one bitmap word at a time for private memory. Physical aliases keep their group-aware checks. [Loading run](../../../data/benchmarks/gmankab/runs.csv) · [Loader PR](https://github.com/spidercraft/VIctory/pull/32)

| 174 MiB private range | Baseline | Candidate |
|---|---:|---:|
| All blocks clean | 11.03 µs | 0.158 µs |
| Four blocks opened and re-armed | 22.32 µs | 11.21 µs |

Each result is the median of seven paired process medians from the integrated release build. The fixture verified the exact dirty block list. The integration passed 44 loader tests, and the contribution head passed 35, including the partial-word boundary regression. The measured timings cover the guard API and its protection calls. Open-world frame timing remains unverified. [Individual measurements](../../../data/benchmarks/gmankab/benchmark-logs.csv)
