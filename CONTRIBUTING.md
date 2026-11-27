# Contributing benchmarks

## Choose the tester’s page

1. Use `docs/benchmarks/<github-handle>.md` for measurements from that tester. The page name follows the person who ran the test, even if someone else writes the entry or commits it.
2. Keep that tester’s CSVs and other shareable evidence in `data/benchmarks/<github-handle>/`. Link evidence from the entry it supports. Give a new file a descriptive run name, such as `2026-11-28-kyty-night-scene.csv`.
3. For a new tester, add their page, their evidence directory, and a row linking both from the benchmark index. Include their GitHub handle, OS, CPU, GPU, and RAM. Add exact driver and OS versions to the entry when they matter. [Benchmark index](docs/benchmarks.md)

Put a result in the directory for the person who measured it. If two contributors run the same test, record each result on their own page and cross-link the entries. Put published results from outside the wiki on the published benchmarks page with the original reporter and a source link. [Published benchmarks](docs/benchmarks/external.md)

## Record a run

Use a descriptive heading and name the tester in the entry. Include the date, tested repository and exact revision or build, OS and driver, renderer or runtime, relevant settings, and the scene or workload. State which build was the baseline in a comparison. For FPS, include the sample duration or frame count, measurement method, and what the player could see or control. Link shareable evidence and call out differences that affect comparison, such as using a warm cache for one run.

```markdown
## YYYY-MM-DD · Project and scene

**Run by** [@handle](https://github.com/handle)

**Build and setup** Link the revision and record the OS, driver, renderer,
game version or console profile, settings, and cache state.

**Workload** Describe the scene or fixture and how it was measured.

**Result** Give the measured value, sample size, and furthest verified stage.

**Evidence** Link the corresponding CSV, log summary, or public test report.
```

Label loading, menus, cinematics, controllable gameplay, and synthetic fixtures separately. Use a relative link for evidence committed here. Add public summaries and source links. Keep game files, saves, captures, and raw dumps out of the repository.

## Submit changes

Run `git pull --rebase` before pushing. Keep the history linear and use ordinary pushes. Review the changed files and check that the benchmark links open.
