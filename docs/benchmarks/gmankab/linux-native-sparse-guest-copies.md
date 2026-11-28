# Linux native sparse guest copies

**Run by** [@gmankab](https://github.com/gmankab)

The heavier loading sample reported 912 MB per frame of guest bytes that could not be read by copy probes. The loader now lets the copy fallback zero a whole known unreadable run after a failed read. Ranges outside the loader's table still use the existing page probes. [Loading run](../../../data/benchmarks/gmankab/runs.csv) · [Sparse-copy PR](https://github.com/spidercraft/VIctory/pull/33)

A 174 MiB fixture with 87 MiB mapped and 87 MiB reserved measured **78.95 → 29.20 ms** per copy across seven paired process medians. The contribution head measured **28.37 ms** in its own fixture and passed six regular host tests. The integration passed 13 regular host tests. Both verified the bytes after a hole that ended inside a 64 KiB copy piece. The integration also verified both hole cases with guarded reads enabled. These timings measure stand-alone CPU copies. [Individual measurements](../../../data/benchmarks/gmankab/benchmark-logs.csv)

A Linux native build with both changes presented its first frame after **4.21 seconds** on Lavapipe, with hardware render devices hidden. Its five-minute run reached capture **512**, which showed the illustrated loading screen. The 47 reports had a median of **0.4 FPS** and at most 11 draws per frame. Vulkan validation flagged a vertex shader using a subgroup operation that the software adapter did not list for that stage. The run ended at the time cap. [Run index](../../../data/benchmarks/gmankab/runs.csv)
