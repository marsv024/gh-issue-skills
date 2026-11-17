---
name: issue-audit-scores
description: List open GitHub issues that are missing a RICE score, either in the body or in the Projects v2 fields. Use to find unscored backlog items, then score them with issue-score.
---

# Audit missing RICE scores

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

Dump the board items (field keys come back lowercased: `rICE`, `reach`, `impact`,
`confidence (%)`, `effort (days)`):

```bash
gh project item-list "$PROJECT" --owner "$OWNER" --limit 400 --format json > /tmp/items.json
```

Report two gaps:

```python
import json
d = json.load(open('/tmp/items.json'))['items']
fields = ['rICE', 'reach', 'impact', 'confidence (%)', 'effort (days)']
missing_fields = [i for i in d if any(i.get(k) is None for k in fields)]
no_body_score = [i for i in d if '**Score:**' not in (i['content'].get('body') or '')]
```

For each hit print number, labels and title.

## Triage

- **Skip epics** whose body says "options analysis only" or "no sub-issues yet", and epics that are
  split or purely historical. Mention them in the summary instead.
- **Aggregate epics** (all sub-issues already scored): ask before adding a roll-up score.
- **Score the rest** with `issue-score`. Read each issue body first; do not score from the title.
- If only the board's RICE field is empty but the other fields are set, just compute and set RICE.

After applying, re-run the dump and confirm only the intentionally skipped issues remain.
