---
name: issue-close-out
description: After a PR merges, verify the issue closed, set Status = Done on the Projects v2 board, and update related issues and epics. Use right after merging a PR that closes or partly resolves issues.
---

# Close out after merge

1. Confirm the closing keyword worked:
   ```bash
   gh issue view <n> --json state,stateReason -q '.state'
   ```
   If it is still open although the PR fully resolved it, close it with a comment linking the PR.
2. Set **Status = Done** with `issue-project-fields`, then re-read the item to verify.
   The board does not always do this automatically.
3. **Partly resolved issues:** comment on them with what was done and the PR link. If the work
   changed an estimate or score, update the body and the project fields (`issue-score`).
4. **Epics:** when a slice merges, update its sub-issue. Close the epic only when all sub-issues are
   done (`gh api repos/$REPO/issues/<epic>/sub_issues`, see `issue-link`).
5. Mention any new issues filed along the way (`issue-file-found`) in the summary.
