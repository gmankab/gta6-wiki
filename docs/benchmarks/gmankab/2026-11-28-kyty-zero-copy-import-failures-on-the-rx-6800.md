# November 28, 2026 · Kyty zero-copy import failures on the RX 6800

**Run by** [@gmankab](https://github.com/gmankab)

**Build and setup** Disroot Kyty `ee39d796` with the compressed mip fix and the reviewed streaming-store `CLZERO` patch. The 1280×720 Wayland session ran host RADV 26.2.6. Kyty reported a 4K output and the title registered two 3840×2160 buffers. Zero-copy guest-memory promotion was enabled. [Disroot revision](https://git.disroot.org/flo/kyty/commit/ee39d796f9e5e826e46eafdbda5062cb3e2d2ee4) · [Zero-copy implementation](https://git.disroot.org/flo/kyty/commit/a89247f3dcd0f7ed72194c01eea527e6f92c3894) · [CPU patch](https://github.com/KytyPS5/KytyPS5/pull/2599)

**Result** REA counted **3,973** reported import refusals and zero imports in the observed log prefix. The most frequently refused range accounted for **2,505** attempts. The opening cinematic issued **222 flip requests over 105 seconds (2.11/s)** and used **18.58%** of the graphics engine over 103.1 seconds. After the title read the imported save at about **389 seconds**, a subsequent 3D sample recorded **69 flip requests in 60 seconds (1.15/s)** and used **26.08%** of the graphics engine over 59.1 seconds. The command thread used **87.28%** and **80.93%** of one CPU core in those windows. This run used a different executable and settings from the earlier guarded-shader run.

The Linux kernel killed the emulator during a global out-of-memory event about **862 seconds** after launch. The last sampled 3D window recorded seven flip requests in five seconds. The run had reached the credits and additional 3D scenes. Camera and locomotion remained unverified.
