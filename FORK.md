# xenia-edge linux-aarch64 fork

This fork of [has207/xenia-edge](https://github.com/has207/xenia-edge) publishes
a native **linux-aarch64 AppImage** (`xenia_edge_linux-aarch64.AppImage`) on the
[Releases](https://github.com/chucklesb/xenia-edge/releases) page.

The emulator source is upstream's, unchanged. The fork only **adds** files, so
upstream merges never conflict:

| File | Purpose |
| --- | --- |
| `.github/workflows/linux-aarch64.yml` | Builds the AppImage on GitHub's `ubuntu-22.04-arm` runner. Each push to `edge` creates a release tagged with the short commit SHA. |
| `.github/workflows/sync-upstream.yml` | Merges upstream `edge` daily (06:00 UTC, or run it by hand) and pushes, which starts a new build and release. |
| `FORK.md` | This file. |

Upstream's own `CI` workflow is **disabled** in this repo's Actions settings, so
it doesn't build Windows/macOS/x86_64. Leave `CI.yml` and `build.yml` exactly as
upstream has them.

## One-time setup

1. Create a [fine-grained PAT](https://github.com/settings/personal-access-tokens/new)
   for `chucklesb/xenia-edge` only, with **Contents: Read and write** and
   **Workflows: Read and write**.
2. `gh secret set SYNC_TOKEN -R chucklesb/xenia-edge` and paste the token.
3. Test it: `gh workflow run sync-upstream.yml -R chucklesb/xenia-edge`.

Renew the PAT before it expires. When it lapses, the sync job fails at checkout.

## When the sync warns "Upstream changed build-linux"

The `build` job in `linux-aarch64.yml` is a copy of upstream's `build-linux` job.
The sync job's summary shows upstream's diff. Apply it to `linux-aarch64.yml`,
keeping the lines marked `fork:` (arm64 runner, `aarch64` linuxdeploy, and
`runner.arch` in the cache keys).

## Manual sync

```sh
git remote add upstream https://github.com/has207/xenia-edge.git  # once
git fetch upstream
git merge upstream/edge
git push origin edge
```

Don't use GitHub's **Sync fork** button. This repo's recorded parent is
`coulterpeterson/xenia-edge`, not has207.
