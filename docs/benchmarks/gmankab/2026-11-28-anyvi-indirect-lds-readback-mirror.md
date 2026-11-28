# November 28, 2026 · AnyVI indirect shared-memory readback mirror

**Run by** [@gmankab](https://github.com/gmankab)

I tested local revision `5d8206a1` on the Radeon RX 6800 in Fedora Linux 45 with RADV 26.2.6 and 1080p guest output. The change tries AnyVI's existing readback mirror when it needs indirect workgroup counts to size storage-backed local data share (LDS). If the mirror lacks the current counts, the renderer uses its synchronous readback path. REA captured the native indirect dispatch fixture passing with the mirror on and off. The readback batch and wait suites also passed. This run reuses the shader and driver caches from the [warm BVH4 benchmark](2026-11-28-anyvi-software-bvh4-bounds.md#warm-ps5-run-with-named-dispatches).

During the 3D exterior sequence, I alternated the mirror setting twice in the same game process. The table uses complete reporting windows after each toggle settled. Completed flips are the guest's main-output presentations. Argument wait time is summed across wait calls and divided by the number of accepted flip requests.

| Launch time | Mirror | Completed flips | Flips/s | p95 interval | Argument wait per request | Graphics engine |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| 570–600 s | On | 69 | 2.30 | 733 ms | 5.22 ms | 24.73% |
| 610–640 s | Off | 47 | 1.57 | 1,617 ms | 19.78 ms | 25.93% |
| 650–680 s | On | 23 | 0.77 | 2,316 ms | 42.33 ms | 50.45% |
| 690–720 s | Off | 22 | 0.73 | 2,650 ms | 43.47 ms | 45.76% |

The mirror served **180,309 reads** in the first enabled window and **31,725** in the second. Those counters include shader and other renderer reads. The first pair shows the larger difference in both wait time and completed flips. Camera shots and dispatch counts changed between windows, and the experiment toggles the whole mirror, so it leaves the isolated game frame-rate effect of the new call site unresolved. I restored the mirror for the ongoing run. These samples cover the cinematic sequence, and stable open-world performance remains unverified.
