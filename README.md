# AppThere OS — repo manifest

Manifest for **AppThere OS**, a 64-bit-only Android fork based on
**AOSP `android-17.0.0_r1`**, targeting arm64 and x86_64 across phones,
tablets and laptops, with desktop window management and the Linux
development environment (AVF Terminal) enabled by default.

## Branches

| Branch | Base | Purpose |
| --- | --- | --- |
| `android-17.0.0_r1` | pristine AOSP | untouched upstream, for diffing |
| `appthere-17` | AOSP 17.0.0_r1 | AppThere development |

## Getting the source

```bash
mkdir -p appthere-os/src && cd appthere-os/src
repo init -u https://github.com/AppThere/manifest -b appthere-17 --git-lfs
repo sync -c -j8 --no-clone-bundle --no-tags
```

`-c` fetches only the current branch and `--no-tags` skips AOSP's very large
tag set; together they cut the sync from ~250 GB to roughly 120 GB.

## How this fork is structured

`default.xml` is AOSP's own manifest with three edits:

1. The `aosp` remote's `fetch` is made **absolute**
   (`https://android.googlesource.com/`). Upstream used `fetch=".."`, which
   resolves relative to the manifest URL — that silently points at GitHub
   once the manifest is rehosted, so it must be pinned.
2. The `superproject` element is **removed**. It pins an exact SHA per
   project and conflicts with per-project overrides.
3. `appthere.xml` is **included last**, so our entries win.

`appthere.xml` declares AppThere-only projects and holds the
**fork-on-write** override list.

### Fork-on-write

Unmodified projects are fetched directly from googlesource. A project moves
to `github.com/AppThere/` only when we actually need to patch it, via
`scripts/fork-upstream.sh <aosp-project-path>`.

That keeps the override list short and makes it an exact, reviewable
statement of how AppThere diverges from AOSP. The list is mirrored in
`docs/DIVERGENCE.md`.

## Licensing

Manifest content is Apache-2.0, matching AOSP. Forked projects retain their
upstream licenses; GPL-licensed components (notably the Linux kernel) are
published as required.
