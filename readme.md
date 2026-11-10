# Renovate ignores a post-upgrade task that undoes a change to a file

Discussion: DISCUSSION_LINK

## Setup

- `minimatch@^10.2.6` has a GitHub security advisory (GHSA-g3hv-75wg-m249), fixed in 11.0.1.
- `pnpm-workspace.yaml` sets `minimumReleaseAge` to one day and turns on `minimumReleaseAgeExcludePrune`.
- `renovate.json` runs `pnpm install --lockfile-only --ignore-scripts` as a post-upgrade task for security updates.

## Steps

1. Renovate opens a security PR that bumps `minimatch` to `^11.0.0`.
2. Renovate adds `minimatch@11.0.1` to `minimumReleaseAgeExclude` in `pnpm-workspace.yaml`.
3. The lockfile resolves `minimatch@11.0.5`, which is old enough not to need an exemption.
4. The post-upgrade `pnpm install` removes the unneeded entry again, so `pnpm-workspace.yaml` is back to exactly what it is on `main`.

## Current behavior

The PR still changes `pnpm-workspace.yaml` and adds the `minimatch@11.0.1` entry. Running `pnpm install` on the PR branch removes it again and leaves the working tree dirty.

After the post-upgrade task, `git status` doesn't list `pnpm-workspace.yaml`, because it is unchanged from `main`. Renovate only picks up files that `git status` lists, so it keeps the version it wrote before the task ran.

## Expected behavior

The PR doesn't touch `pnpm-workspace.yaml`, because the post-upgrade task put it back the way it was.
