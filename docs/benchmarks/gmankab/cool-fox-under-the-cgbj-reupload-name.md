# Cool Fox under the CGBJ reupload name

**Run by** [@gmankab](https://github.com/gmankab)

The public `cgbj-reupload` resolves to the original Cool Fox `f4b48ad` commit. Both runs used the locally built Windows-target binary, the `pareto-residency` Series S profile, Wine/Wayland, RADV on the RX 6800, and a read-only title directory. The rerun granted Wine access to PulseAudio. [Source check](../../projects.md#cgbj-source-checks)

| Setup | Result |
|---|---|
| Fresh data and scripted input, 155 s | Rendered the intro and advanced to Legal Notices. The last capture, frame 3,900, still showed Legal Notices. Median **30.0 FPS** across 27 startup and setup samples. |
| Copied checkpoint profile and adjusted input, 155 s | Advanced beyond Legal Notices to the illustrated loading screen, which remained visible in the last capture at frame 3,600. Median **30.0 FPS** across 26 startup and loading samples. |

Both runs reached their time caps and the kernel logged no GPU reset. Player control and an in-world frame rate remain unverified for this commit on the RX 6800. [Run index](../../../data/benchmarks/gmankab/runs.csv)
