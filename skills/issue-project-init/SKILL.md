---
name: issue-project-init
description: Bootstrap GitHub issue tracking for a repo: create the standard labels, a Projects v2 board with Status/Reach/Impact/Confidence/Effort/RICE fields (from scratch or copied from a reference board), link it to the repo and document the workflow. Use when setting up issues and a project for a new repo, or when labels/fields drifted.
---

# Initialise issues and project for a repo

Sets up the conventions the other `issue-*` skills assume. Everything here writes to GitHub
(labels, a project, a repo link): state what you will create and get the user's go-ahead first.
Nothing is pushed to git.

Prerequisites: `gh auth status` shows the `repo` and `project` scopes (else
`gh auth refresh -s project`); `jq` is installed.

## 1. Inspect (read-only)

```bash
REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner); OWNER=${REPO%%/*}
gh label list --repo "$REPO" --limit 100
gh project list --owner "$OWNER"
```

## 2. Labels

Create the standard labels below (`name|description|colour`). Add the repo's own **component**
labels (default `backend`, `frontend`; drop or add as fits). Leave GitHub's default labels
(`enhancement`, `documentation`, ...) alone unless asked.

```bash
while IFS='|' read -r n d c; do
  gh label create "$n" --repo "$REPO" --description "$d" --color "$c" --force
done <<'EOF'
bug|Something isn't working|d73a4a
feature|New user-facing capability|a2eeef
tech-debt|Tracked technical debt|fbca04
epic|Multi-issue effort|5319e7
backend|Backend / services|0e8a16
frontend|Frontend / web app|1d76db
performance|Performance|fef2c0
security|Security|d93f0b
testing|Tests / CI / E2E|c5def5
privacy|Privacy / GDPR|bfdadc
ai|Addressable by an AI agent without human intervention|7057ff
needs-context|AI-addressable once a quick check or decision is answered (also carries ai)|fbca04
needs-human|Needs a device, store/legal/owner decision, external access or a roadmap choice|d93f0b
wontfix|This will not be worked on|ffffff
EOF
gh label list --repo "$REPO"      # create is silent on success: verify
```

## 3. Board

**A. Copy a reference board** (fields and views, not items):

```bash
gh project copy <source-number> --source-owner <owner> --target-owner "$OWNER" --title "<Repo name>"
```

**B. From scratch:**

```bash
gh project create --owner "$OWNER" --title "<Repo name>" --format json -q '.number'   # note it as $N
for f in Reach Impact "Confidence (%)" "Effort (days)" RICE; do
  gh project field-create "$N" --owner "$OWNER" --name "$f" --data-type NUMBER
done
```

A new board already has a **Status** field with `Todo`, `In Progress`, `Done`; that is enough
(a `Defer` option can be added in the board's UI).

Then link it and verify:

```bash
gh project link "$N" --owner "$OWNER" --repo "$REPO"
# then follow "Resolve IDs first" in issue-project-fields: it must find the board and all fields
```

## 4. Document it in the repo

Add this section to the repo's agent instructions (`AGENTS.md`, `CLAUDE.md`, or both), filling in
the `<...>` placeholders (repo, board URL, and the component labels you created in step 2; if the
skills come from a plugin, say so and name it). Commit on a branch, not the default branch.

```markdown
## Work Tracking (GitHub Issues)

The backlog lives in **GitHub Issues** (`<owner/repo>`), tracked in the **<board title>** project
(<board URL>). The issue is the source of truth for context *and* status; refer to work by issue
number (`#123`) and labels. Workflow skills: `issue-search`, `issue-file`, `issue-start`,
`issue-pr-refs`, `issue-close-out`, `issue-project-fields`, `issue-score`, `issue-link`,
`issue-plan-doc`, `issue-file-found`, `issue-audit-scores`, `issue-project-init` (<where they come from>).

### Labels

| Group | Labels | Rule |
|---|---|---|
| Type | `bug`, `feature`, `tech-debt`, `epic` | Exactly one per issue |
| Component | <e.g. `backend`, `frontend`> | As many as apply |
| Topic | `performance`, `security`, `testing`, `privacy` | Optional |
| Agent routing | `ai`, `needs-context`, `needs-human` | Optional. `ai` = an agent can do it unattended; `needs-context` = an agent can once a quick question is answered (also carries `ai`); `needs-human` = needs a device, external access or an owner decision |

### Project fields

| Field | Values | Notes |
|---|---|---|
| Status | Todo / In Progress / Done | Set by the orchestrating agent only |
| Reach | 1–5 | How many users it touches |
| Impact | 0.5–3 | Size of the effect |
| Confidence (%) | whole percent, e.g. `70` | |
| Effort (days) | number | |
| RICE | `(Reach × Impact × Confidence) / Effort` | Also written in the body as `**Score:** RICE x = (R × I × C%) / Ed` so the issue reads standalone |

### Issue body and workflow

**Issue body** carries the whole record: Description, Current State, What Needs to Be Done, Why It's
Important, Related Files, estimate. Link related work with `#n`; express ordering with "blocked by"
(body text and the dependencies API, see `issue-link`). Large work is an `epic` whose plan lives in
the issue body and whose slices are sub-issues.

- **Before starting:** search first (`issue-search`); reuse or extend instead of filing duplicates.
- **While working:** set the item to In Progress and assign it (`issue-start`); branch names carry the
  issue number (`feat/123-short-desc`). Bugs and tech debt start with a failing test. File unrelated
  findings as new issues instead of expanding scope (`issue-file-found`).
- **Finishing:** every PR references its issues (`Closes #n` per issue, `Part of #n` for the epic;
  `issue-pr-refs`); after merge set Status = Done and update related issues (`issue-close-out`).
- `gh` needs the `project` scope (`gh auth refresh -s project`). Only the orchestrating agent
  changes issue/project status; delegated sub-agents do not create, edit or close issues.
```

## 5. Optional: seed an epic

`issue-file` (type `epic`) → `issue-plan-doc` → per slice `issue-file` + `issue-link` +
`issue-project-fields` + `issue-score`.

## Gotchas

- `gh label create`, `gh project copy|link|item-edit` are silent on success: always verify.
- A copied board starts empty; `item-add` is idempotent.
- Sub-issue and dependency REST calls take the issue's numeric database id (see `issue-link`).
- Confidence is a whole percent (`70`); IDs differ per board, so always re-discover them (`issue-project-fields`).
