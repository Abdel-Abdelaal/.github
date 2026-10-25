# Repo structure

## Repo types

| Type | Example | Purpose |
|---|---|---|
| Service | [shortlink](https://github.com/Abdel-Abdelaal/shortlink) | a deployable app or API |
| Library | (none yet) | a package other code installs |
| Platform | [platform-workflows](https://github.com/Abdel-Abdelaal/platform-workflows) | shared CI/CD workflows |
| Governance | [github-governance](https://github.com/Abdel-Abdelaal/github-governance) | repository settings and fleet evidence |
| Standards | this repo | the handbook and default community files |

## Naming repos

Lowercase kebab-case, named for what the repo is: `shortlink`, `platform-workflows`.
No `-repo`, `-project`, or `my-`.

## Files every repo has

| File | Why |
|---|---|
| `README.md` | what it is, how to run it, how to contribute. Follows [Standard Readme](https://github.com/RichardLitt/standard-readme) sections. |
| `LICENSE` | MIT unless there's a reason not to |
| `.gitattributes` | `* text=auto eol=lf`, so Windows checkouts don't turn files into CRLF |
| `.github/CODEOWNERS` | who reviews what |
| `.github/dependabot.yml` | dependency updates, every ecosystem with a 14-day cooldown |
| `.github/workflows/` | CI that calls [platform-workflows](https://github.com/Abdel-Abdelaal/platform-workflows) |
| `AGENTS.md` | context for AI agents: commands, layout, glossary, rules (see [AI](ai.md)) |
| `CLAUDE.md` | `@AGENTS.md`, so Claude Code reads the same context |
| `opencode.json` | opencode CLI provider config for local use, environment references only. CI review uses the platform runner's own config. |

Conformance (platform-workflows v1.6.0, `REQUIRED` in `tools/conformance.py`) fails
a PR when any of these is missing. Fleet health in
[github-governance](https://github.com/Abdel-Abdelaal/github-governance) checks them
daily on `main`, together with the five callers below.

`CONTRIBUTING.md`, `SECURITY.md`, issue templates and the PR template come from this
`.github` repo automatically. A repo only adds its own copy to override one.

### CODEOWNERS

Every path has an owner. The owner is requested for review automatically, and on team
repos their approval is required by the branch rules.

## Starting a new repo

Every new public repo joins the fleet. Fleet health lists any public repo that isn't
a fork, isn't archived and is missing from `github-governance/repos.yaml` as
**unmanaged** until it's onboarded. This account is personal, so no account-wide
ruleset can do it on creation (see
[Moving to a GitHub organisation](https://github.com/Abdel-Abdelaal/github-governance/blob/main/docs/setup.md#moving-to-a-github-organisation)).

The starter is [`baseline/new-repo/`](../baseline/new-repo). Its callers are
byte-identical to this repo's own and pinned to the same platform-workflows release
by full SHA. `Baseline matches root` fails any PR that lets them drift.

| Starter file | Calls |
|---|---|
| `.github/workflows/pr.yml` | `pr-title.yml` (Conventional title) |
| `.github/workflows/security.yml` | `security.yml` (CodeQL, dependency review, secrets, workflow lint) |
| `.github/workflows/conformance-checks.yml` | `conformance.yml` (required files, pins, cooldowns) |
| `.github/workflows/scorecard.yml` | OpenSSF Scorecard, `report-failure.yml` |
| `.github/workflows/ai-review.yml` | `ai-review.yml` (advisory, needs `AI_API_KEY`) |

It also has `CODEOWNERS`, `dependabot.yml` (14-day cooldown), `LICENSE` (MIT),
`CLAUDE.md`, and README and AGENTS.md templates to fill in. The common files come
from `baseline/` itself.

1. Create the repo, lowercase kebab-case, and clone it next to this one:

   ```bash
   gh repo create Abdel-Abdelaal/NAME --public --description "One sentence" --clone
   ```

2. Copy the starter and the common baseline files (run from the folder that holds
   both clones):

   ```bash
   cp -R .github/baseline/new-repo/. NAME/
   cp .github/baseline/.gitattributes .github/baseline/.editorconfig .github/baseline/opencode.json NAME/
   ```

   Python repos also copy `ruff-base.toml`, `pyrightconfig.base.json` and
   `.pre-commit-config.yaml`. Then:
   - add the repo's languages to `languages` in `security.yml`, for example
     `'["actions", "python"]'`
   - add its ecosystems to `dependabot.yml`
   - add a CI caller such as `python-ci.yml`, with the same pin

3. Fill in `README.md` and `AGENTS.md`, then make the first commit:

   ```bash
   git -C NAME add -A
   ```

   ```bash
   git -C NAME commit -m "chore: start from the fleet starter"
   ```

   ```bash
   git -C NAME push -u origin main
   ```

4. Add the repo to `github-governance/repos.yaml` by PR, with its required checks.
   Add it to the governance and automation App installations, and set `AI_API_KEY`
   if it should get AI review.
5. From then on, every change goes through a PR with a Conventional title.

The next Fleet health run moves the repo from unmanaged to managed and checks its
files and callers. Settings and rulesets in `repos.yaml` stay desired state until
the governance apply runs.

There's no GitHub template repo on purpose. A template would be one more repo to
keep pinned, and its caller pins would drift without a check. Copying from
`baseline/` keeps one reviewed source.

## Python service layout

Uses the [src layout](https://packaging.python.org/en/latest/discussions/src-layout-vs-flat-layout/),
so tests run against the installed package, not loose files that happen to be on the path.

```
shortlink/
├── .github/
│   ├── CODEOWNERS
│   └── workflows/
├── docs/
│   └── decisions/          # ADRs, see decisions.md
├── src/
│   └── shortlink/
│       ├── __init__.py
│       ├── main.py         # app factory and wiring only
│       ├── api/            # routes: HTTP in, HTTP out
│       ├── domain/         # business rules, no FastAPI or DB imports
│       ├── db/             # persistence
│       └── settings.py     # config from environment variables
├── tests/
│   ├── unit/               # domain logic, fast, no I/O
│   └── integration/        # API + DB together
├── .editorconfig
├── .gitattributes
├── .gitignore
├── .pre-commit-config.yaml
├── .python-version
├── AGENTS.md
├── CHANGELOG.md            # generated, see versioning.md
├── LICENSE
├── pyproject.toml          # dependencies and all tool config
├── README.md
└── uv.lock
```

Dependencies point inward: `api` and `db` may import `domain`, and `domain` imports
neither. That keeps business rules testable without a server or a database.

All tool config (ruff, pyright, pytest, coverage) lives in `pyproject.toml`, not in
separate dotfiles.

## Configuration and secrets

- Config comes from environment variables, read once in `settings.py`
  (pydantic-settings).
- `.env.example` is committed with every variable and a dummy value. `.env` never is.
- Secrets in CI are GitHub Actions secrets, scoped to an environment where possible.

Remote workflow pinning follows [ADR 0003](../docs/decisions/0003-pin-controlled-workflow-releases.md).

## Shared configuration and conformance

`baseline/` is the reviewed source for common files. `.baseline-ignore` documents
exact path exceptions. Python repos extend `ruff-base.toml` and carry a strict
Pyright base; local configuration may strengthen or specialize it.
`opencode.json` contains environment references only, never credentials.

The conformance tool checks tracked ownership/community files, glossary, lockfiles,
full-SHA remote workflow/action and hook references, empty default token permissions,
explicit secrets and a minimum 14-day cooldown for every dependency ecosystem.
It parses configuration as data and never executes repository code. Malformed input
fails closed. Actionlint, zizmor, CodeQL and the secret scanner provide deeper checks.
