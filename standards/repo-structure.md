# Repo structure

## Repo types

| Type | Example | Purpose |
|---|---|---|
| Service | [shortlink](https://github.com/Abdel-Abdelaal/shortlink) | a deployable app or API |
| Library | (none yet) | a package other code installs |
| Platform | [platform-workflows](https://github.com/Abdel-Abdelaal/platform-workflows) | shared CI/CD workflows |
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
| `.github/workflows/` | CI that calls [platform-workflows](https://github.com/Abdel-Abdelaal/platform-workflows) |
| `AGENTS.md` | context for AI agents: commands, layout, glossary, rules (see [AI](ai.md)) |

`CONTRIBUTING.md`, `SECURITY.md`, issue templates and the PR template come from this
`.github` repo automatically. A repo only adds its own copy to override one.

### CODEOWNERS

Every path has an owner. The owner is requested for review automatically, and on team
repos their approval is required by the branch rules.

## Python service layout

Uses the [src layout](https://packaging.python.org/en/latest/discussions/src-layout-vs-flat-layout/),
so tests run against the installed package, not loose files that happen to be on the path.

```
shortlink/
â”œâ”€â”€ .github/
â”‚   â”œâ”€â”€ CODEOWNERS
â”‚   â””â”€â”€ workflows/
â”œâ”€â”€ docs/
â”‚   â””â”€â”€ decisions/          # ADRs, see decisions.md
â”œâ”€â”€ src/
â”‚   â””â”€â”€ shortlink/
â”‚       â”œâ”€â”€ __init__.py
â”‚       â”œâ”€â”€ main.py         # app factory and wiring only
â”‚       â”œâ”€â”€ api/            # routes: HTTP in, HTTP out
â”‚       â”œâ”€â”€ domain/         # business rules, no FastAPI or DB imports
â”‚       â”œâ”€â”€ db/             # persistence
â”‚       â””â”€â”€ settings.py     # config from environment variables
â”œâ”€â”€ tests/
â”‚   â”œâ”€â”€ unit/               # domain logic, fast, no I/O
â”‚   â””â”€â”€ integration/        # API + DB together
â”œâ”€â”€ .editorconfig
â”œâ”€â”€ .gitattributes
â”œâ”€â”€ .gitignore
â”œâ”€â”€ .pre-commit-config.yaml
â”œâ”€â”€ .python-version
â”œâ”€â”€ AGENTS.md
â”œâ”€â”€ CHANGELOG.md            # generated, see versioning.md
â”œâ”€â”€ LICENSE
â”œâ”€â”€ pyproject.toml          # dependencies and all tool config
â”œâ”€â”€ README.md
â””â”€â”€ uv.lock
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
