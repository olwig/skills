---
name: grok-build-commit
description: >
  Finds the best-effort GitHub SHA in `xai-org/grok-build` for a Grok
  Build version string such as `1.0.13`. Use the first public snapshot
  commit that sets `xai-grok-version` to that version. Use for
  version-to-commit lookups and /grok-build-commit. This is not an
  official channel-to-commit map and not the published binary version.
  For the published version number, use /grok-build-version.
---

# Grok Build commit

Use this skill when you have a **version number** and need the matching GitHub commit from the public snapshot repo.

This is not the prebuilt binary version and not an official xAI pin.

If the user gives a channel name (`stable`, `alpha`, `enterprise`) or no version at all, first get the version number from `grok-build-version`, then continue here. Do not fetch channel pointers in this skill. Do not run `grok --version`.

## What the SHA is

Repo: https://github.com/xai-org/grok-build.git

This repo is made of periodic public snapshots of an internal monorepo. Snapshot commits usually say `Synced from monorepo`. There are no GitHub tags or releases for this mapping. `SOURCE_REV` is the internal monorepo SHA, not the GitHub SHA you should return.

Return the **first** commit (the oldest one on `main`) where

`crates/codegen/xai-grok-version/Cargo.toml`

has `version = "<ver>"`. That crate tracks the CLI version. Later snapshots may still show the same version string. Do not return `HEAD` just because the same version is still there.

GitHub has no channels. Some versions, especially alpha, may not be mirrored yet. If the search finds nothing, say that clearly.

## Fetch the repo

You need full history for that file, so do not use `--depth`. If the current repo is already `xai-org/grok-build` with full history, use it. Otherwise clone to a temp dir, not into the user’s project:

```bash
git clone --single-branch https://github.com/xai-org/grok-build.git
```

## Pick the commit

```bash
VER=1.0.13   # the version number
git fetch origin main
git log origin/main --reverse -S "version = \"$VER\"" --format='%H' \
  -- crates/codegen/xai-grok-version/Cargo.toml | head -1
```

Confirm:

```bash
git show "$SHA:crates/codegen/xai-grok-version/Cargo.toml" | grep '^version'
# must print: version = "1.0.13"
git show "$SHA^:crates/codegen/xai-grok-version/Cargo.toml" | grep '^version'
# must be the previous version
```

If no commit matches, that version has not been mirrored to the public repo yet. Do not substitute `HEAD` or a tag.

Do not use as the GitHub SHA:

- `SOURCE_REV`
- the hash in `grok --version` / npm `gitHead`
- GitHub tags or releases

## Output

```
Version: 1.0.13
Commit: bb7f39d5858cbf5e00de639367f59debbdcb0138
Parent: bc7f02eddd3d84085849dc19ed216f11c23b0571 (1.0.12)
Repo: https://github.com/xai-org/grok-build
Note: snapshot heuristic; not an official xAI pin
```
