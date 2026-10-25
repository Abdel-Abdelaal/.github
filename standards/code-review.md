# Code review

Based on [Google's engineering practices](https://google.github.io/eng-practices/review/).
The rule that decides most arguments: approve once the change makes the codebase
better overall, even if it isn't how you'd have written it.

## What to look at, in order

1. **Design.** Does this belong here? Does it fit the rest of the system?
2. **Behaviour.** Does it do what the PR says? Edge cases, errors, concurrency.
3. **Tests.** Would they fail if the code were broken? Do they test behaviour, not
    implementation details?
4. **Complexity.** Could it be simpler? Is anything built "for later" that isn't needed now?
5. **Naming.** Do names say what things are, and match the repo's
    [glossary](python.md#one-name-per-concept)?
6. **Duplication.** Does this repeat logic that already exists somewhere?
7. **Comments and docs.** Do comments explain why? Are docs updated?
8. **Security.** Input validation, secrets, permissions, injection.
9. **Style.** Only what the linters don't already catch. Never argue about formatting:
    the formatter decides.

## Writing comments

Use [Conventional Comments](https://conventionalcomments.org/), so the author knows
how much each comment matters:

```text
<label> [decorations]: <subject>

<optional discussion>
```

| Label | Meaning |
| --- | --- |
| `praise:` | something done well. Leave at least one when it's deserved. |
| `issue:` | a problem that needs fixing |
| `suggestion:` | a specific improvement, ideally with the code |
| `question:` | you're not sure. Asking, not demanding. |
| `nitpick:` | trivial and preference-based. Always non-blocking. |
| `todo:` | a small necessary change |
| `thought:` | an idea for later, not for this PR |
| `note:` | information the author should know |

Decorations: `(blocking)` must be fixed before merge, `(non-blocking)` is optional,
`(security)`, `(performance)` and `(test)` mark the area.

```text
issue (blocking, security): the target URL isn't validated, so `javascript:` links
get stored and served as redirects.

suggestion (non-blocking): `pydantic.HttpUrl` on the request model would handle this
and give a 422 for free.
```

## Etiquette

- Comment on the code, not the person: "this function" rather than "you".
- Explain the reason. "Do X" teaches nothing; "X, because Y" does.
- First response within one working day. Even a short "looking tomorrow" counts.
- AI review comments follow the same format and are always `non-blocking`, unless a
  human agrees and changes the label. See [AI](ai.md).

Frozen pre-commit workflow analysis uses the same medium-or-higher failure threshold
as the hosted security gate, plus strict collection so invalid workflow YAML fails.
Offline mode avoids authenticated API access. Low-severity suggestions remain visible
for review; intentional immutable tooling checkouts are not replaced by dynamic
self-repository code merely to remove a suggestion.
