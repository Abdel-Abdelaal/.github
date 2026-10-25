# Versioning and releases

## Versions

[Semantic Versioning 2.0](https://semver.org/): `MAJOR.MINOR.PATCH`.

| Bump | When | Comes from |
| --- | --- | --- |
| major | breaking change | `!` or a `BREAKING CHANGE:` footer |
| minor | new feature | `feat` |
| patch | bug fix or performance improvement | `fix`, `perf` |

Before `1.0.0`, breaking changes bump minor. `1.0.0` means the API is stable.

## Tags and releases

- Every release is a git tag `vX.Y.Z` with a GitHub Release.
- Reusable workflows publish a moving major tag (`v1`) for discovery. Managed
  callers pin release commit SHAs; Dependabot proposes reviewed updates.
- All remote actions and workflows are pinned to a full commit SHA with the version in a comment.
  Tags can be moved; SHAs can't.

## Changelog

`CHANGELOG.md` follows [Keep a Changelog](https://keepachangelog.com/). It's generated
by [release-please](https://github.com/googleapis/release-please) from the commit
types on `main`: it keeps a release PR open with the next version and changelog, and
merging that PR tags the release. Nobody edits the version or changelog by hand.

Remote workflow pinning follows [ADR 0003](../docs/decisions/0003-pin-controlled-workflow-releases.md).
