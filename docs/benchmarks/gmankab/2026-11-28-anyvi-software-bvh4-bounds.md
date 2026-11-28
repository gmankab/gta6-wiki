# November 28, 2026 · AnyVI software BVH4 bounds decoding

**Run by** [@gmankab](https://github.com/gmankab)

REA matched all seven cached shader variants of the six slow AnyVI compute pipelines to the software four-child BVH helper by comparing their SPIR-V function signatures. The diagnostic credits run attributed **5.51 of 5.94 seconds** of indirect-dispatch intervals to those pipelines in a 15-second window. [Outdoor cinematic benchmark](2026-11-28-frozzy-anyvi-outdoor-cinematic.md)

I tested a local patch that selects the FP16 or FP32 bounds layout once for each node instead of once for each of its four children. The translation continues to check access to the full-width node's second block and uses the same intersection, child ordering, and depth-growth rules. The baseline was local revision `600790ee`, based on Frozzy's `anyvi` branch, and the patched build was `7a39d40b`. [AnyVI source revision](https://git.disroot.org/frozzy/kyty/commit/eed0c578eab39717030a9890cc0d3b0220b6e8c5)

**Synthetic dispatches** Both builds passed all **28** cases from `shader_recompiler_compute_tests --ray-tracing-only` on Fedora Linux 45 with the Radeon RX 6800 and RADV 26.2.6. I warmed both Mesa caches, then ran **12 paired repetitions**, alternating which build went first. Each repetition ran all 28 cases. I compared the GPU timestamps for the six ending BVH4 dispatches. Their combined median fell from **67.64 µs** to **59.86 µs**, an **11.5% reduction**. Every patched repetition was faster than its paired baseline. [Per-run timings](../../../data/benchmarks/gmankab/2026-11-28-bvh4-dispatch-pairs.csv) · [Mesa timing documentation](https://docs.mesa3d.org/u_trace.html)

RADV compiled the final barycentric sorted fixture from 1,428 to 1,375 machine instructions and from 53 to 41 branches. The shader still used 64 VGPRs and reported zero spills. The measurements cover synthetic GPU dispatches. The effect on in-game frame times and controllable play remains unmeasured.
