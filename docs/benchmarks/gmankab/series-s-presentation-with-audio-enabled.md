# Series S presentation with audio enabled

**Run by** [@gmankab](https://github.com/gmankab)

The RX 6800 runs used Wine and RADV with PulseAudio available, the same checkpoint, and the same scripted input. Each captured the same 25 frames through frame 1,024 and reached its time cap without the startup breakpoint. The newest Fxceless baseline used the read-only stream probe.

| Build and configuration | Result |
|---|---|
| `ceb9e1f7`, 90 s | All 25 captures were black. Host draws and dispatches executed. |
| Published `d56316bb`, 90 s | 13 of 25 captures had visible content. Frame 200 showed the interface, and frame 1,024 showed the illustrated loading screen. |
| Published `d56316bb`, `VICTORY_PRESENT_PLANES=0`, 60 s | All 25 captures were black again when only the first present plane was shown. |

Toggling the compositor on the same build isolated the second plane as the source of the visible Series S loading screen in this test. The captured sequence ended during loading. The raw frame-rate samples in the run index cover startup and loading. [Related Fxceless report](https://github.com/fxceless/victory-fork/issues/425) · [Second-plane proposal](https://github.com/fxceless/victory-fork/pull/188)

## Second-plane proposal on `ceb9e1f7`

**Run by** [@gmankab](https://github.com/gmankab)

Six commits from the proposal were rebased onto `ceb9e1f7`, with a follow-up that carries the interface resource through queued presents. Wine runs used PulseAudio, RADV and the Series S checkpoint. The two runs of the port enabled asynchronous submission and presentation and used the same later Enter schedule. Their logs confirm the switches were enabled. The evidence for the compositor comes from captured frames, which use the synchronous readback path. [Tested branch](https://github.com/gmankab/victory/tree/contrib/fx-series-s-pr188-current)

| Build and capture source | Result |
|---|---|
| Published `d56316bb`, 200 s, original input schedule | The last capture at frame 2,400 showed loading artwork. Whole-card busy samples were **1% median and 25% peak** across 101 readings. System-wide VRAM use peaked at 7,985 MiB. |
| Rebased proposal, 180 s, first-plane readback | All 30 captures were black, although the host logged initialization of the overlay pass. |
| Rebased proposal with composed-buffer readback, 135 s | **26 of 28** captures were nonblack. The same 28 frame numbers in the first-plane run were all black. Frame 1,300 still showed loading. |

The port restored visible output during startup and illustrated loading. Both GPU readback tests ran on the RX 6800 and passed, as did the plane parser test. The recorded frame rates cover startup and loading under different configurations. [Upstream test report](https://github.com/fxceless/victory-fork/pull/188#issuecomment-7191270104)
