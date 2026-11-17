---
name: issue-link
description: Link GitHub issues to each other as sub-issues of an epic or with blocked-by ordering, using the GitHub REST API. Use when creating epics with sub-issues or expressing issue ordering.
---

# Link issues

The REST endpoints take an issue's numeric **database id** (the `id` field), not its `#number`:

```bash
REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner)
gh api repos/$REPO/issues/<n> --jq .id
```

## Sub-issue of an epic

```bash
# list
gh api repos/$REPO/issues/<epic>/sub_issues --jq '.[].number'
# add
gh api -X POST repos/$REPO/issues/<epic>/sub_issues -F sub_issue_id=<child-db-id>
```

## Blocked by

```bash
# list what <n> is blocked by
gh api repos/$REPO/issues/<n>/dependencies/blocked_by --jq '.[].number'
# add: <n> is blocked by <blocker>
gh api -X POST repos/$REPO/issues/<n>/dependencies/blocked_by -F issue_id=<blocker-db-id>
```

## Also

- Mention the relationship in the body with `#n` (and "blocked by #n") so the issue reads standalone.
- Close an epic only when all its sub-issues are done. When a slice merges, update its sub-issue
  (see `issue-close-out`).
- Epics can be created with no sub-issues; add them only once an option is scoped enough to build.
