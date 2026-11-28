# November 28, 2026 · Disroot Kyty resource-walk lookahead

**Run by** [@gmankab](https://github.com/gmankab)

I built Disroot Kyty revision `ed28f9b9` with Kache in Fedora Linux 45 Toolbox and ran it on the Radeon RX 6800. REA captured the guest-read-log and resource-materialization suites passing. Lookahead walks shader resources on a second CPU thread. The command thread can reuse a result when the recorded guest reads still match and the GPU owns none of those bytes. [Revision](https://git.disroot.org/flo/kyty/commit/ed28f9b9c615261792e190b5c4cc661b7b0d1174)

The driver accepted a **208.34 MB** pipeline cache. The seed's AnyVI `.recipes2` archive used a different format from this build's `.recipes` archive. The offline shader check found zero compatible modules, and the title compiled shaders during startup. REA measured an initial **127.70-second** presentation gap that recovered while the package remained readable.

I alternated lookahead after the first sustained 3D reports. The table uses complete five-second windows inside each setting and counts completed main-output flips.

| Time after title launch | Lookahead | Flips | Flips/s | Graphics engine | Accepted resolutions |
| --- | --- | ---: | ---: | ---: | ---: |
| 695–735 s | On | 14 | 0.35 | 5.27% | 10,764 of 40,068 |
| 745–780 s | Off | 6 | 0.17 | 4.36% | — |
| 790–830 s | On | 8 | 0.20 | 11.51% | 28,671 of 72,078 |
| 840–880 s | Off | 6 | 0.15 | 6.99% | — |

The command thread reused **26.9%** and **39.8%** of its lookahead lookups in the two enabled windows. Most misses had no prepared result for their inputs. Several compilation gaps occurred, draw counts varied, and low-priority builds overlapped parts of the trial. Those differences prevent attributing the frame-rate changes to lookahead.

REA also measured a later interval near **916 seconds** with **7 completed flips in 24.1 seconds** and **29.64%** graphics-engine activity. Lookahead stayed enabled after the comparison.

## Dawn and launcher interruption

The title reached further into the credits with its package reader still live. During a **60-second** late window it completed **47 flips** at **0.78 flips/s**, with a **2,833 ms** p95 interval and **43.50%** graphics-engine activity. Across an earlier 18.99-second controller check it completed **23 flips**, and REA matched all three alternating right-stick commands to events on the virtual device the emulator opened. The screenshots showed changing shots within the credits sequence, so they leave player camera control unverified.

The launch ended with an interruption marker after **1,556.69 seconds**. The last completed flip was about **0.86 seconds** earlier. All **104** package checks passed, with the final read **11.13 seconds** before the interruption. No guest exit record or mount failure marker was present. Controllable open-world gameplay at 30 FPS remains unverified.
