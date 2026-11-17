---
name: issue-project-fields
description: Add a GitHub issue to the repo's Projects v2 board and set its Status, Reach, Impact, Confidence, Effort and RICE fields, with all project/field/option IDs discovered at runtime. Use whenever an issue's project fields need to be set or changed.
---

# Set Projects v2 fields

Required board fields: **Status** (single select with `Todo`, `In Progress`, `Done`) and number
fields **Reach**, **Impact**, **Confidence (%)**, **Effort (days)**, **RICE**
(`issue-project-init` creates them).

## Resolve IDs first (nothing is hard-coded)

Run these in the repo's directory (see `issue-project-fields` for details):

```bash
REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner); OWNER=${REPO%%/*}
PROJECT=$(gh api graphql -f query='query($o:String!,$n:String!){repository(owner:$o,name:$n){projectsV2(first:20){nodes{number closed}}}}' \
  -f o="$OWNER" -f n="${REPO##*/}" -q '[.data.repository.projectsV2.nodes[] | select(.closed | not)] | .[0].number')
PROJECT_ID=$(gh project view "$PROJECT" --owner "$OWNER" --format json -q .id)
gh project field-list "$PROJECT" --owner "$OWNER" --format json --limit 100
```

`$PROJECT` is the first open board linked to the repo; if the user names a different board, use
that number. If none is linked, run `issue-project-init`. From the `field-list` output read the
`id` of the fields **Status**, **RICE**, **Reach**, **Impact**, **Confidence (%)** and
**Effort (days)**, and the option ids of **Status** = `Todo`, `In Progress`, `Done`. Use them
wherever a command below says `<field-id>` or `<option-id>`.

If a required field or option is missing, run `issue-project-init` to add it.

## Steps

1. Get the project item id. `item-add` is safe on an issue already on the board and returns its
   existing item id:
   ```bash
   ITEM=$(gh project item-add "$PROJECT" --owner "$OWNER" --url <issue-url> --format json -q .id)
   ```
2. Set a number field (Reach, Impact, Confidence, Effort, RICE):
   ```bash
   gh project item-edit --project-id "$PROJECT_ID" --id "$ITEM" --field-id <field-id> --number <value>
   ```
3. Set Status (the option id of `Todo`, `In Progress` or `Done`):
   ```bash
   gh project item-edit --project-id "$PROJECT_ID" --id "$ITEM" \
     --field-id <status-field-id> --single-select-option-id <option-id>
   ```
4. Confidence is stored as a whole percent (`70`, not `0.7`). Effort is in days. RICE is a plain
   number rounded to 1-2 decimals.

If `gh` reports a missing scope: `gh auth refresh -s project`. `gh project item-edit` is silent on
success; re-read the item to verify (`gh project item-list "$PROJECT" --owner "$OWNER" --format json`).

To compute RICE and write the body line, use `issue-score`. Only the orchestrating agent edits
project fields; delegated sub-agents never do.
