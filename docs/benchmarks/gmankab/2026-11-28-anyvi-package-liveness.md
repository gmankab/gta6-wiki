# November 28, 2026 · AnyVI with package liveness checks

**Run by** [@gmankab](https://github.com/gmankab)

The [previous run](2026-11-28-anyvi-indirect-lds-readback-mirror.md#follow-up-through-the-credits) kept rendering after the read-only package view disconnected. I ran the same local AnyVI binary at `5d8206a1` on the RX 6800 in Fedora Linux 45, with the launcher and package reader in a bounded process scope. Every 15 seconds the launcher verified the reader PID and read one byte from a rotating offset in the executable. A synthetic reader exit verified the disconnect marker and the stop signal to the test launcher.

The copied toggle file contained `rb_mirror=1`. The launcher omitted `KYTY_TOGGLE_FILE`, so the emulator used its default mirror-off mode. The launcher now activates the toggle file when the seed provides one. The mirror-off result is useful for diagnosing this run's resource costs.

| Time after launch | Stage | Completed flips | Flips/s | p95 interval | Graphics engine |
| --- | --- | ---: | ---: | ---: | ---: |
| 270–330 s | Intro | 97 | 1.62 | 650 ms | 0.40% |
| 335–390 s | Loading | 1,630 | 29.64 | 33.7 ms | 2.27% |
| 477.5–501.5 s | First 3D trace | 21 | 0.88 | 2,433 ms | 10.82% |
| 650–710 s | 3D exterior | 63 | 1.05 | 1,633 ms | 28.61% |
| 836.7–860.8 s | Later 3D trace | 23 | 0.95 | 1,917 ms | 46.58% |

During the loading window the package decoder delivered **1.49 GB** across the 54 seconds sampled. It delivered **321.88 MB** across the 21 seconds sampled in the later 3D trace. The package view was readable throughout these windows. A low-priority build ran alongside the title from about 224 to 641 seconds.

REA analyzed both Tracy captures. In the first, five Vulkan compute pipeline creations took **17.28 seconds** in total across the recorded zones. In the later capture, no compute pipeline creation zone ran. Buffer readbacks took **17.10 seconds** across **12,350** caller visits, including **10.64 seconds** within GPU readbacks. The scheduler wait zone accounted for **5.33 seconds**. These zones overlap.

Alternating right and left stick input during the 3D exterior left shared camera landmarks fixed while new scenery streamed in. Player control and stable 30 FPS open-world gameplay remain unverified.
