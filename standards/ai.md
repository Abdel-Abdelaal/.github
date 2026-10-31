# AI

AI tools are used for review, refactoring suggestions and drafting. They work from the
same written rules as people, they run anywhere an API key is available, and they
never get the final say.

## Same rules everywhere

The rules an AI follows aren't buried in a prompt inside one workflow. They are:

1. **This handbook**, for everything general.
2. **`AGENTS.md`** in each repo ([agents.md](https://agents.md/) format, read by most
    coding agents): how to install, test and lint, the layout, the domain glossary, and
    anything specific to that repo. Tools that look for their own file name (such as
    `CLAUDE.md`) get a one-line file that points at `AGENTS.md`.

So the AI reviewer in CI, an agent on a laptop and a new human contributor all
read the same instructions.

## Where it runs

| Where | How | Key comes from |
| --- | --- | --- |
| CI, on every PR | isolated opencode through platform-workflows | GitHub Actions secret |
| Locally | opencode CLI in the repo | environment variable in your shell or a git-ignored `.env` |
| Anywhere else (a VPS, a scheduled job) | the same opencode CLI | that machine's environment or secret store |

Keys are always read from the environment, never from a file in the repo. If no key
is set, AI steps **skip with a notice instead of failing**, so forks, outside
contributors and offline work aren't blocked.

## What AI may and may not do

| Allowed | Not allowed |
| --- | --- |
| comment on PRs, using [Conventional Comments](code-review.md#writing-comments), marked `non-blocking` | approve PRs or count towards required reviews |
| suggest changes as review suggestions | push to `main` or bypass any branch rule |
| open its own PRs, which then go through the same checks and review | merge anything |
| draft issues, docs and ADRs for a human to edit | read or print secrets |

AI-written commits carry a `Co-Authored-By:` trailer naming the tool.

## Why AI checks are advisory

Linters, type checks, tests and scanners are deterministic: the same code gives the
same result every time, so they can block a merge. AI review isn't deterministic, so
it can inform a decision but not make one. A human can turn an AI comment into a
blocking one by agreeing with it.

## Runner configuration

The runner uses locked [OpenCode](https://opencode.ai/docs/) and Xiaomi MiMo
2.6 Flash (`mimo/mimo-v2.6-flash`). `AI_API_KEY` is the named secret;
`AI_BASE_URL` is an optional repository variable (the Amsterdam Token Plan endpoint
is the reviewed default). Both primary and auxiliary models are fixed to Flash.

All model tools are denied. Trusted code attaches a bounded snapshot of tracked
source, the diff and glossary as untrusted data. The model does not need filesystem
or network tools to read that attachment. Project plugins, external skills and
agent configuration are disabled in a clean temporary directory. GitHub/App tokens
never enter the provider process. Source is secret-scanned before sending context.

The provider's own model key is the one exception, here and in agent-dispatcher
runs: the provider CLI needs it to call the model, so it is in that process's
environment. Use a key that can only call models, and no other credential.

A separate publisher validates bounded JSON, verifies added-line locations and
updates one sticky comment plus a neutral check. It creates no blocking review
threads. Model suggestions can be wrong; an operator verifies them against source.
Malformed output fails visibly after bounded attempts without printing raw output.

Weekly audits update one health issue. Fix and monthly standards workflows need the
automation App and otherwise skip explicitly. Validated patches have strict file,
size and symlink limits, run tests without provider/App credentials and become draft
PRs through a separate App job. They never approve or merge.

See [the implemented boundary and its tests](https://github.com/Abdel-Abdelaal/platform-workflows/blob/main/docs/ai.md).

## Existing review bots, and why this one is custom

Off-the-shelf AI reviewers exist, and they're the default choice wherever they fit:

| Tool | Model and key | Cost for this account | Fit |
| --- | --- | --- | --- |
| Cursor Bugbot | Cursor's models | needs a paid Cursor plan | not used: can't run on our own key |
| CodeRabbit | CodeRabbit's models | free for public repos; needs a GitHub App install by the owner | candidate; owner decision |
| GitHub Copilot code review | GitHub's models | needs a Copilot plan | not used |
| [PR-Agent](https://github.com/The-PR-Agent/pr-agent) (open source) | any provider, including an OpenAI-compatible endpoint | free; uses our MiMo key | trial planned as a second advisory reviewer on shortlink |

The platform runner exists because none of these keep GitHub tokens and the model in
separate processes. The trial result is recorded as an ADR in platform-workflows,
and whichever reviewer finds more real issues stays.
