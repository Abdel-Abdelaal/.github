# Python

The base is [PEP 8](https://peps.python.org/pep-0008/) and the
[Google Python Style Guide](https://google.github.io/styleguide/pyguide.html).
[Ruff](https://docs.astral.sh/ruff/) enforces most of it and fixes what it can on save
and in pre-commit. Everything in a "How it's enforced" line fails CI.

## Tooling

| Tool | Job |
| --- | --- |
| `uv` | Python version, dependencies, lockfile (`uv.lock` is committed) |
| `ruff format` | formatting. Not up for discussion in review. |
| `ruff check` | linting: style, bugs, naming, complexity, imports, docstrings |
| `pyright` (strict) | type checking |
| `pytest` + `coverage` | tests, with a minimum coverage threshold |
| SonarQube Cloud | duplication, code smells, maintainability rating on new code |

## Naming

| Thing | Style | Example |
| --- | --- | --- |
| module, package | `snake_case`, short | `links.py` |
| function, method, variable | `snake_case` | `create_link` |
| class, exception, type alias | `PascalCase` | `LinkRepository`, `LinkExpiredError` |
| constant | `UPPER_SNAKE_CASE`, module level | `MAX_CODE_LENGTH` |
| private | leading underscore | `_hash_url` |
| boolean | reads as a yes/no question | `is_expired`, `has_owner` |

- Names say what something **is**, not its type: `links`, not `link_list`.
- No single letters except `i`/`j` in short loops and `_` for ignored values.
- No abbreviations unless they're more familiar than the full word (`url`, `id`, `db`).
- Functions are verbs (`delete_link`), everything else is a noun.

**How it's enforced:** ruff `N` (pep8-naming) rules. The rest in review.

### One name per concept

Each repo keeps a **glossary** in its `AGENTS.md`: one name for each domain concept,
used everywhere (variables, columns, API fields, docs). If the glossary says
`target_url`, then nothing is called `long_url`, `destination` or `original`.
Renaming a concept means renaming it everywhere in one PR.

**How it's enforced:** review, both human and AI. The AI reviewer is given the glossary.

## Comments and docstrings

- Comments explain **why**: a constraint, a trade-off, a non-obvious bug being avoided.
  If a comment explains what the code does, rename things until it doesn't need to.
- No commented-out code. Git remembers it.
- `TODO` comments link an issue: `# TODO(#42): drop once clients are on v2`.
- Every public module, class and function has a docstring in
  [Google style](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings):

  ```python
  def create_link(target_url: str, code: str | None = None) -> Link:
      """Store a new short link.

      Args:
          target_url: Where the short code redirects to.
          code: Custom short code. Generated if omitted.

      Returns:
          The stored link.

      Raises:
          CodeTakenError: If `code` is already in use.
      """
  ```

**How it's enforced:** ruff `D` (pydocstyle, Google convention), `ERA` (commented-out
code), `TD` (TODO format).

## Types

- Every function signature is fully typed. No implicit `Any`.
- Use built-in generics and `|` (`list[str]`, `str | None`), not `typing.List` or `Optional`.
- Data crossing a boundary (API, DB, config) is a Pydantic model or a dataclass, not a dict.

**How it's enforced:** pyright strict, ruff `ANN` and `UP`.

## No duplication

- Logic that appears twice is a warning. Three times, extract it.
- Two pieces of code that look the same but change for different reasons stay
  separate. That's coincidence, not duplication.
- Magic numbers and strings become named constants.

**How it's enforced:** SonarQube Cloud quality gate (at most 3% duplicated lines in new
code), ruff `PLR2004` (magic values), `SIM` (simplifiable code).

## Size and complexity

| Limit | Value | Rule |
| --- | --- | --- |
| cyclomatic complexity per function | 10 | ruff `C901` |
| arguments per function | 5 | ruff `PLR0913` |
| branches per function | 12 | ruff `PLR0912` |
| line length | 88 | ruff formatter |

## Errors

- Raise specific exceptions defined in the repo (`LinkNotFoundError`), not bare
  `Exception` or `ValueError` for domain errors.
- Never `except:` or `except Exception: pass`. Catch what you can handle, let the rest
  propagate.
- Translate domain errors to HTTP responses in one place (FastAPI exception handlers),
  not in every route.

**How it's enforced:** ruff `BLE` (blind except), `E722`, `TRY`, `B`.

## Imports

Absolute imports, sorted by ruff (`I`): standard library, third party, local. No
wildcard imports.
