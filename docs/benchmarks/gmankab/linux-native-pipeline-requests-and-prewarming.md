# Linux native pipeline requests and prewarming

**Run by** [@gmankab](https://github.com/gmankab)

A synthetic compute pipeline stayed queued until its draw's wait ended. The runtime recorded it for prewarming on the next launch. The stand-alone contribution passed all 44 regular GPU tests on the RX 6800 and Lavapipe. The integration passed all 75 on both backends. [Linux native PR](https://github.com/spidercraft/VIctory/pull/46) · [Tested integration](https://github.com/gmankab/victory/tree/test/linux-native-remember-requested-pipelines)

The first 1080p Series S run on Lavapipe started with 292 pipeline keys and saved 1,420. All 1,128 additions were requested by the title, and 1,119 of them never became available to a draw or dispatch during the recorded run. The process exited after 421 seconds on a guest null write. The last captured frame was 1,300. The last image showed the illustrated loading screen.

The repeat used the same executable, Vulkan driver build, console profile, and original cache files with just the learned pipeline list substituted. It built or loaded **658 of the added keys**. Both added keys requested before the cap had finished before their first request. Frame 1,300 was captured at **193 seconds**, versus **354 seconds** in the first run. The repeat reached frame **1,750** and hit its **450-second** cap. Its last capture still showed the loading artwork. The runs took place at different times and used relocated tool paths in the repeat. Gameplay frame time and player control remain unverified.

Fxceless's queue policy moves foreground requests ahead of prewarm work. The Linux native regression checked order, promotion, and that the workers claim a promoted job only once. All 44 regular GPU tests passed on each backend on the stand-alone contribution. The integration built a Linux native executable and passed 76 regular GPU tests per backend. [Tested integration](https://github.com/gmankab/victory/tree/test/linux-native-prioritize-requested-pipelines) · [Linux native PR](https://github.com/spidercraft/VIctory/pull/47) · [Fxceless source](https://github.com/fxceless/victory-fork/commit/578d459c344aa44753e9a61ea6ba10a8c68f6ea6)

Both title runs seeded with the learned list timed out at 450 seconds. The previous run reached frame 1,750 and the priority build reached frame 1,900. The last images showed the same loading artwork. The nine-draw loading phase had a median of **2.3 FPS** in each run, and each built or loaded **830 unique pipeline keys**. The priority change's effect on gameplay frame time remains unverified.
