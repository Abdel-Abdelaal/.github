# Languages

Every repository gets checks for the languages it actually contains. Nobody
maintains a list per repo: the shared workflows detect languages on every run
(see [platform docs](https://github.com/Abdel-Abdelaal/platform-workflows/blob/main/docs/languages.md)).
A language counts once it's at least 1% of a repository's code, by GitHub's own
language statistics, which already exclude vendored and generated code.

## What checks which language

| Language | Lint and format | Types | Tests | Code security | Dependencies |
| --- | --- | --- | --- | --- | --- |
| Python | Ruff (lint + format) | Pyright strict | pytest + coverage ≥90%, Hypothesis | CodeQL `python` | pip-audit, OSV, Dependabot `uv`/`pip` |
| TypeScript / JavaScript | ESLint + Prettier | the project's `typecheck` script (`tsc`) | the project's `test` script | CodeQL `javascript-typescript` | npm/pnpm/yarn audit, Dependabot `npm` |
| Kotlin / Java (Android) | ktlint | compiler (Gradle build) | Gradle tests | CodeQL `java-kotlin` | Gradle wrapper validation, Dependabot `gradle` |
| Shell | ShellCheck, shfmt | — | — | — | — |
| Terraform | `terraform fmt`, tflint | `terraform validate` | — | Trivy config scan | Dependabot `terraform` |
| Dockerfile | hadolint | — | container smoke test where a service ships | Trivy image scan | digest pins, Dependabot `docker` |
| YAML, Markdown, JSON | yamllint, markdownlint, Prettier | — | — | — | — |
| GitHub Actions | actionlint | — | — | zizmor, CodeQL `actions` | SHA pins, 14-day cooldown, Dependabot `github-actions` |
| C / C++, Swift, Go, Rust | the language's linter in super-linter | — | — | **not automatic:** reported as "not scanned" with the reason | OSV, Dependabot where a manifest exists |

The lint column is one gate, `lint.yml` ([super-linter](https://github.com/super-linter/super-linter)).
It picks the linter per file type, so a repository written in a new language is
linted from its first PR. Python repositories also run `python-ci.yml`, whose
stricter Ruff and Pyright settings are the ones that count; `lint.yml` doesn't run
competing Python linters.

## Rules

- **Pull requests lint the files they change.** Inherited or vendored code doesn't
  block new work. A scheduled full scan reports the whole backlog without failing,
  so it can be paid down deliberately.
- **Use the project's own scripts** (`lint`, `typecheck`, `test`) for JavaScript and
  TypeScript. The shared workflow runs whichever are defined and says in the job
  summary which were missing.
- **Installs are reproducible.** A lockfile is required, and dependency install
  scripts are off unless a repository opts in.
- **Gaps are visible.** A language CodeQL can't analyse automatically is listed in
  the job summary with the reason, never dropped silently.
- **Adding a language** to the fleet means: add its row here, add or extend a
  reusable workflow in platform-workflows, and add its CodeQL mapping only after a
  real run proves it works.
