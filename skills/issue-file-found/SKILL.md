---
name: issue-file-found
description: File a new bug or tech-debt issue discovered while working on something else, link it, and keep scope from expanding. Use when you hit an unrelated problem mid-task.
---

# File found work, don't expand scope

When you find a new bug or piece of debt while working on another issue:

1. **Do not fix it in the current branch** unless the current issue's work cannot be completed
   without it. Silent scope expansion is not allowed.
2. Run `issue-search`; it may already be filed.
3. File it with `issue-file`: one type label, component labels, a RICE score, and a body that states
   where and how it was found. Mention the current issue as `#n`.
4. Link it to the current issue in a comment on the current issue (`Found while working on this:
   #m`), or with `issue-link` if it blocks the current work.
5. Mention it in your end-of-task summary with its number.

For bugs and debt the body's first step is always: reproduce with a failing test, and trace the
actual call patterns rather than trusting the issue text as a spec.
