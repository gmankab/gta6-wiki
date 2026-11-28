# November 28, 2026 · Molier Kyty 3D startup

**Run by** [@gmankab](https://github.com/gmankab)

**Build and setup** Molier `bringup` at `b4957bc7`, built in Fedora 45 Toolbox. The RX 6800 ran the title through RADV 26.2.6 in a private 1280×720 Wayland session. The fork opened its own empty shader store. [Tested revision](https://github.com/Molier/KytyPS5/tree/b4957bc741631dcad798236482f14530444d0088)

**Result** The Continue prompt and illustrated loading screen rendered at about 30 FPS. The fork then recorded at least **30 seconds without a frame** while processing 3D work, with a **5.27-second** compute-pipeline build among the reported waits. The emulator logged a fatal exception at about 320 seconds. REA independently decoded the failing guest instruction as a direct write to address zero. The fork already provides the VideoOut import mentioned in one report for this crash address. The logs establish a stall and subsequent guest abort, with the condition that triggered the abort still unverified. [Related Kyty compatibility report](https://github.com/KytyPS5/KytyPS5/issues/2571)
