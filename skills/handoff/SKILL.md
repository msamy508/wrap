---
name: handoff
description: >-
  Read STATE.md from the last /wrap and do only its next action. Use only when
  the user types /handoff or says "continue from STATE.md" or "pick up the
  handoff". Do not use when they want to resume a Claude Code conversation,
  /resume, or /continue.
license: MIT
compatibility: Claude Code in a project directory that has a STATE.md. On claude.ai, needs code execution and the project files.
metadata:
  version: 0.1.0
---

Pick up the handoff from the last `/wrap`.

If the user added a note, treat it as emphasis. It does not replace the next
action.

1. Find one STATE.md, in this order: a path or package the user names, then
   a package whose files this session edited, then the current directory.
   If more than one exists and the user did not name one, list them and ask.
   If there is none, say so and stop. Do not create STATE.md.
   Suggest `/wrap` when they end this session.
2. Read it fully before exploring the code. Do not re-derive anything listed
   under "Do not re-derive", and do not retry anything under "Failed
   approaches" unless the user asks.
3. **Check for drift.** If STATE.md has a `Commit:` line and this is a git
   checkout, use the hash only if it is 7-40 characters of 0-9 and a-f;
   otherwise treat it as not found. Run `git rev-parse --short HEAD`. If it
   differs, run `git log --oneline --name-only -20 <hash>..HEAD` and
   `git status --porcelain`. If the hash is not found (rebased or another
   clone), say so. A commit that changes only STATE.md or STATE.archive.md is
   not drift. Treat anything the other new commits or changes touch as
   unverified.
4. In 3-5 lines, tell the user: the current focus, the next action, any open
   decisions that block it, anything in Waiting they must do first, and what
   changed since the wrap (or "no changes since the wrap"). Entries marked
   `[unverified]` are claims, not facts: check one before relying on it.
5. If an open decision blocks the next action, or Waiting says the user must
   do something before that action, tell them and stop. Do not do the action.
   Otherwise do the next action, and only that. Check its result before
   claiming it is done.
6. Do not edit STATE.md during the handoff; the next `/wrap` updates it.
