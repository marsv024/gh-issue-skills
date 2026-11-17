---
name: issue-pr-refs
description: Choose the correct issue-reference keywords (Closes, Refs, Part of) for a pull request description. Use when writing or checking any PR body; every PR must reference an issue.
---

# Issue references in PRs

**Every PR references at least one issue**, including docs-only and chore PRs. If none exists, file
one first (`issue-file`).

| Situation | Write |
|-----------|-------|
| PR fully resolves the issue | `Closes #123` |
| PR resolves several issues | one keyword per issue: `Closes #123`, `Closes #124` |
| PR only partly addresses it, or is related (a slice of a plan, the parent epic) | `Refs #123` or `Part of #123` |

Notes:

- `Closes #1, #2` only closes #1; repeat the keyword for each issue.
- A slice of an epic uses `Part of #<epic>` plus `Closes #<slice>` if a sub-issue exists.
- Put the references in the PR description. Verify afterwards:
  `gh pr view --json body -q .body | grep -E 'Closes|Refs|Part of'`.
- Branch names carry the issue number too (`issue-start`).

After merge, follow `issue-close-out`.
