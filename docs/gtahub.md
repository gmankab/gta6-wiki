# GTAHub forks

The November 26, 2026 survey covered [GTAHub’s repository directory](https://git.gtahub.st/explore/repos). Its `github/` organization holds imported source snapshots and their GitHub issue and PR history. The `gmanka/` forks hold the branches used for the AMD Linux work.

| Fork | Verified branches | Contents |
|---|---:|---|
| [Victory](https://git.gtahub.st/gmanka/victory) | 155 | Canonical `main` at `d56316bb`, original `cool_fox` at `f4b48ad`, the union of public GitHub and Disroot work, and `upstream/` namespaces for relevant Victory lineages. |
| [XCL](https://git.gtahub.st/gmanka/xcl) | 5 | Source branches and `wine-rx6800-comparison`. |
| [Leonida](https://git.gtahub.st/gmanka/leonida) | 282 | Source branches and the public contribution refs. |
| [KLBF Lab](https://git.gtahub.st/gmanka/klbf-lab) | 6 | Stable refs and public beta refs under `upstream/beta/`. |
| [TuxBox](https://git.gtahub.st/gmanka/tuxbox) | 2 | Source `main` and `docs/projectscarlett-fresh-prefix`. |
| [AnyXboxX](https://git.gtahub.st/gmanka/anyxboxx) | 9 | Source refs from GTAHub. |

The [branch inventory](../data/gtahub-branches.csv) records the remote commit IDs. GitHub and Disroot remain the public sources for the established local Victory branches. The namespaced GTAHub copies retain additional upstream refs alongside them. See [published branches](projects.md#published-branches) for the other remotes.

## CLI

`fgj` v0.6.1 is available in the Fedora Toolbox. Append `--hostname git.gtahub.st` to select this Forgejo instance.

```bash
toolbox run fgj --version
toolbox run fgj repo view gmanka/victory --hostname git.gtahub.st
```

The executable is stored at `/var/mnt/ssd/tools/fgj/bin/fgj`. [fgj release](https://codeberg.org/romaintb/fgj/releases/tag/v0.6.1)

## Development activity

The sampled Victory, Fxceless, cool_fox, KLBF Lab, XCL and Leonida threads showed imported GitHub history. Recent imported comments were authored by `github-bot`. Three other local cool_fox forks held the same commit as the source and showed no issue or PR discussion in the API. No active in-scope GTAHub discussion was identified in this survey. [Upstream contributions and reports](upstream.md)
