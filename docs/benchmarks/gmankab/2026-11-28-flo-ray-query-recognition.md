# November 28, 2026 · Flo ray-query recognition and EXEC selects

**Run by** [@gmankab](https://github.com/gmankab)

Flo's fork documented its flat ray-query traversal fixture falling back to software. I isolated the existing shader recognition checks from the Vulkan part of the fixture in local commit `a495569e`. The full fixture could not start on this Lavapipe build because the test harness requires fragment barycentrics and 64-bit LDS and image atomics. The CPU entry point let me compare compiler passes on Fedora Linux 45 while the title was using the RX 6800. [Original report](https://git.disroot.org/flo/kyty/commit/3c7137e02c964e6ad42f3d02803eb2012dc48ae1)

| Compiler setting | REA-observed recognition result |
| --- | --- |
| Flo `ed28f9b9`, default passes | Flat-loop assertion failed. Exit 134. |
| Same code with `KYTY_EXEC_SELECTS=0` | Passed the positive and rejection checks. Exit 0. |
| Local `16d1a8e2`, default passes | Passed the positive and rejection checks. Exit 0. |

The fix runs ray-query recognition while the loop-carried EXEC-masked selects are still visible. It then runs the existing select simplification before SPIR-V emission. The CPU fixture passed with the default setting and the emulator built in Toolbox with Kache.

## Radeon dispatch validation

REA captured the full `--bvh-mirror-only` fixture running on the RX 6800 with RADV. The standard harness stopped at its byte-granular robustness check. Setting the existing `KYTY_TEST_ALLOW_COARSE_ROBUST_BUFFER_ACCESS=1` option allowed the fixture to use RADV's four-byte granularity. BVH mirror formats **1, 3, and 10** passed, and the traversal fixture produced equivalent hits in both its **32-lane** and **64-lane** variants. The test exited with code 0.

Native ray-query coverage and frame-rate effects in the title remain unverified. The change lives on a local branch based on the Disroot fork.
