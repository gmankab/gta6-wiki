# Native occlusion query measurements

**Run by** [@gmankab](https://github.com/gmankab)

A 4×4 draw fixture reproduced the title run’s overlapping-query warning on Lavapipe. The fixture used two title heaps and crossing counted and binary queries. Splitting the host measurement into intervals reduced the warnings from **three to zero** and preserved counted results **20 and 12** and binary results **1 and 1**. The native integration passed 63 regular GPU tests. The exact Spidercraft PR head passed the draw fixture and 44 regular GPU tests. Both ran with Vulkan validation and `/dev/dri` hidden. The native title’s reset and open-world frame rate still need separate verification. [Upstream patch and test](https://github.com/spidercraft/VIctory/pull/29) · [Test index](../../../data/benchmarks/gmankab/test-results.csv)
