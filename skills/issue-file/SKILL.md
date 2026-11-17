---
name: issue-file
description: File a new GitHub issue (bug, feature, tech-debt or epic) with the project's labels, body format, RICE score and Projects v2 fields. Use when work needs to be tracked and no existing issue covers it.
---

# File an issue

Run `issue-search` first.

## Labels

- **Exactly one type:** `bug`, `feature`, `tech-debt`, `epic`.
- **Component (as many as apply):** the repo's own components, e.g. `backend`, `frontend`.
- **Topic (optional):** `performance`, `security`, `testing`, `privacy`.
- **Agent routing (optional):** `ai` (an agent can do it unattended), `needs-context` (an agent can
  do it once a quick question is answered; also carries `ai`), `needs-human` (needs a device,
  external access or an owner decision).

(`issue-project-init` creates these labels.)

## Body

Write it to a temp file, then pass `--body-file`. It must read standalone:

```markdown
**Category:** <e.g. Correctness / Performance / Privacy>
**Component:** <module, class or path>
**Date Added:** <YYYY-MM-DD> (<how it was found>)
**Score:** RICE <x> = (<R> × <I> × <C>%) / <E>d  (Reach <R>, Impact <I>, Confidence <C>%, Effort <E>d)
**Estimated Effort:** <E> days

**Description:**
<what is wrong or missing, with evidence>

**Current State:**
<how it behaves today>

**What Needs to Be Done:**
<approach. For bugs and tech debt, start with: reproduce with a failing test first>

**Why It's Important:**
<impact>

**Related Files:**
- `path/to/file`
```

Link related work with `#n`; express ordering as "blocked by #n" (and use `issue-link`).

## Create and register

```bash
URL=$(gh issue create --title "<title>" --label <type>,<components> --body-file <file>)
```

Then set the project fields (Status = Todo, Reach, Impact, Confidence, Effort, RICE) with
`issue-project-fields`; work out the numbers with `issue-score`.

An `epic` may legitimately have no sub-issues and no score yet; create sub-issues only once an
option is scoped enough to build (see `issue-plan-doc`).
