# November 28, 2026 · Kyty 1080p presentation retest

**Run by** [@gmankab](https://github.com/gmankab)

Disroot Kyty `70f82dbb` includes a dedicated Vulkan copy queue for buffer readbacks. A local candidate also restricted the prepared frame to the resolution registered by the title. With host RADV 26.2.6, the 1920×1080 Continue screen still occupied the top-left **640×360** of the 1280×720 Wayland output. The extent change failed the title test. REA captured the readback batch, detached submission, and refused-import fixtures passing with Vulkan validation on Toolbox RADV 26.2.7. These fixtures exercise the readback and fallback behavior. [Disroot copy queue](https://git.disroot.org/flo/kyty/commit/70f82dbb504a73cfb06b869a4e9ec70ae7ca2196)

**Dedicated copy queue comparison** The title opened and read the imported story save during loading. In the first 3D cinematic, I switched the queue off, on, off, and on in the same process. REA summarized five complete five-second intervals per setting after each switch. No new pipelines were created in these intervals. Kyty's performance summary counts accepted guest flip requests when they enter the video-out queue.

| Sample | Dedicated queue | Flip requests/s | Draws and dispatches per flip request | Readback handler µs/call | Graphics engine | Compute engine |
|---|---|---:|---:|---:|---:|---:|
| 1 | Off | 2.56 | 1,730 | 397 | 17.74% | 0.00% |
| 2 | On | 2.68 | 1,894 | 463 | 16.19% | 2.82% |
| 3 | Off | 2.72 | 1,881 | 400 | 19.40% | 0.00% |
| 4 | On | 1.96 | 2,267 | 474 | 14.76% | 2.53% |

On the dedicated path, each readback took **296–308 µs** on average. With the queue off, the fused path took **413–433 µs** on average. The handler spent more time overall with the queue on, and the remaining readbacks that flushed the graphics queue took longer. The cinematic workload changed across samples, so the throughput effect remains unresolved.

**CPU write-fault window comparison** Later in the same run, I alternated the write-watch window between **64 KiB** and **256 KiB**, with the dedicated queue enabled. REA summarized four 25-second windows. Write faults per 10,000 draws and dispatches were **2,829 → 2,599 → 3,935 → 1,471**. Buffer uploads were **40.8 → 66.2 → 59.8 → 80.3 MB/s**. Summed fault-handler time across threads rose in both 256 KiB samples. Scene changes and shader compilation prevent attributing the differences in flip-request rate to the window size. The run returned to 64 KiB. A lower-priority build was running during both comparisons.

**Later opening scenes** Without a cinematic skip, the 1080p run issued **246 flip requests in 215 seconds (1.14/s)**. AMDGPU recorded **52.94%** graphics-engine activity and **1.17%** compute-engine activity over the corresponding 212-second sample. The command thread used **393.9 ms of CPU time per flip request**. The next 90-second window averaged **1.67 flip requests/s**, with **35.27%** graphics-engine activity over 87 seconds and **331.9 ms** of command-thread CPU time per flip request. Later Right Stick, Left Stick, and Start probes during the twilight sequence and scene transitions did not establish camera or movement control.
