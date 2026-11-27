# Published benchmarks

These measurements are attributed to the original projects. See the linked reports for their workloads and test conditions. [Benchmark index](../benchmarks.md)

## Windows fork source comparison

The project authors measured different scenes on different GPUs and console profiles.

| Reported by | Hardware and settings | Result and workload |
|---|---|---|
| [@readmet3xt-lab (Foxhound)](https://github.com/readmet3xt-lab/foxhound/tree/454ffead768aaf2fedab7e51f641523ce6f8a440) | Ryzen 7 7800X3D, RTX 4070 Super, Windows 11, Series X, 1440p DLSS Quality, frame generation off | **Steady 30 FPS** at the first boat ride, with stalls earlier in the run. |
| [@Belobug (VIctory-Belobug-AMD)](https://github.com/Belobug/VIctory-Belobug-AMD/tree/4eb6cfdf9e61cb112f1f811344ec0dd309eddd6c) | Core i9-12900K, RX 7900 XTX, Windows 11, ray tracing off | Series S city street at **44.9 ms/frame**, about **22 FPS**, compared with **57–75 ms/frame** before the listed patches. The README also reports about **19 FPS** in Series X. |

## Disroot Kyty image heap

[flo](https://git.disroot.org/flo) measured the effect of restricting image-heap retries to changed guest memory ranges. The maintainer’s 240-second RADV run used a world save and alternated full and range-aware discovery. It reported about **9.7% fewer command-thread instructions per draw or dispatch** with range-aware discovery. [Maintainer benchmark and methodology](https://git.disroot.org/flo/kyty/commit/d1b1ba83eff534f582dbdd15fc04e40b674c7087)
