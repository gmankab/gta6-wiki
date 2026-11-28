# Linux native dynamic descriptor allocation

**Run by** [@gmankab](https://github.com/gmankab)

The cursor backport found all 41,472 expected results in a CPU harness that checked every occupancy pattern, cursor, and request size for a nine-slot heap. The original search missed 996 valid allocations. An RX 6800 host-heap test also verified that a free run can cross the search cursor and that separate slots at the physical ends of the heap do not form a run. [Original cursor fix](https://github.com/Marvisak/VIctory/pull/974)

For bursts of 4,096 failed requests against 65,536 occupied slots, the median of seven synthetic bursts fell from **668.45 ms** with the cursor fix alone to **0.256 ms** with failed searches cached. A release that joined free runs let a previously impossible request succeed immediately. Three focused checks passed on each of the RX 6800 and Lavapipe for the contribution and six passed per backend for the integration build. The integrated Linux native executable built successfully. Gameplay performance and GPU stability with these allocator changes remain unmeasured. [Failure-cache source](https://github.com/Marvisak/VIctory/pull/2394) · [Linux native contribution](https://github.com/spidercraft/VIctory/pull/38) · [Tested integration](https://github.com/gmankab/victory/tree/test/linux-native-descriptor-allocator)
