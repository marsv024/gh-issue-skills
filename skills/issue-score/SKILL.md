---
name: issue-score
description: Compute a RICE score for a GitHub issue and record it in both the issue body and the Projects v2 fields. Use when scoring a new issue or when an estimate changes.
---

# Score an issue with RICE

`RICE = (Reach × Impact × Confidence) / Effort`, Confidence as a fraction in the formula
(70% → 0.7), Effort in days.

## Scales

| Reach | Meaning |
|-------|---------|
| 1 | Niche: dead code, internal tooling, one rare path |
| 2 | One feature area or a few flows |
| 3 | A core flow, or internals every request touches |
| 4 | Most users, or all users of one client |
| 5 | Every user |

| Impact | Meaning |
|--------|---------|
| 0.5 | Polish, hygiene, docs |
| 1 | Noticeable improvement or moderate risk reduction |
| 1.5 | Significant |
| 2 | Major: correctness, security, blocks a milestone |
| 3 | Massive |

Confidence: 50% = guessing, 70% = reasonable evidence, 90%+ = verified in the code. Effort comes
from the issue's own estimate when it has one.

Worked examples: `(5×2×40%)/0.5d = 8.0`, `(2×0.5×90%)/0.25d = 3.6`, `(4×1×70%)/2d = 1.4`,
`(2×0.5×50%)/1.5d = 0.33`. Calibrate against the repo's existing scores when there are any.

## Record it in two places

1. **Body**, one line in the header block (after `Date Added`):
   ```
   **Score:** RICE 1.4 = (4 × 1 × 70%) / 2d  (Reach 4, Impact 1, Confidence 70%, Effort 2d)
   ```
   Edit with `gh issue edit <n> --body-file <file>` (read the current body first, keep the rest).
2. **Project fields** Reach, Impact, Confidence (%), Effort (days) and RICE, using
   `issue-project-fields`.

Body and project fields must agree. When an estimate changes, update both. State in your summary
that the inputs are estimates.
