---
name: issue-plan-doc
description: Write the plan for a non-trivial feature or epic into the GitHub issue body, split into independently mergeable slices. Use when an issue needs a persisted plan.
---

# Plan in the issue

Non-trivial features and epics keep their plan in the **issue body**, under a `# Plan` heading (or
in the epic's description) below the standard record, not in a repo document.

## Rules

- Divide the plan by **independently shippable phase**.
- Each phase is divided into the **smallest independently mergeable slices**. Each slice lists:
  its own tests, acceptance criteria, dependencies, and the agent/role that implements it.
- Bug-fix slices need captured red/green test-run evidence, not just a "TDD" bullet.
- New packages/components ship a complete test set.
- **Status is not tracked in the plan text.** It lives in the sub-issues (one per slice).
- Mermaid diagrams render in issues.

## Steps

1. Write the plan into the issue body (`gh issue edit <n> --body-file <file>`).
2. If the body nears GitHub's 65k character limit, split phases into sub-issues instead of comments.
3. Create sub-issues for slices only once an option is scoped enough to build, link them with
   `issue-link`, and add them to the board with `issue-project-fields`.

If the request was "scope this down and persist a plan", write the plan into the issue and stop;
do not continue into implementation.
