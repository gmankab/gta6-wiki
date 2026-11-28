# @gmankab benchmarks

Run by [@gmankab](https://github.com/gmankab) on the machine below. Each result identifies its renderer and workload where recorded. The tests cover the Xbox and PS5 builds. [Benchmark index](../../benchmarks.md)

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

## Reports

- [PS5 KytyPS5 startup](ps5-kytyps5-startup.md)
- [November 28, 2026 · Molier Kyty 3D startup](2026-11-28-molier-kyty-3d-startup.md)
- [November 28, 2026 · Kyty CLZERO and image-heap profile](2026-11-28-kyty-clzero-and-image-heap-profile.md)
- [November 28, 2026 · Disroot Kyty with the shader loop limit](2026-11-28-disroot-kyty-with-the-shader-loop-limit.md)
- [November 28, 2026 · Kyty 3D street capture and shader cache verification](2026-11-28-kyty-3d-street-capture-and-shader-cache-verification.md)
- [November 28, 2026 · Kyty zero-copy import failures on the RX 6800](2026-11-28-kyty-zero-copy-import-failures-on-the-rx-6800.md)
- [November 28, 2026 · Kyty with 1080p video-out and a failed-import fallback](2026-11-28-kyty-with-1080p-video-out-and-a-failed-import-fallback.md)
- [November 28, 2026 · Kyty 1080p presentation retest](2026-11-28-kyty-1080p-presentation-retest.md)
- [November 28, 2026 · Vulkan guest memory on the RX 6800](2026-11-28-vulkan-guest-memory-on-the-rx-6800.md)
- [November 28, 2026 · Disroot Kyty writer-thread fence waits](2026-11-28-disroot-kyty-writer-thread-fence-waits.md)
- [November 28, 2026 · Disroot Kyty Vulkan recorder](2026-11-28-disroot-kyty-vulkan-recorder.md)
- [PS5 AnyPS5 startup cache](ps5-anyps5-startup-cache.md)
- [November 28, 2026 · AnyPS5 Intel conversion and packed-color retest](2026-11-28-anyps5-intel-conversion-and-packed-color-retest.md)
- [November 28, 2026 · AnyKyty Intel relinker](2026-11-28-anykyty-intel-relinker.md)
- [November 28, 2026 · VIcky Blue flip-wait port](2026-11-28-vicky-blue-flip-wait-port.md)
- [November 28, 2026 · Later PS5 branch builds](2026-11-28-later-ps5-branch-builds.md)
- [November 28, 2026 · VIcky Blue shader compile-time branch](2026-11-28-vicky-blue-shader-compile-time-branch.md)
- [November 28, 2026 · Frozzy AnyVI outdoor cinematic](2026-11-28-frozzy-anyvi-outdoor-cinematic.md)
- [November 28, 2026 · AnyVI software BVH4 bounds decoding](2026-11-28-anyvi-software-bvh4-bounds.md)
- [November 28, 2026 · AnyVI indirect shared-memory readback mirror](2026-11-28-anyvi-indirect-lds-readback-mirror.md)
- [November 28, 2026 · AnyVI with package liveness checks](2026-11-28-anyvi-package-liveness.md)
- [November 28, 2026 · PaN1C27 Kyty indirect-dispatch comparison](2026-11-28-pan1c27-kyty-indirect-dispatch-comparison.md)
- [Title runs](title-runs.md)
- [Linux build checks for Foxhound and Belobug AMD](linux-build-checks-for-foxhound-and-belobug-amd.md)
- [Cool Fox under the CGBJ reupload name](cool-fox-under-the-cgbj-reupload-name.md)
- [Native compute shader validation](native-compute-shader-validation.md)
- [Native occlusion query measurements](native-occlusion-query-measurements.md)
- [Linux native descriptor-buffer checks](linux-native-descriptor-buffer-checks.md)
- [Linux native offline pipeline preparation](linux-native-offline-pipeline-preparation.md)
- [Linux native write-watch bitmap scans](linux-native-write-watch-bitmap-scans.md)
- [Linux native sparse guest copies](linux-native-sparse-guest-copies.md)
- [Linux native repeated GPU writes](linux-native-repeated-gpu-writes.md)
- [Linux native overlapping GPU writebacks](linux-native-overlapping-gpu-writebacks.md)
- [Linux native pipeline requests and prewarming](linux-native-pipeline-requests-and-prewarming.md)
- [Linux native dynamic descriptor exhaustion](linux-native-dynamic-descriptor-exhaustion.md)
- [Linux native guarded DirectStorage reads](linux-native-guarded-directstorage-reads.md)
- [Linux native dynamic descriptor allocation](linux-native-dynamic-descriptor-allocation.md)
- [Linux native descriptor sharing across graphics stages](linux-native-descriptor-sharing-across-graphics-stages.md)
- [Linux native missing shader pipelines](linux-native-missing-shader-pipelines.md)
- [Linux native host heap import](linux-native-host-heap-import.md)
- [XCL startup preparation under Wine](xcl-startup-preparation-under-wine.md)
- [Linux native parallel shadow refreshes](linux-native-parallel-shadow-refreshes.md)
- [Fxceless startup controls](fxceless-startup-controls.md)
- [Series S presentation with audio enabled](series-s-presentation-with-audio-enabled.md)
- [Isolated measurements and build times](isolated-measurements-and-build-times.md)

## Evidence index

- [Run observations](../../../data/benchmarks/gmankab/runs.csv) extract timestamps, exit codes and explicit frame/FPS counters from retained logs. An exit of 124 records a harness timeout. A missing exit means the available log had no exit sidecar.
- [Frame timings](../../../data/benchmarks/gmankab/frame-timings.csv) contain the retained runtime timing CSV rows. Read rows in the context of the test configuration and stage.
- [Benchmark log measurements](../../../data/benchmarks/gmankab/benchmark-logs.csv) index timing lines in saved benchmark output. They include trials and repeats. Labels and units use the source file’s format.
- [Test results](../../../data/benchmarks/gmankab/test-results.csv) index suite summaries. Multiple rows can come from repeated runs of the same suite.
- Filenames in the index identify local evidence. Figures reported only in historical replies or reviews appear in the tables in these reports. Captures and title assets are kept out of the public repository.
