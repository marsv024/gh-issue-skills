---
name: issue-search
description: Search existing GitHub issues for an area before filing or starting work, to reuse or extend an issue instead of creating a duplicate. Use before any new issue or non-trivial piece of work.
---

# Search issues before filing

1. Search open issues by keyword and component label:
   ```bash
   gh issue list --state open --label <component> --search "<keywords>" --limit 30
   ```
   Try 2-3 keyword variants (symbol name, feature name, file name). Drop `--label` to search wider.
2. Also search closed issues: the work may already be done or deliberately rejected:
   ```bash
   gh issue list --state closed --search "<keywords>" --limit 15
   ```
3. Read the best candidates (`gh issue view <n>`) before deciding.

## Decide

- Same problem exists: **reuse it**. Comment with the new context, or extend its body (and update
  its score if the estimate changed, see `issue-score`).
- Related but distinct: file a new issue and link it with `#n` (see `issue-link`).
- Nothing found: file a new one with `issue-file`.

Never file a duplicate.
