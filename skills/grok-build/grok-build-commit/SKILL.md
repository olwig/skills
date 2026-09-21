---
name: grok-build-commit
description: >
  Finds a best-effort GitHub SHA in `xai-org/grok-build`. Two modes:
  an exact version string, or the latest mirrored changelog candidate
  (same walk as the olwig/pkgbuilds `update-versions` source job).
  Use for version-to-commit lookups and /grok-build-commit. This is
  not a channel lookup and not the published binary version.
---

# Grok Build commit

Use this skill when the user needs a GitHub commit from the upstream
snapshot repo `xai-org/grok-build` for a Grok Build CLI version.

This is not the prebuilt binary version and not an official xAI pin.

## Modes

### 1. Exact version

The user gives `X.Y.Z` (keep a suffix only if they typed one).

Do not fetch `stable` / `alpha` / `enterprise`. Do not run `grok --version`.
Do not walk the changelog.

Look up the first bump of that string in
`crates/codegen/xai-grok-version/Cargo.toml` (see Pick the commit).
If none: that version is not mirrored. Do not substitute `HEAD`.

### 2. Latest mirrored candidate

Use this when the user asks for the latest public source pin, the
`update-versions` mapping, what `grok-build` `pkgver` should be, or
wants a commit without giving a version.

This mode reads the public changelog and the stable pointer. It still
does not claim the result is the published binary.

The walk matches `.github/workflows/update-versions.yml` job
`grok-build` in `olwig/pkgbuilds`:

1. Fetch
   `https://raw.githubusercontent.com/xai-org/grok-build/refs/heads/main/crates/codegen/xai-grok-shell/CHANGELOG.md`
2. Candidates: headings `^# X.Y.Z — YYYY-MM-DD$`, sort version descending.
3. Cap: `curl -fsS https://x.ai/cli/stable`. **Skip every candidate newer
   than that semver.** Equal to stable stays a candidate. Older stays a
   candidate. If the pointer fails, say so and continue without a cap.
4. For each remaining candidate, find the first bump of that version in
   `crates/codegen/xai-grok-pager-bin/Cargo.toml`.
5. A bump counts only if the parent commit's version is missing or
   different. Same-version parent → skip that commit.
6. First candidate that maps wins. If none map: `not mirrored yet`.

Do not choose the version from the stable pointer alone. Stable is only
the cap. Public source can lag (example: stable `1.0.40`, mirrored
`1.0.38`).

## What the SHA is

Repo: https://github.com/xai-org/grok-build.git

`xai-org/grok-build` is a public snapshot of xAI's internal monorepo.
Snapshot commits usually say `Synced from monorepo`. There are no GitHub
tags or releases for this mapping. `SOURCE_REV` is the internal monorepo
SHA, not the GitHub SHA you should return.

Return the **first** commit (the oldest one on `main`) where the mode's
Cargo file has `version = "<ver>"`. Later snapshots may still show the
same version string. Do not return `HEAD` just because the same version
is still there.

If the search finds nothing, say that clearly.

Exact-version mode uses `crates/codegen/xai-grok-version/Cargo.toml`.
Latest-mirrored mode uses `crates/codegen/xai-grok-pager-bin/Cargo.toml`,
same file as the pkgbuilds workflow. If you also check the other file and
the SHAs differ, report both. Do not silently pick one.

## Fetch the repo

You need full history for that file, so do not use `--depth`. If the
current repo is already `xai-org/grok-build` with full history, use it.
Otherwise clone to a temp dir, not into the user's project:

```bash
git clone --single-branch https://github.com/xai-org/grok-build.git
```

## Pick the commit

Exact version (`xai-grok-version`):

```bash
VER=1.0.13   # the version number
git fetch origin main
git log origin/main --reverse -S "version = \"$VER\"" --format='%H' \
  -- crates/codegen/xai-grok-version/Cargo.toml | head -1
```

Latest-mirrored candidate (`xai-grok-pager-bin`), same form, other path:

```bash
VER=1.0.38   # current changelog candidate at or below the stable cap
git log origin/main --reverse -S "version = \"$VER\"" --format='%H' \
  -- crates/codegen/xai-grok-pager-bin/Cargo.toml | head -1
```

Confirm (swap the path to match the mode):

```bash
CARGO=crates/codegen/xai-grok-version/Cargo.toml
git show "$SHA:$CARGO" | grep '^version'
# must print: version = "<ver>"
git show "$SHA^:$CARGO" | grep '^version'
# must be the previous version, not <ver>
```

If no commit matches, that version has not been mirrored to the public
repo yet. Do not substitute `HEAD` or a tag.

Do not use as the GitHub SHA:

- `SOURCE_REV`
- the hash in `grok --version` / npm `gitHead`
- GitHub tags or releases

## Output

Exact version:

```
Mode: exact-version
Version: 1.0.13
Commit: bb7f39d5858cbf5e00de639367f59debbdcb0138
Parent: bc7f02eddd3d84085849dc19ed216f11c23b0571 (1.0.12)
Cargo file: crates/codegen/xai-grok-version/Cargo.toml
Repo: https://github.com/xai-org/grok-build
Note: snapshot heuristic; not an official xAI pin
```

Latest mirrored:

```
Mode: latest-mirrored
Version: 1.0.38
Commit: 4247f661689354b831191f11eeeac8424993fe3d
Parent: a28ee2b2063426e8816e380ccea528b9de95e5da (1.0.35)
Stable cap: 1.0.40
Skipped above cap: 1.0.40 1.0.39
Cargo file: crates/codegen/xai-grok-pager-bin/Cargo.toml
Repo: https://github.com/xai-org/grok-build
Note: snapshot heuristic; not an official xAI pin
```

If the user asks for JSON, return an object with the same fields
(`mode`, `version`, `commit`, `parent`, `stable_cap` and
`skipped_above_cap` when used, `cargo_file`, `repo`, `note`).
