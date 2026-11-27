# GTA VI compatibility benchmarks on AMD

This wiki records GTA VI compatibility and performance experiments on AMD hardware. The main tracks are Linux native and Wine or Proton, including the Wayland Flatpak. Windows results from the Radeon RX 7800 XT have their own contributor page. NVIDIA results appear only in attributed reports from other projects. [Benchmark index](docs/benchmarks.md)

## Testers

| Tester | OS | GPU | CPU | RAM |
|---|---|---|---|---|
| [@ergolyam](docs/benchmarks/ergolyam.md) | Linux | Radeon RX 6600, 8 GiB | Xeon E5-2643 v3 | 32 GiB DDR4 |
| [@gmankab](docs/benchmarks/gmankab.md) | Fedora Linux 45 Silverblue | Radeon RX 6800, 16 GiB | Xeon E5-2670 v3 | 48 GiB DDR4 |
| [@sillynocodecat](docs/benchmarks/sillynocodecat.md) | Windows | Radeon RX 7800 XT | Ryzen 5 5600 | 32 GiB DDR4 |

## @gmankab’s Linux results

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
- [Branch snapshot](data/branches.csv)
- [Contributing benchmark results](CONTRIBUTING.md)

The documented runs used local Xbox and PS5 builds. A reproducible extraction from the owner’s Xbox was unavailable during the Xbox tests. The wiki publishes observations and source-code references. The local tests used read-only game mounts and isolated runtime state.
