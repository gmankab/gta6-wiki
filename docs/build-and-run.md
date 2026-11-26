# Build and launch notes

Run builds in Fedora Toolbox and keep project dependencies there. The original `torrents/` tree is read-only throughout this workflow. Staging used `cp -r --reflink=always`, and the duplicate staging copy was removed after an inventory and executable-hash comparison. The eventual Flatpak checkout has one `gta6/` content directory. Git worktrees live alongside the other project repos. Build tools, prefixes, binaries and logs live under `tools/`.

## Wine and Proton

The first successful run used a Windows-target VIctory release, UMU, stock Proton Experimental, and the RX 6800. TuxBox independently passed its Python and Lanes tests but failed before the title’s first rendered frame on this Xeon. Enter maps to A, Backspace to B, and the arrow keys to the D-pad in the original scripted launch. For a run comparison, record the runtime commit, Wine build, graphics DLLs, console profile, cache state, prefix and scripted input.

For the existing Flatpak, use the instructions below. The original Toolbox runner used a separate Wine prefix, cache and local state, network isolation and a read-only game mount. Title-generated saves were created in an isolated prefix. One prologue restore read both save blobs and was later verified by movement and camera input. User profiles and original assets were kept out of the test prefixes.

A stopped launcher can leave Wine processes alive. `wineserver` holds a prefix’s shared state, while service, audio and device helpers may run separately. Before cleaning test processes, identify the matching test prefix. The post-Leonida cleanup stopped the helpers for the two expired comparison prefixes.

## Flatpak

