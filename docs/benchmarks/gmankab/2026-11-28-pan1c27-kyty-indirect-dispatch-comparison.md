# November 28, 2026 · PaN1C27 Kyty indirect-dispatch comparison

**Run by** [@gmankab](https://github.com/gmankab)

I built the fork with the tested Intel loader changes and Disroot's 1080p option. The CPU-extension fixture passed after adding the existing CLZERO trap fallback. The native-indirect fixture passed on the RX 6800 with the test harness opted into RADV's four-byte robust bounds. REA captured both results. The 1080p title run initialized Vulkan and stopped **42 seconds** after launch, before Continue. The log showed missing AGC, VideoOut, and AMPR imports, followed by a guest write fault. The unresolved AMPR import was the operation that backs PRT reservations. [Fork revision](https://github.com/PaN1C27/KytyPS5/commit/d36709870f1d8b85fd8e116ee2b1aab7b7108a21) · [AMPR implementation in Kyty](https://github.com/KytyPS5/KytyPS5/commit/c9122a1cfdb8326dd2858ceca27e9a84550744ad)
