---
description: End the coding session at a milestone. Update STATE.md, list uncommitted files, and keep secrets out of that file.
argument-hint: "[optional note]"
disable-model-invocation: true
---

Wrap this session. Long sessions drift: context gets compacted, decisions get
lost, and the next session re-derives what this one already knew. A wrap writes
the state down in STATE.md and ends the session at a milestone.

Note from the user (may be empty): $ARGUMENTS
Treat it as what matters most to carry forward.

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
   If you create it, use these sections, in English:
   - A line `Updated: YYYY-MM-DD` and a line `Commit: <hash> on <branch>`.
   - **Current focus**: one or two lines.
   - **Last completed**: what finished, with today's date.
   - **Next action**: the single first step for the next session.
   - **Open decisions**: what waits on the user, with any defaults.
   - **Do not re-derive**: facts corrected or learned this session.
   - **Failed approaches**: what was tried and did not work.
   - **Verification**: check commands with their date and last result.
   - **Waiting**: one bullet `Uncommitted:` and one bullet `For the user:`.

   In a new or existing file, keep these current:
   - **Commit line.** Under `Updated:`, write `Commit: <short hash> on
     <branch>` from `git rev-parse --short HEAD` and `git branch
     --show-current`. Write `on (detached)` if the branch is empty,
     `Commit: none yet` if the repository has no commits, and
     `Commit: no git` outside a repository. The next `/handoff` compares it
     with the checkout to see what changed since the wrap.
   - **Verified or not.** End each new "Last completed" entry with
     `[verified]` if a command run in this session checked it (a test, a
     build, a query, reading the produced file), otherwise `[unverified]`.
     Do not run new commands just to earn the tag.
   - **Failed approaches.** Add what this session tried that did not work,
     why, and what not to repeat. Keep earlier entries while they still apply.
   - **Verification.** The check commands this session ran, each with its
     date and last result. Do not rerun them for the wrap. A check not run
     this session keeps its old date and result.
   If the file has no Failed approaches or Verification section and there is
   something to record, add the section just before Waiting.

   Keep the file under about 150 lines. If it would grow past that, move the
   oldest "Last completed" entries, Failed approaches that no longer apply,
   and old Verification lines into `STATE.archive.md` beside it (newest
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
   holds a secret or a .env value, and run `git commit --no-verify`. That
   skips the pre-commit and commit-msg hooks; tell the user that other hooks
   the repository installed (such as post-commit) can still run. Still do not
   amend or push.

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
   be closed, and the exact text to type next time: the handoff command
   under the name it is installed as (`/handoff`, or `/wrap:handoff` when
   this came from the wrap plugin), otherwise "Read STATE.md and continue". If STATE.md is not in
   the current directory, add its path relative to the repository root, for
   example `/handoff packages/billing/STATE.md`.

Do not start new work after a wrap. If the user asks for something new, say
it belongs to the next session and record it as the next action in STATE.md.
Do the work in this session only if they ask again after that refusal.
