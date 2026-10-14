# Working on wrap

This repo ships two agent skills, `wrap` and `handoff`, as a [Skills CLI](https://github.com/vercel-labs/skills)
package and as a Claude Code plugin. Read this before you change anything, and point your agent at it.

## Vocabulary

- **Wrap**: ending a session at a milestone by writing `STATE.md`. Not "summary", not "checkpoint".
- **Handoff**: starting the next session from `STATE.md`. Not "resume"; `/resume` is a different,
  built-in Claude Code command that reopens a conversation.
- **Next action**: the single first step for the next session. One, never a list.
- **Drift**: commits or uncommitted changes made after the commit recorded in `STATE.md`.

## Conventions

- Each skill exists twice: `skills/<name>/SKILL.md` and `commands/<name>.md`. Their steps must say the
  same thing. The command file inlines the section list because it cannot ship the template.
- Skills are Markdown only. Do not add scripts, dependencies, or network calls.
- The skills may run only `git status`, `git rev-parse`, `git branch`, `git log`, and a secrets
  scanner (`gitleaks`, `trufflehog`, `detect-secrets`) already on PATH. Never add a step that runs a
  script from the user's repository, a git hook, or a push.
- `/wrap` writes only `STATE.md` and `STATE.archive.md`. `/handoff` writes nothing to `STATE.md`.
- Frontmatter keys allowed by claude.ai upload: `name`, `description`, `license`, `compatibility`,
  `metadata`, `allowed-tools`. Do not add others.
- Plain words. A term the reader may not know gets a short gloss the first time.

## Before publishing a change

1. Both copies of each changed skill updated.
2. `metadata.version` in both `SKILL.md` files, `version` in `.claude-plugin/plugin.json`, and the
   plugin entry in `.claude-plugin/marketplace.json` all equal.
3. `npx skills add . --list` lists `wrap` and `handoff`.
4. `claude plugin validate . --strict` passes.
5. The changed skill was run in a real session, or the pull request says it was not.
