# gh-issue-skills

Pure-markdown agent skills (`skills/<name>/SKILL.md`, frontmatter `name` + `description` only; no
scripts) for a GitHub Issues + Projects v2 workflow. They drive the `gh` CLI and discover the repo,
board and field IDs at runtime, so nothing is hard-coded.

When editing: keep every skill agent-neutral (no tool-specific wording), keep frontmatter to
`name` and `description`, and never hard-code repo names, project numbers or IDs.
