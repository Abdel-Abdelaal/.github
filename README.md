# Engineering standards

How I write, review and ship code across every repo under
[@Abdel-Abdelaal](https://github.com/Abdel-Abdelaal). Two jobs:

1. **The handbook** in [`standards/`](standards/): the written rules for commits, PRs,
   issues, code review, Python code, repo layout, versioning, decisions and AI use.
2. **Default community files.** GitHub uses the issue templates, PR template,
   `CONTRIBUTING.md` and `SECURITY.md` in this special `.github` repo for any of my
   repos that doesn't have its own. Every repo gets them without copying.

Most rules here are enforced by a tool or a CI check, not by memory. Each standard
says which. The CI checks live in
[platform-workflows](https://github.com/Abdel-Abdelaal/platform-workflows).

## The handbook

| Standard | Covers | Built on |
|---|---|---|
| [Commits](standards/commits.md) | commit messages, PR titles | [Conventional Commits 1.0](https://www.conventionalcommits.org/) |
| [Pull requests](standards/pull-requests.md) | size, description, merge flow | [Google eng-practices](https://google.github.io/eng-practices/) |
| [Code review](standards/code-review.md) | what reviewers look for, comment format | [Google eng-practices](https://google.github.io/eng-practices/review/), [Conventional Comments](https://conventionalcomments.org/) |
| [Issues](standards/issues.md) | tickets, labels, acceptance criteria | GitHub issue forms, labels as code |
| [Python](standards/python.md) | naming, comments, docstrings, types, duplication | [PEP 8](https://peps.python.org/pep-0008/), [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html), [Ruff](https://docs.astral.sh/ruff/) |
| [Repo structure](standards/repo-structure.md) | layout and required files per repo type | [PyPA src layout](https://packaging.python.org/en/latest/discussions/src-layout-vs-flat-layout/), [Standard Readme](https://github.com/RichardLitt/standard-readme) |
| [Versioning](standards/versioning.md) | releases, tags, changelogs | [SemVer 2.0](https://semver.org/), [Keep a Changelog](https://keepachangelog.com/) |
| [Decisions](standards/decisions.md) | recording why we chose something | [MADR](https://adr.github.io/madr/) |
| [AI](standards/ai.md) | where AI runs, API keys, what it may do | [AGENTS.md](https://agents.md/), [opencode](https://opencode.ai/) |
| [Governance](standards/governance.md) | how everything is re-checked and kept current, settings as code | Terraform, Dependabot, OpenSSF Scorecard |

## Changing a standard

Open a PR here. A standard changes when the tool or check that enforces it changes in
the same release, so the written rule and the enforced rule never drift apart.

## Advisory AI maintenance

Same-repository PRs receive an isolated MiMo 2.6 Flash review; fork and Dependabot
PRs skip. Findings are non-blocking summaries and neutral notice annotations.
Weekly audits maintain one health issue. A bounded, tested draft fix PR is possible
only after the automation App is configured; otherwise that step skips explicitly.
No AI check is required for merging, and no AI workflow can merge or approve.
See [the isolation design](https://github.com/Abdel-Abdelaal/platform-workflows/blob/main/docs/ai.md).

Maintenance callers now pin platform v1.7.0 (`40e5261e2e39d8d0c88e709c26385cab3cc43466`).
Signed AI evidence and measured runs appear in the
[public AI observatory](https://abdel-abdelaal.github.io/github-governance/). Frozen hook checks
run where a hook configuration is supported; native Dependabot maintains baseline
hook revisions. Stale/release mutations remain explicitly gated on canonical
labels and maintenance App setup. The Python audit includes development tooling.
