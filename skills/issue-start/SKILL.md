---
name: issue-start
description: Start work on a GitHub issue: assign it, move it to In Progress on the Projects v2 board, and create a branch named with the issue number. Use at the beginning of any issue's implementation.
---

# Start an issue

Never commit to the default branch directly.

1. Read the issue (`gh issue view <n>`) and run `issue-search` if you suspect overlap.
2. Assign yourself:
   ```bash
   gh issue edit <n> --add-assignee @me
   ```
3. Set **Status = In Progress** with `issue-project-fields` (Status option `In Progress`).
4. Branch from the up-to-date default branch, with the issue number in the name:
   ```bash
   git checkout <default-branch> && git pull
   git checkout -b fix/<n>-short-desc     # or feat/<n>-..., chore/<n>-..., docs/<n>-..., refactor/<n>-...
   ```
5. For bugs and tech debt, write the failing test that reproduces the real problem first.

Only the orchestrating agent changes issue and project status. Delegated sub-agents never create,
edit or close issues; say so in their prompt.
