---
name: wrap
description: >-
  Write STATE.md and stop a coding session at a milestone. Use only when the
  user is ending this session: /wrap, "close the session", "end the session",
  "ready to compact", or "hand this session over". Do not use for wrapping a
  function, wrapping text, finishing one task, or any request to keep working.
license: MIT
compatibility: Claude Code in a project directory. Uses git when the folder is a repository. On claude.ai, needs code execution and the project files.
---

Wrap this session. Long sessions drift: context gets compacted, decisions get
lost, and the next session re-derives what this one already knew. A wrap writes
the state down in STATE.md and ends the session at a milestone.

If the user added a note after the command, treat it as what matters most to
carry forward.

## Steps

1. **Detect the milestone.** If the user is not ending the session (they asked
   to wrap a function, wrap text, finish one task, or keep working), say this
   does not apply and stop. Do not write STATE.md.
   Otherwise state in 2-3 lines what was completed, what was decided, and what
   is mid-flight. If something mid-flight would be damaged by stopping (failing
   checks, a half-written change, a migration in progress), say so and ask
   whether to finish it first. Then stop this turn. Do not write STATE.md or
   run later steps until the user answers. If they say to finish the work
   first, do that and do not write STATE.md until they ask to wrap again.
   If they say to wrap anyway, continue at step 2.

2. **Choose one STATE.md, then update that file only.** Prefer, in order: the
   package the user named, the package whose files this session edited, then
   the current directory. If none of those already has a STATE.md, create it
   in the package that was edited, or in the current directory if no package
   was edited. Never create a second file at the repository root.
   If the file exists, edit it in place. Keep its headings and their order.
   Rewrite the text under a heading when that fact changed. Add a section only
   when the file has nowhere to put that fact. Do not replace the file with
   the template.
   If you create it, copy `assets/STATE.template.md` (keep the headings in
   English); `assets/STATE.example.md` shows a filled file.

   Keep the file under about 150 lines. If it would grow past that, move the
   oldest "Last completed" entries into `STATE.archive.md` beside it (newest
   first) and leave a one-line pointer. If `STATE.archive.md` already exists,
   add to it; do not overwrite it. STATE.md holds the current state, not the
   whole history, so the next session reads it INSTEAD of re-deriving context.

3. **Sweep the loose ends.** Run `git status --porcelain`. If it fails because
   this folder is not a git checkout, say so in the close and write
   `Uncommitted: no git` in Waiting. If it succeeds, list every uncommitted and
   untracked path in Waiting, and record the directory you ran it from,
   relative to the repository root (never an absolute path).
   Do not stage, commit, amend, or push. If the user explicitly asks to commit
   in this turn, stage only the paths the user confirms, never a file that
   holds a secret or a .env value, and run `git commit --no-verify` so no git
   hook runs. Still do not amend or push.

4. **Check secrets.** STATE.md is copied into reviews and later chats. Never
   write a token, password, key, connection string, cookie, or .env value into
   it, and do not paste a diff hunk that contains one. Name the file that holds
   it instead. Leave out private customer or personal data the same way.
   Run only a `gitleaks`, `trufflehog`, or `detect-secrets` program that is
   already installed on PATH (use the repo's config for it if there is one).
   Never execute a script or binary from the repository, and do not run any
   other tool. If one of those scanners is installed, run it on STATE.md only
   and quote its verdict in the close. If you do not, say
   "No secrets scanner found" in the close. Do not write that sentence into
   STATE.md. Do not run pre-commit, husky, lint-staged, or any git hook.

5. **Close.** Tell the user the wrap is done in ONE short block, in the
   language they have been using: milestone reached, STATE.md updated (with
   its path), what is uncommitted, and the single next action. Keep the
   filename STATE.md in Latin letters. End with one line that this session can
   be closed, and the exact text to type next time: `/handoff` if it is
   installed, otherwise "Read STATE.md and continue". If STATE.md is not in
   the current directory, add its path relative to the repository root, for
   example `/handoff packages/billing/STATE.md`.

Do not start new work after a wrap. If the user asks for something new, say
it belongs to the next session and record it as the next action in STATE.md.
Do the work in this session only if they ask again after that refusal.
