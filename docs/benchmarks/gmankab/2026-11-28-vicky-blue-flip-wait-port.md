# November 28, 2026 · VIcky Blue flip-wait port

**Run by** [@gmankab](https://github.com/gmankab)

I applied VIcky Blue's timed flip-wait behavior to the local Disroot baseline as `7babb002`. REA captured passing PM4 flip and flip-rate fixtures under an isolated Weston session. The 1080p title smoke test reached illustrated loading and the opening 3D cinematic on host RADV 26.2.6. The test enabled processing of queued GPU commands during pending flips. [Source PR](https://github.com/wololo-wololo/VIcky-Blue/pull/42)

REA counted **81 completed presentations over 39.94 seconds (2.03/s)** during the opening 3D sequence. The title averaged **20.58%** graphics-engine activity and **0.12%** compute-engine activity, with **149.15 µs** of command-thread CPU time per draw or dispatch. Separate builds ran during this smoke test. The result establishes that the port reached the cinematic and leaves its startup-speed effect unmeasured.

The title continued through the exterior opening credits. Camera and movement probes did not establish player control. A separate 24.1-second Tracy capture recorded **17,491 zero-copy read waits** and **20.06 seconds of summed exclusive thread time** in those wait zones. These waits can overlap across threads. The indirect-dispatch handler accounted for **3.08 seconds** of exclusive time in the same capture.
