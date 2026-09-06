---
name: handoff
description: >
  Generate a session handoff document summarizing work done, current state,
  and next steps for whoever continues (person or agent).
  Trigger: /handoff.
---

# Handoff

Produce a handoff document from the current conversation and repo state, then write it to `HANDOFF.md` in the project root (overwrite if it already exists — this is a snapshot, not a log).

## Gather

1. Repo state:
   - If a git repo: `git status`, `git diff` (staged + unstaged), `git log -5 --oneline`.
   - If no git repo (check with `git rev-parse --is-inside-work-tree`): list files modified in this session instead (from tool-call history), with no git commands.
2. Conversation state: what was asked, what was done, what's unresolved, any decisions made (and why), any assumptions taken.
3. Known blockers or open questions the user hasn't answered yet.

## Write `HANDOFF.md`

Structure:

```markdown
# Handoff — <one-line task summary>

Date: <YYYY-MM-DD>

## Done
- <concrete change 1, with file:line if relevant>
- <concrete change 2>

## In progress / not done
- <what's left, why it's not done>

## Decisions
- <decision> — <why>

## Next steps
1. <concrete next action>
2. ...

## Notes
- <blockers, gotchas, things the next person should know that aren't obvious from the diff>
```

Rules:
- Concrete over vague: cite file paths and line numbers, not "updated some files."
- Skip empty sections rather than writing "None."
- Don't include unrelated project boilerplate already covered by CLAUDE.md — this doc is about the session's delta, not the whole project.
- After writing, tell the user the file path and give a 2-3 line spoken summary — don't restate the whole document in chat.

## Language

Write `HANDOFF.md` in the user's dominant language for this session (the language they've been typing in), not necessarily English. Keep code, file paths, commands, and error strings verbatim regardless of language. If the user explicitly asks for a specific language, use that instead.
