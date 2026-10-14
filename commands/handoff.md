---
description: Start a session from the STATE.md written by /wrap. Read it, report the next action, then do only that.
argument-hint: "[optional note]"
disable-model-invocation: true
---

Pick up the handoff from the last `/wrap`.

Note from the user (may be empty): $ARGUMENTS
It adds emphasis. It does not replace the next action.

1. Find one STATE.md, in this order: a path or package the user names, then
   a package whose files this session edited, then the current directory.
   If more than one exists and the user did not name one, list them and ask.
   If there is none, say so and stop. Do not create STATE.md.
   Suggest `/wrap` when they end this session.
2. Read it fully before exploring the code. Do not re-derive anything listed
   under "Do not re-derive".
3. In 3-5 lines, tell the user: the current focus, the next action, any open
   decisions that block it, and anything in Waiting they must do first.
4. If an open decision blocks the next action, or Waiting says the user must
   do something before that action, tell them and stop. Do not do the action.
   Otherwise do the next action, and only that. Check its result before
   claiming it is done.
5. Do not edit STATE.md during this step; the next `/wrap` updates it.
