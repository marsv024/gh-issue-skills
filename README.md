# gh-issue-skills

Agent skills for tracking work in **GitHub Issues + a Projects v2 board**, with RICE scoring.
Pure markdown (`SKILL.md`), no scripts: the skills tell your agent which `gh` commands to run and
discover the repo, board and field IDs themselves, so nothing is hard-coded to one project.

| Skill | Use it to |
|---|---|
| `issue-project-init` | Create the standard labels and a board (Status, Reach, Impact, Confidence, Effort, RICE) for a repo, from scratch or by copying one |
| `issue-search` | Look for an existing issue before filing or starting work |
| `issue-file` | File a bug / feature / tech-debt / epic with a standard body and RICE score |
| `issue-file-found` | File something found mid-task without expanding scope |
| `issue-score` | Compute RICE and record it in the body and the board |
| `issue-plan-doc` | Put a phased, sliceable plan in an epic's issue body |
| `issue-link` | Sub-issues and blocked-by links through the REST API |
| `issue-project-fields` | Add an issue to the board and set Status / RICE fields (also documents ID discovery) |
| `issue-start` | Assign, set In Progress, branch with the issue number |
| `issue-pr-refs` | Pick `Closes` / `Refs` / `Part of` for PR descriptions |
| `issue-close-out` | After merge: verify closure, Status = Done, update epics |
| `issue-audit-scores` | Find issues missing a RICE score |

## Requirements

[`gh`](https://cli.github.com/) logged in with the `repo` and `project` scopes
(`gh auth refresh -s project`). The repo needs a Projects v2 board with a `Status` field
(`Todo` / `In Progress` / `Done`) and number fields `Reach`, `Impact`, `Confidence (%)`,
`Effort (days)`, `RICE`: `issue-project-init` creates them.

## Install

The skills use the open `SKILL.md` Agent Skills format, so they can be copied into any tool that
reads it. Install all of them together (they reference each other).

- **Claude Code (plugin):** `/plugin marketplace add marsv024/gh-issue-skills`, then
  `/plugin install gh-issue-skills@gh-issue-skills`.
- **Claude Code (copy):** copy `skills/*` into `.claude/skills/` (project) or `~/.claude/skills/` (user).
- **Codex CLI:** copy `skills/*` into `~/.codex/skills/` (or the repo-level `.agents/skills/`; check
  the Codex docs for your version).
- **Other agents (Gemini CLI, Cursor, ...):** copy `skills/*` into the tool's skills folder, commonly
  `.agents/skills/`; see the tool's docs.

Then in a repo: ask your agent to run `issue-project-init`, and use the other skills as you work.

## Conventions (opinionated defaults)

- Exactly one type label per issue (`bug`, `feature`, `tech-debt`, `epic`), plus component, topic and
  agent-routing labels (`ai`, `needs-context`, `needs-human`).
- Plan of a non-trivial feature lives in the epic's issue body; slices are sub-issues; ordering uses
  blocked-by links.
- `RICE = (Reach × Impact × Confidence) / Effort`, kept in both the issue body and the board.
- Every PR references an issue; branches carry the issue number.

## Notes

`gh project ...` and `gh label create` print nothing on success, so the skills verify afterwards.
The skills write to GitHub (issues, labels, boards): review what your agent is about to do.

## License

MIT
