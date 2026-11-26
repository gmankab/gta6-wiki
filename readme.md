# GTA VI on AMD Linux

This wiki records local compatibility and performance experiments with GTA VI on an AMD GPU under Linux. It covers both a native Linux host that runs without Wine and Linux launches through Wine or Proton, including the Wayland Flatpak. NVIDIA hardware and Windows as a host OS are out of scope. A Windows-target runtime tested through Wine on Linux is within scope.

## Test machine

| Component | Configuration |
|---|---|
| Operating system | Fedora Linux 45 Silverblue, deployment 45.20261112.0 |
| Kernel | 7.3.3-300.fc45.x86_64 at the November 26, 2026 snapshot |
| CPU | Intel Xeon E5-2670 v3, 12 cores and 24 threads, 2.30 GHz base |
| GPU | AMD Radeon RX 6800, Navi 21, 16 GiB VRAM, AMDGPU kernel driver |
| Memory | 46 GiB reported usable by Linux |
| Desktop | Niri 26.04 on Wayland |
| Graphics | RADV for hardware tests. The Fedora 45 Toolbox snapshot had Mesa 26.2.7. Some software runs used a local Mesa 26.2.6 Lavapipe build. |
| Wine | Stock Proton Experimental, Wine 11.0 and GE-Proton were used in different runs. Each comparison identifies its configuration when recorded. |

## Current results

- **Wine on the RX 6800.** A Victory integration build verified player movement, camera control, pause and resume, dialogue, and progression through part of the prison prologue. Geometry and lighting were incomplete, and heavier scenes took seconds per frame.
- **Wine-free Linux on the RX 6800.** The native Victory ELF displayed the menus at about 30 FPS and entered heavy 3D rendering. One run reported about 0.5 FPS before an AMDGPU queue timeout.
- **Wine-free Linux on Lavapipe.** The native host reached illustrated loading and submitted early world work. An instrumented run traced the repeated zero-range Vulkan validation errors to empty constant-buffer views.
- **Open-world status.** Stable 30 FPS in controllable free roam remains unverified. The 30 FPS startup, menu and loading reports belong to those specific stages.

## Pages

- [Tested projects, branches and mirrors](docs/projects.md)
- [GTAHub forks and branch snapshot](docs/gtahub.md)
- [Benchmarks and how to read them](docs/benchmarks.md)
- [Victory experiment log](docs/victory-experiments.md)
- [Build and launch notes](docs/build-and-run.md)
- [Reviews, research and troubleshooting](docs/research-notes.md)
- [Upstream changes and reports](docs/upstream.md)
- [Recorded runs](data/runs.csv), [test summaries](data/test-results.csv), [saved benchmark measurements](data/benchmark-logs.csv), [frame timings](data/frame-timings.csv) and [branch snapshot](data/branches.csv)

The test input was the Xbox build already available locally. A reproducible extraction from the owner’s console was unavailable when these tests were run. The wiki publishes observations and source-code references. The local tests used read-only game mounts and isolated runtime state.
