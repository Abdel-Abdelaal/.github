# Pull requests

Every change to `main` goes through a PR. Rulesets block direct pushes, force pushes
and branch deletion, and require all checks to pass on an up-to-date branch.

## One PR, one concern

- A PR does one thing: a feature, a fix, or a refactor. Not a mix.
- Aim for under ~400 changed lines. Large changes are split into a series of PRs, or
  land behind a feature flag.
- Refactors and behaviour changes go in separate PRs, so a reviewer can trust that the
  refactor changes nothing.

## Branches

`<type>/<short-kebab-description>`, using the commit types from [commits](commits.md):
`feat/custom-codes`, `fix/expired-link-404`. Branches are deleted on merge.

## Title and description

- **Title:** a Conventional Commit, because it becomes the squash commit on `main`.
- **Description:** fill in the template: what, why, how to test, checklist. Link the
  issue with `Closes #N` so it closes on merge.
- Screenshots or example requests/responses for anything a user can see.

## Lifecycle

1. Open as a **draft** while work is in progress. CI runs on drafts too.
2. Self-review the diff on GitHub before asking anyone else. Most problems are caught here.
3. Mark ready for review only when CI is green.
4. Respond to every review comment, by changing the code or by replying with a reason.
    Threads must be resolved before merge.
5. Squash-merge. Auto-merge is allowed once checks and review pass.

## What blocks a merge

Branch rules on `main` require:

- passing checks (`pr-title`, plus lint, types, tests, coverage and security scans as
  they're added)
- the branch up to date with `main`
- all review threads resolved
- squash merge (linear history)

On a team, at least one approval from a [code owner](repo-structure.md#codeowners) as
well. On solo repos this is 0, because GitHub doesn't let you approve your own PR.
