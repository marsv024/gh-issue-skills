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

`references/labels.tsv` lists the standard labels (`name<TAB>description<TAB>colour`). Create the
missing ones; add the repo's own **component** labels (default `backend`, `frontend`; drop or add
as fits). Leave GitHub's default labels (`enhancement`, `documentation`, ...) alone unless asked.

```bash
while IFS=$'\t' read -r n d c; do
  gh label create "$n" --repo "$REPO" --description "$d" --color "$c" --force
done < references/labels.tsv      # path relative to this skill's folder
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

Add a **Work Tracking** section to the repo's agent instructions (`AGENTS.md`, `CLAUDE.md`, or
both) covering: item-type labels (exactly one per issue), component/topic/routing labels, board
fields and the RICE formula, the issue-body format (`issue-file`), epics with sub-issues and plans in
the issue body (`issue-plan-doc`), PR references (`issue-pr-refs`), and "only the orchestrating
agent edits issues and the board". Commit on a branch, not the default branch.

## 5. Optional: seed an epic

`issue-file` (type `epic`) → `issue-plan-doc` → per slice `issue-file` + `issue-link` +
`issue-project-fields` + `issue-score`.

## Gotchas

- `gh label create`, `gh project copy|link|item-edit` are silent on success: always verify.
- A copied board starts empty; `item-add` is idempotent.
- Sub-issue and dependency REST calls take the issue's numeric database id (see `issue-link`).
- Confidence is a whole percent (`70`); IDs differ per board, so always re-discover them (`issue-project-fields`).
