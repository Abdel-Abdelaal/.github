# Architecture decisions

Significant decisions are written down as Architecture Decision Records (ADRs), so
that six months later the reason is still there, not just the result.

## When to write one

When a choice is hard to reverse or someone will ask "why did we do it this way?":
a framework, a database, an auth approach, a deploy target, a repo split, a rule in
this handbook.

## Format

[MADR](https://adr.github.io/madr/) (Markdown Architectural Decision Records), stored
in `docs/decisions/NNNN-short-title.md`, numbered in order.

```markdown
---
status: accepted          # proposed | accepted | rejected | deprecated | superseded by 0007
date: 2026-10-24
---

# Use SQLite for the first release

## Context and problem statement

What forces this decision? What problem are we solving?

## Considered options

- SQLite
- PostgreSQL
- Cloudflare D1

## Decision outcome

Chosen option: "SQLite", because ...

### Consequences

- Good: ...
- Bad: ...
```

## Rules

- An ADR is added through a PR like any other change, titled `docs: add ADR 0003 ...`.
- Accepted ADRs aren't edited. A new decision supersedes the old one, and the old one's
  status points at it.
