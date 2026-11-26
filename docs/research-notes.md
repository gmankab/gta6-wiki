# Research notes, reviews and troubleshooting

## What the two opening sequences are

The eagle and bullets logo, about **0:05–0:20** in the reference, is a **15-second Bink 2 movie**. The local decode of `Stinger/RSG_STINGER.bk2` from `movies.rpf` matched the reference. The character artwork, about **0:21–0:59**, is **real-time 2D composition**. Across 248 traced frames, the same six separate textures were drawn and five shifted to create parallax. The early fan-made comparison clip was replaced with the game recording. [Logo segment](https://youtu.be/l4ukT5Ie-Go?t=5) · [Loading artwork segment](https://youtu.be/l4ukT5Ie-Go?t=21)

The Chasm skyline image matches a screenshot attributed to Hyphen, including **615.6 ms/frame** and **4,890 draws/frame**. The public lighting work was identified at `578d459`, which also underlies the AMD donor branch. The exact configuration used for the prison clip remains unknown. [Skyline post](https://x.com/chasmmmmmmmmmmm/status/2125300820748939714) · [Attribution](https://x.com/raul_bs3/status/2125302594277114333) · [Lighting commit](https://github.com/fxceless/victory-gta6/commit/578d459c344aa44753e9a61ea6e38df92f1f8d50986)

## Research on AI-assisted development

Project histories identify specific contributions. The table gives examples of the model or tool names that authors recorded in recent local Git history, plus repository-level agent instructions when present. A trailer is an attribution claim by its author. Project-wide prompts, private chat transcripts and model settings are generally unavailable from the repository.

| Repository | Public evidence in the inspected history |
|---|---|
| Victory | Commit trailers mention Claude Fable 5.2, Claude Opus 5.5 and GPT-6.2 Sol. This wiki’s local integration work used `Assisted-by: gpt-6.2 sol xhigh` where recorded. |
| cool_fox | Original Git history includes Claude Opus 5.5 co-authorship. |
| Leonida | `AGENTS.md` gives coding-agent instructions. Trailers mention Claude Fable 5.2 and Claude Opus 5.5. |
| XCL | `AGENTS.md` and `CLAUDE.md` discuss agent workflows and reporting AI use in PRs. Trailers include Codex/GPT-6, GPT-6 Astra, Claude Fable 5.2 and Claude Opus 5.5. |
| TuxBox | Recent history includes Claude Opus 5.5 co-authorship. |
| AnyXboxX | `AGENTS.md` and `CLAUDE.md` document contributor rules. Trailers include Claude Fable, Haiku and Opus. |
| OpenScarlett | `AGENTS.md` describes build and workspace rules. Trailers include Claude Opus and Copilot. |
| Palmwave | Agent instruction files and trailers identify Claude Code, Codex and Antigravity. The model names reported include Claude, GPT-5 and Gemini. |

The visible evidence describes tasks such as compiler fixes, compatibility testing, measurement and original runtime contributions. It does not reveal why any private LLM interaction produced a particular response. This wiki’s evidence comes from those recorded contributions, local test results and repository instructions.

## Technical review of the project

The November 25 transcript-based review identified Fxceless’s Wine integration as the strongest local title path and Leonida as a parallel native-Vulkan research track. Its early native conclusion covered the inspection-only Linux CLI available at the time. The later Spidercraft-based experiments verified actual Wine-free execution, menus, loading and some 3D submissions. Those milestones still leave visible, controllable native gameplay to verify.

The review’s main bottlenecks were guest-to-host memory copies, CPU–GPU synchronization, incorrect descriptor bindings and shader translation. The Series S presentation work and shader corrections later moved several of those cases forward.

- **Memory coherence.** Xbox-style shared memory semantics led to repeated uploads and readbacks on a discrete GPU. The review called out resource ownership, persistent allocations, dirty ranges, queue overlap and alias-aware synchronization. A recorded heavy frame uploaded about 322 MB and read back about 149 MB. Changing fences or queues has to preserve which view of memory the guest and renderer see.
- **Descriptors.** Copied guest records can bypass descriptor-creation calls. Fxceless’s existing `a26859f` already mirrored this case in the local build, so the separate PR was not reapplied. The review’s upstream example described about 40,000 directly written descriptors per frame and a reduction from roughly 550–710 MB scanned to 25 MB. These figures were an upstream design lead. [Fxceless change](https://github.com/fxceless/victory-fork/commit/a26859f) · [Descriptor PR](https://github.com/fxceless/victory-fork/pull/305)
- **Shader correctness.** A large ray-shadow pipeline exhausted stack in the translation stack. Loop-latch normalization and descriptor-address work later compiled and validated all six sampled variants on RX 6800. Branch-selected descriptors, SSA joins, divergent Wave32/Wave64 semantics and structured storage views remained important gaps. [Loop-latch PR](https://github.com/fxceless/victory-fork/pull/737) · [Descriptor address PR](https://github.com/fxceless/victory-fork/pull/738) · [Nested descriptor PR](https://github.com/fxceless/victory-fork/pull/739)
- **PSO and presentation.** Warm the cache with the same GPU capability data as runtime, distinguish cache misses from unavoidable compilation and measure wait time alongside actual GPU work. The warm-up correction reused all five sampled entries on a second pass. The runtime’s RDNA translation cache, vkd3d-proton’s DXIL-to-SPIR-V cache, and Mesa’s Vulkan pipeline or native-code cache are distinct. Series S also needed the display planes composed and scaled correctly. The first explicit root-signature candidate `16a34e6f` passed three compositor tests and detected two display planes. [Warm-up PR](https://github.com/fxceless/victory-fork/pull/813) · [Series S PR](https://github.com/spidercraft/VIctory/pull/23)
- **Benchmark design.** The review called for same-scene comparisons that capture median, P95 and P99 frame times, GPU and CPU submission durations, pipeline waits, uploaded and read-back bytes, dropped operations, memory residency and device losses. A useful sequence is correct rendered output first, then stable 5, 10, 20 and 30 FPS under player control.

Other projects were surveyed through PortWatch and upstream Git history for descriptor residency, parallel translation and asynchronous scheduling. The downloaded 7900 XTX review pointed to deferred descriptor work, fewer queue waits, multithreaded command translation, batching, memory transfer reductions, write interception and GPU-side buffer hashing. Its published title results came from a different host configuration. The local Linux tests of the fork and its Proton PR are in the project table. [Tested projects](projects.md) [PortWatch](https://portwatch-vi.xyz/#top) · [7900 XTX project](https://github.com/avatarmanz/VIctory-7900XTX)

The related engineering references were LLVM’s AMDGPU work, DXIL/SPIR-V control-flow translation, vkd3d-proton’s graphics tests, public Xbox API samples and other AMD console compatibility projects. [LLVM](https://github.com/llvm/llvm-project) · [dxil-spirv](https://github.com/HansKristian-Work/dxil-spirv) · [vkd3d-proton](https://github.com/HansKristian-Work/vkd3d-proton) · [DXC](https://github.com/microsoft/DirectXShaderCompiler) · [Xbox GDK samples](https://github.com/microsoft/Xbox-GDK-Samples) · [shadPS4](https://github.com/shadps4-emu/shadPS4)

## Native host and REA

REA inspected the original title PE, checked native binaries and related crash frames to API calls. One title inventory recorded 587 import entries over 68 DLL groups. REA resolved a Wine wait frame to `NtWaitForSingleObject`, confirmed XCL stopped in `CreateCommandListX` with `E_INVALIDARG`, and identified 30 Windows DLL imports in a Leonida launcher. The Wine-free Victory ELF verification plus a real native title launch established actual runtime coverage. The existing x86-64 guest instructions already execute on the host CPU, so reducing graphics translation, synchronization and copy costs is the performance path evaluated here. A full game-source decompilation was not performed.

## Host incidents and resource use

- **Temporary storage pressure.** Flatpak’s game `/tmp` used the user runtime tmpfs in the affected launch. Deleted Wine `tmpmap` files still held open by processes occupied **4.65 GiB** on a **4.7 GiB** tmpfs. Ending the test instance released the mappings and usage fell to **424 KiB**. Later tests used a private larger tmpfs for transient Wine mappings.
- **SSD pressure.** A cleanup recovered about **20 GiB**. It moved older captures, a Leonida shader comparison archive and an old vkd3d checkout to a test archive. Removing 26 unused Git worktrees freed another **75 MiB**, with branches and detached snapshots preserved. Original game contents, torrents and unrelated Lineage data stayed where they were.
- **AMDGPU and Niri.** Some runs ended with a successful per-queue recovery and the game lost its graphics device while Niri kept running. In the desktop-crash incident, the queue reset failed, a whole-device reset followed, and Mesa aborted Niri’s rendering thread after the context was lost. PCIe errors appeared around the event. The kernel named another process on one timed-out queue, so the underlying trigger remains unresolved. [Linux PCIe AER](https://docs.kernel.org/PCI/pcieaer-howto.html)
- **Recovery.** The local systemd recovery test verified restart behavior with a disposable service. Niri was configured for a two-second retry and Codex for reopening the saved session in a terminal. The ordinary queue-timeout replays left both processes alive. That was a different event from a desktop-wide reset, so automatic recovery from the full event was not confirmed.
- **Reference validity.** GitHub’s public DMCA notice named a Leonida network. The tested public `ubiquitarianism/leonida` project was checked as a separate accessible repository. Refer to [tested repositories](projects.md) for actual refs and the Disroot mirror. [Public notice](https://github.com/github/dmca/blob/master/2026/11/2026-11-24-take-two.md)

## Record provenance

This page incorporates the local transcript review, the downloaded 7900 XTX analysis, the native timing note, saved project-session findings and the retained test archive. Historical conclusions are dated by the evidence they relied on. Where a timed run ended early or a diagnostic changed the picture, its result is recorded with that limit. The public data files contain technical observations and test summaries.
