# wrap

`/wrap` ends a Claude Code session at a milestone. `/handoff` starts the next one from where it stopped.

Long agent sessions drift: context gets compacted, decisions get lost, and the next session spends its first hour re-deriving what the last one knew. `/wrap` closes the session on purpose. It writes the current state to `STATE.md`, lists uncommitted work, keeps secrets out of that file, and tells you the one next action.

Claude does not load `STATE.md` by itself. In the next session, type `/handoff`, or paste: "Read STATE.md and continue".

## What's in the repo

```
commands/wrap.md      /wrap as a command file
commands/handoff.md   /handoff as a command file
wrap/                 /wrap as a skill folder (SKILL.md + a STATE.md template and example)
handoff/              /handoff as a skill folder
```

## Install

For `/wrap`, install either the command file or the `wrap/` skill, not both. Do the same for `/handoff`. Command files are the older format; they still work.

You need [Claude Code](https://code.claude.com/docs/en/quickstart).

**Commands (simplest).** Copy `commands/wrap.md` and `commands/handoff.md` to one of:

- macOS or Linux, all projects: `~/.claude/commands/`
- Windows, all projects: `%USERPROFILE%\.claude\commands\`
- one repository, shared with your team: `.claude/commands/`

Start a new Claude Code session afterward. Command files run only when you type them; Claude never starts a wrap on its own.

**Skills.** Copy the `wrap` and `handoff` folders (each contains a `SKILL.md`) to one of:

- macOS or Linux, all projects: `~/.claude/skills/`
- Windows, all projects: `%USERPROFILE%\.claude\skills\`
- one repository: `.claude/skills/`

The folder name must stay `wrap` / `handoff`, matching `name` in `SKILL.md`. If you created the skills folder during a session, run `/reload-skills`. As skills, Claude may also start them when you say "close the session" or "continue from STATE.md".

**claude.ai.** Uploading to claude.ai does not replace the Claude Code install; it makes the skills available in the Claude apps.

- Free, Pro, and Max: turn on **Code execution and file creation** under **Settings > Capabilities**, then open **Customize > Skills**, choose **+**, then **Create skill**, then **Upload a skill**.
- Team: Skills are on by default. Team and Enterprise: if they are off, an owner enables Code execution and Skills under **Organization settings > Plugins & skills** (Policy tab). Members then use **Customize > Skills**.

Zip each skill folder from its parent, so the archive contains `wrap/SKILL.md` or `handoff/SKILL.md`. An archive with `SKILL.md` at its root is rejected.

macOS or Linux, from this repo's root:

    zip -r wrap.zip wrap/
    zip -r handoff.zip handoff/

Windows (PowerShell or cmd), from this repo's root. Use the Windows `tar`, which writes the forward-slash paths the uploader expects (if `tar` resolves to another build, call `C:\Windows\System32\tar.exe`):

    tar.exe -a -c -f wrap.zip wrap
    tar.exe -a -c -f handoff.zip handoff

Check the archive before uploading: its entries must start with `wrap/` or `handoff/`.

On claude.ai the skill has no git checkout unless that chat already has the project files and code execution. The secrets step then reports that no scanner was found.

## Use

    /wrap
    /wrap focus on the migration decision
    /handoff

A good close looks like this:

> Wrap done. Milestone: Parquet writer matches the CSV output (212 tests pass).
> STATE.md updated (`./STATE.md`). Uncommitted: `export/parquet_writer.py`, `tests/test_parquet_writer.py`. No secrets scanner found.
> Next action: run `make nightly-dry` and compare row counts.
> This session can be closed. Next time: `/handoff`.

## What `/wrap` does

1. States the milestone. If something would break by stopping, it asks and waits.
2. Picks one `STATE.md` (the package you worked in, else the current directory) and updates it. An existing file keeps its headings; the six-section shape in `wrap/assets/STATE.template.md` is used only when the file is missing. It keeps the file under about 150 lines by moving old entries to `STATE.archive.md`.
3. Lists uncommitted paths from `git status --porcelain`. It never amends or pushes. It commits only if you explicitly ask in that same turn, only the files you confirm, without running git hooks, and never pushes. Without git it says so and still writes `STATE.md`.
4. Never writes tokens, passwords, keys or personal data into `STATE.md`. Runs gitleaks, trufflehog or detect-secrets on `STATE.md` only if one is already installed on your machine; it never runs scripts from the repository or git hooks.
5. Closes in one short block, in your language, with the single next action.

It does not start new work after the wrap.

## What `/handoff` does

Finds the `STATE.md`, reads it before exploring the code, tells you the focus, the next action and anything blocking it, and then does that one action. If a decision or a step in Waiting blocks it, it stops and asks. It never edits `STATE.md`; the next `/wrap` does.

It is not Claude Code's built-in `/resume`, which reopens an earlier conversation. Use both: `/resume` brings back a chat, `/handoff` brings back the work.

## Before you rely on it

- A skill wins over a command file of the same name. A personal skill wins over a project skill, and an enterprise skill wins over both. Install the copy you want to run.
- `STATE.md` is meant to be read by the next session and often by teammates. Commit it if your team wants a shared handover; add it to `.gitignore` if it is personal.

## Optional hook

Remind each new or resumed session to read `STATE.md`. Claude Code adds the text a `SessionStart` hook prints to Claude's context. Merge this into the `hooks` object in `~/.claude/settings.json` or `.claude/settings.json`; do not replace the whole file. `/wrap` never edits your settings. The `echo` command works in `sh`, Git Bash and PowerShell.

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup|resume|compact",
        "hooks": [{ "type": "command", "command": "echo 'If STATE.md exists in this directory, read it before exploring the code.'" }]
      }
    ]
  }
}
```

## License

[MIT](LICENSE)
