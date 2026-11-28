# November 28, 2026 · Disroot Kyty with the shader loop limit

**Run by** [@gmankab](https://github.com/gmankab)

**Build and setup** Disroot Kyty `d054552d` with the compressed mip fix and the ordinary-store `CLZERO` change, using host RADV 26.2.6. Kyty loaded 3,336 modules and 3,036 pipeline entries from a cache rebuilt for the new default shader loop limit. Linux reported a read-only package mount. [Disroot revision](https://git.disroot.org/flo/kyty/commit/d054552ded4f5a4e26b5c5a83b507d1838925fd6) · [Shader loop limit](https://git.disroot.org/flo/kyty/commit/17b25950d3d1a6144174ec2e2922499b5603c48f)

**Result** One stretch of the street cinematic presented **605 guest flips in 155 seconds (3.90 FPS)**. AMDGPU recorded **33.50%** graphics-engine activity over the corresponding 154.3-second hardware window. The graphics command thread averaged **78.4% of one CPU core**. The imported save opened and was read about **503 seconds** after launch. The street lacked most solid geometry and the scene did not respond with visible camera or locomotion changes during the stick tests. A later close-up cinematic presented **77 flips in 40 seconds (1.93 FPS)** with **30.31%** graphics-engine activity over 41.1 seconds.

**Capture and profile** The four-frame recording completed during illustrated loading, before the first visible 3D sequence. A later 18-second instruction profile of the street cinematic collected 1,017 samples. REA captured a report showing **1.86% of weighted instructions** on one line of the full image-heap scan. The scan's earlier 24.20% result covers a different scene and build.

**Loading replay** REA captured runs in baseline, indexed, indexed, baseline order, with three passes per run. All 12 passes completed four flips and four submissions with zero pacing timeouts. All four final PNGs matched byte for byte. The warm passes took **0.518–0.542 s** on the baseline and **0.513–0.524 s** with the index. These ranges overlap, and the capture covers illustrated loading.