The [packaging repository](https://github.com/gmanka-flatpaks/io.github.marvisak.victory) grew from a build-only manifest to an installed `victory-launch` command. On the tested local checkout, run the following from the manifest directory. The `gta6/` subdirectory should contain the existing files, including `game_scarlett_master.exe`.

```bash
flatpak run org.flatpak.Builder --user build --repo=repo \
  --install-deps-from=flathub --force-clean --install io.github.marvisak.victory.yml

flatpak run --cwd="$PWD" --filesystem="$PWD/gta6:ro" io.github.marvisak.victory
```

The mount uses an absolute path expanded by the shell. The launcher resolves `./gta6/game_scarlett_master.exe` relative to the supplied working directory. A minimal shell check for that path is `test -f ./gta6/game_scarlett_master.exe`. [Flatpak sandbox permissions](https://docs.flatpak.org/en/latest/sandbox-permissions.html)

| Part | Verified configuration |
|---|---|
| Build options | Native Flatpak `build-options.env`, freedesktop Rust SDK extension, LLVM SDK extension, Windows MSVC target with `clang-cl` and `lld-link`. |
| Rust dependency pinning | Generated cargo sources using `flatpak-builder-tools/cargo/flatpak-cargo-generator.py` on the root `Cargo.lock` from the identical Victory revision used in the manifest. The original exact pin was `8e79c80e4b71e252bcd271e0587aa662c4770008`. New revisions need their own matching lockfile sources. |
| SDK source | Official prebuilt `xwin 0.10.0` with a SHA-256 pin. The first offline SDK pack listed 41 files. Cache paths and filenames came from the working `xwin splat` cache and Microsoft manifest. |
| Module layout | Independent xwin, Proton, Victory and launcher modules. xwin uses `splat --copy` to install the SDK under `/app/lib/xwin` across build mounts. The Flatpak source list uses YAML and the Microsoft channel payload remains a separate JSON file for xwin. |
| Networking | Flatpak Builder downloads pinned sources before the offline module build. The manifest dropped `--share=network` and Cargo ran `--locked --offline`. |
| Initial versus local test source | The original manifest fetched Victory and xwin over HTTPS. The tested custom manifest explicitly used `file:///var/mnt/ssd/recomp/VIctory`, branch `openworld-rx6800`, commit `748d150c80660ae311079e013588a392102d4e09`, plus Proton Experimental graphics DLLs from the local tools directory. Victory source patches belong in the Victory checkout. |
| Runtime and command | `org.winehq.Wine` base `stable-26.08`, Wayland socket, audio socket and DRI. The built entry point executes `wine /app/bin/victory.exe run ./gta6/game_scarlett_master.exe --execute`. Wayland worked with `DISPLAY` unset and the X11 socket removed. |
| Prefix and overrides | `WINEPREFIX=/var/data/wine` makes the prefix persistent under the app’s Flatpak data directory. `XDG_DATA_HOME` inside the sandbox maps to that app data. Wine uses `$HOME/.wine` in the absence of an override. The unquoted YAML scalar for `WINEDLLOVERRIDES` retained the semicolon and trailing `=`. |

The build-only shell module was used while the package produced a Windows executable without a runtime launcher. It was superseded by the Wine launch entry point. The app ID moved from `io.github.gmankab.gta6` to `io.github.marvisak.victory`.

## Native Linux Victory

[Build guide for the tested native branch](https://github.com/gmankab/victory/blob/test/native-guarded-copy/docs/linux.md). This branch builds a Linux ELF. The loader maps the existing x86-64 title executable, resolves the guest ABI, and calls native vkd3d-proton and SDL3 for graphics and presentation. It needs native `libvkd3d-proton-d3d12.so` and `libvkd3d-proton-d3d12core.so` of the same build. Install those and, for indirect clamp support, the Microsoft Linux DXC libraries into the tools directory inside Toolbox.

A representative configuration from the full title tests is below. Set the paths to the installed native libraries and the existing game directory. Start from a private working directory with write access for the shader cache and logs. Use a read-only game mount.

```bash
cargo build --locked --release -p victory --features diagnostics

VICTORY_VKD3D=/path/to/native-vkd3d/lib \
VICTORY_DXIL=/path/to/dxc/lib/libdxil.so \
VICTORY_DXCOMPILER=/path/to/dxc/lib/libdxcompiler.so \
VICTORY_GAME_DIR=/path/to/gta6 \
SDL_VIDEODRIVER=wayland \
target/release/victory run /path/to/gta6/game_scarlett_master.exe --execute --console series-s
```

The scripted software tests used a private headless Weston/pixman session, Lavapipe, a fresh copy of known cache/state seeds and a time cap. CPU-only runs hid `/dev/dri`. Hardware fixtures used RADV and exact readback assertions. See the [native branch table](projects.md#native-snapshots) for the supported snapshots and what each one reached.

`VICTORY_SKIP_BINK=1` is an opt-in test shortcut. `VICTORY_NO_WATCHDOG=1` was used as a mapped-image diagnostic for software runs. Forced shader caps, skipped draws and forced sampler modes were experiments used to identify expensive or incorrect passes. They change what the game renders and therefore affect benchmark interpretation.

## Build caching

Kache 2.3.1 was used as `RUSTC_WRAPPER` for Rust builds. In a separate test, its `gcc` and `g++` compiler shims were selected through a local Meson native file in Toolbox. Both shims hit the cache across fresh Meson build directories. Use a cache on the build volume if you want to preserve reflink or no-copy behavior. A warm cache accelerates eligible object builds; linking runs normally. One existing Ninja build directory already skips targets that remain up to date. [Kache documentation](https://ninja.kunobi.com/docs/kache/getting-started/c-cpp)

Blaze accelerated Linux checks and built a functional inspection ELF. Its Windows cross-target hit a host-platform guard before invoking the linker. Rustd stalled compiling logging code. Fedora-packaged Rust was used where compatible; the MSVC build still needs target standard libraries and the offline SDK. See the [timing table](benchmarks.md#isolated-measurements-and-build-times).

## Niri window behavior

Wine’s Wayland client was tested at a 2× desktop scale. The prefix’s `LogPixels` was set to 192 DPI to match the desktop scale. The frame still looked blurry. Both Niri fullscreen modes kept the title bar, so the prototype removed the Win32 caption and frame directly. The helper was prototyped as a standalone utility, reduced in Rust, then superseded in the tested package by an always-borderless Victory source branch.

The following was validated with Niri 26.04. Choose your output name in place of `DP-1`. Either match can select the window. `niri msg pick-window` can confirm a fork’s title or app ID. [Niri named workspaces](https://niri-wm.github.io/niri/Configuration:-Named-Workspaces.html) · [Window rules](https://niri-wm.github.io/niri/Configuration:-Window-Rules.html#open-focused)

```kdl
workspace "game" {
    open-on-output "DP-1"
}

window-rule {
    match title=r#"(?i)^victory( \|.*)?$"#
    match app-id=r#"(?i)^(io\.github\.marvisak\.victory|victory(\.exe)?|game_scarlett_master\.exe)$"#
    open-on-workspace "game"
    open-focused false
}
```
