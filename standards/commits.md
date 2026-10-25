# Commits

Follows [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/).

## Format

```
<type>(<optional scope>): <subject>

<optional body: why, not what>

<optional footers>
```

- **type**: one of the list below.
- **scope**: the area touched, in lowercase: `api`, `db`, `ci`, `deps`.
- **subject**: imperative mood ("add", not "added" or "adds"), starts lowercase, no
  full stop, 72 characters or fewer for the whole first line.
- **body**: wrap at 72. Explain the reason and any trade-off. The diff already shows
  what changed.

| Type | Use for | Version bump |
| --- | --- | --- |
| `feat` | new behaviour a user or caller can see | minor |
| `fix` | a bug fix | patch |
| `perf` | faster or lighter, same behaviour | patch |
| `refactor` | restructuring with no behaviour change | none |
| `docs` | documentation only | none |
| `test` | tests only | none |
| `build` | dependencies, packaging, Dockerfiles | none |
| `ci` | workflow and pipeline config | none |
| `style` | formatting only | none |
| `chore` | anything else that doesn't touch shipped code | none |
| `revert` | undoing an earlier commit | depends |

A breaking change adds `!` after the type and a footer:

```
feat(api)!: return 410 for expired links

BREAKING CHANGE: clients that treated 404 as "expired" must handle 410.
```

## Footers

| Footer | When |
| --- | --- |
| `Closes #12` | the commit finishes an issue |
| `Refs #12` | related, but doesn't finish it |
| `BREAKING CHANGE: ...` | any breaking change |
| `Co-Authored-By: Name <email>` | someone else, or an AI tool, wrote part of it |

## How it's enforced

- **On `main`:** merges are squash-only, so each PR becomes one commit titled with the
  PR title. The `pr-title` check rejects titles that break the format.
- **Locally:** a [commitizen](https://commitizen-tools.github.io/commitizen/) pre-commit
  hook checks each message before the commit is created.
- **Releases:** version bumps and changelogs are generated from these types (see
  [versioning](versioning.md)), which is why the format is strict.

## Examples

```
feat: add custom short codes
fix(redirect): return 404 for unknown codes
build(deps): bump fastapi to 0.120
ci: cache uv downloads between runs
refactor(db): move queries into a repository class
```
