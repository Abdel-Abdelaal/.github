# Contributing

## The flow

Issue → branch `<type>/<description>` → draft PR → green CI → review → squash merge.
Keep each PR focused on one concern and link its issue with `Closes #123`.

## PR titles

Use [Conventional Commits](https://github.com/Abdel-Abdelaal/.github/blob/main/standards/commits.md),
for example `fix: handle expired links`. The subject starts lowercase.

## Local setup

Follow the repository's README and AGENTS.md. For Python repositories, install with
`uv sync`, then `uv run pre-commit install` once the quality tooling is available.
On Windows, `python -m uv` can replace `uv` when it is installed as a Python module.

## Code review

Follow the [review standard](https://github.com/Abdel-Abdelaal/.github/blob/main/standards/code-review.md).
Explain why the change is needed and include reproducible validation in the PR.

## AI assistance

Follow the [AI standard](https://github.com/Abdel-Abdelaal/.github/blob/main/standards/ai.md).
Name the tool in a Co-Authored-By trailer. Automated AI reviews are advisory;
they cannot approve or merge changes.
