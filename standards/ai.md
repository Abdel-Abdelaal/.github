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
|---|---|---|
| CI, on every PR | opencode through platform-workflows (planned) | GitHub Actions secret |
| Locally | opencode CLI in the repo | environment variable in your shell or a git-ignored `.env` |
| Anywhere else (a VPS, a scheduled job) | the same opencode CLI | that machine's environment or secret store |

Keys are always read from the environment, never from a file in the repo. If no key
is set, AI steps **skip with a notice instead of failing**, so forks, outside
contributors and offline work aren't blocked.

## What AI may and may not do

| Allowed | Not allowed |
|---|---|
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

The portable runner is [opencode](https://opencode.ai/docs/). Workflows will use
`AI_MODEL` plus the provider's key environment variable. For a custom compatible
endpoint, use `AI_BASE_URL` and `AI_API_KEY`. Provider and model remain an owner
decision. Read-only review denies writes and arbitrary shell execution. Fix runs
may propose one PR but cannot merge. Fork and Dependabot PRs do not receive keys.
