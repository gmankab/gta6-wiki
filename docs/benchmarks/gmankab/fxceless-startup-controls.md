# Fxceless startup controls

**Run by** [@gmankab](https://github.com/gmankab)

These runs used Wine with isolated profiles and the same read-only game directory. The scripted comparison reached different setup pages. In the paired idle runs, the published build and stock Fxceless hit the same guest breakpoint. The faulting instruction and the saved Windows exception context showed a stream writer on the title’s main thread trying to copy 1,728 bytes with 80 bytes remaining.

| Build and configuration | Result |
|---|---|
| Stock `06702fe8`, Series S, scripted setup, RADV | Same breakpoint after 41 s. |
| Stock `06702fe8`, Series S, no input, RADV | Same breakpoint after 24 s with Relay on and after 24 s with Relay off. |
| Published `d56316bb`, Series S, no input, RADV | Same breakpoint after 23 s. |
| Stock `06702fe8` with the known checkpoint, RADV | Same breakpoint in Series S and Series X. The Series X run exited after 26 s. The Series S harness was interrupted after recording the exception. |
| Stock `06702fe8`, Series S, no input or frame captures, RADV | Same breakpoint after 24 s, including the run with intro skipping disabled. |
| Stock `06702fe8`, Series S, no input, DRI hidden and Lavapipe selected | Same breakpoint after 23 s. The runtime found llvmpipe and failed to create its D3D12 host device. No host draws or dispatches ran. |
| `06702fe8` with a read-only crash probe, Series S, DRI hidden | Same breakpoint after 23 s. The 98,304-byte stream had reached byte 98,224. |
| `ceb9e1f7` with a read-only crash probe, DRI hidden | Same breakpoint after 24 s in Series S and 26 s in Series X. Both stopped at byte 98,224 with the same 41 consecutive copies of the pending write already present. |
| Same `ceb9e1f7` Series S probe, DRI hidden, PulseAudio available | No breakpoint before the 70 s cap with Wine's PulseAudio trace enabled or before the 60 s cap with logging matched to the failing control. Both reached logged frame 1,024. |

All five DRI-hidden probe runs executed zero host draws and dispatches. The 41 byte-identical copies were spaced exactly 1,728 bytes apart and filled the end of the occupied stream. The probe compared bytes in-process and logged only lengths, match counts and offsets.

The earlier headless harness denied the PulseAudio socket. Granting it under Wine allowed a real playback stream to start and prevented the breakpoint within both test windows. The 60-second repeat changed only the socket permission from the failing Series S configuration. The normal Flatpak manifest already grants this socket. The title's internal reason for the repeated writes remains open.
