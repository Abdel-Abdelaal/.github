# Shared baseline

Reviewed source for weekly pull-request distribution. The synchronizer copies only
an explicit file allowlist; it does not execute files or accept arbitrary paths.
`.baseline-ignore` contains exact relative file names, one per line. Invalid or
unknown entries fail closed. Exceptions are visible and reviewed with the repo.

- Common: LF attributes, editor settings, provider configuration, Scorecard caller.
- Python: Ruff base, strict Pyright base and frozen pre-commit revisions.
- `labels.yml`: Terraform source reference; fleet label writes remain governance-owned.
- `new-repo/`: the starter a new repo copies once (callers, CODEOWNERS, Dependabot,
  LICENSE, templates). It isn't synced; see
  [Starting a new repo](../standards/repo-structure.md#starting-a-new-repo).

Bot PR creation requires the automation App. Missing credentials skip explicitly;
there is no personal-token fallback. All copied changes run normal required CI.
Hook updates propose changes in this source first, then the next sync distributes them.

This repo uses its own baseline. The `Baseline matches root` check fails any PR where
a baseline file and this repo's root copy differ, so a root-only update (Dependabot
only scans `/`) can't leave an older baseline to be synced out to the fleet.
