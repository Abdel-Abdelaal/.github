# Issues

Every piece of work starts as an issue, so there's a record of why it was done. The
PR then links back with `Closes #N`.

## Types

Issue forms (`.github/ISSUE_TEMPLATE/`) give each type required fields. Blank issues
are off.

| Form | For | Must include |
| --- | --- | --- |
| Bug | something broken | what happened, what was expected, steps to reproduce, version |
| Feature | new behaviour | the problem it solves, proposed solution, acceptance criteria |
| Task | refactors, chores, tech debt | goal, acceptance criteria |

Security problems are never filed as public issues. See [SECURITY.md](../SECURITY.md).

## Writing a good ticket

- **Title:** the outcome, not the activity. "Expired links return 410" rather than
  "Look into expiry".
- **Acceptance criteria:** testable statements, in Given / When / Then form where it
  helps. They become the tests.

  ```
  Given a link that expired yesterday
  When someone requests its short code
  Then the API returns 410 Gone
  ```

- **Scope:** small enough to finish in one PR. Bigger work becomes a parent issue
  with sub-issues.

## Labels

Defined once in [`labels.yml`](../.github/labels.yml). Terraform in
`github-governance` reads a reviewed source revision. Newly added labels remain
desired state until its configured apply; workflows must skip or dry-run honestly
when required labels are absent. Baseline label files are reference copies.

| Group | Labels |
| --- | --- |
| Type | `bug`, `enhancement`, `task`, `documentation`, `security`, `dependencies` |
| Priority | `priority: critical`, `priority: high`, `priority: medium`, `priority: low` |
| Status | `needs triage`, `blocked`, `help wanted`, `good first issue` |
| Resolution | `duplicate`, `wontfix`, `invalid` |
| Automation | `stale`, `autorelease: pending`, `autorelease: tagged` |

New issues get `needs triage` from their form. Triage means: confirm it's real, set a
priority, remove `needs triage`.
