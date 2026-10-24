---
status: accepted
date: 2026-10-24
---

# Pin our own shared workflows to immutable release commits

## Context and problem statement

The original handoff allowed the account's reusable workflows at moving major tag
v1. The first CodeQL scan correctly identified that these references could change
without a consumer PR. The owner reinforced that the showcase should demonstrate
security and reliability beyond a minimal setup. Accepting the warnings would
weaken reproducibility and make rollback evidence harder to follow.

## Considered options

- Accept own moving tags and dismiss the findings.
- Pin only third-party actions.
- Pin every remote action and reusable workflow, including our own releases.

## Decision outcome

Use a full 40-character commit SHA plus a release-version comment for every remote
uses reference. Local relative reusable calls remain tied to their caller commit.
Keep v1 as a discoverability/convenience tag, but fleet callers use immutable
release commits. Dependabot proposes release updates through normal protected PRs.

### Consequences

- A workflow rerun uses the same platform code, independent of later releases.
- Consumers explicitly review platform updates and can roll back by reverting a PR.
- An urgent fix requires updating consumers, rather than silently moving all of them.
- Conformance and baseline sync must enforce SHA pins for our own calls too.
- CodeQL and Scorecard findings are fixed in source instead of suppressed.
