---
description: Show current Incubator workflow status — phase, active agent, ticket board, open blocks
---

Show the current workflow status.

Instructions for Claude:

Do NOT dispatch any agent. Just read and summarize:

1. Read `STATE.md` — show:
   - Current phase + phase_status
   - Active agent
   - Ticket board counts (Ready / In progress / Blocked / In QA / Closed)
   - Last cycle timestamp + cycle count

2. Read `BLOCKED.md` — if any open entries (without `resolution:` set), list them:
   - ID + title
   - Raised by
   - Question (one line)
   - Recommendation

3. Read the last 5 entries of `REVIEW.md` — show them briefly so Owner sees recent autonomous decisions.

4. Read `OWNER_INBOX.md` — if any unprocessed notes exist, flag them ("Orchestrator hasn't picked these up yet — run `/resume` after saving your note").

Format output as:
```
=== INCUBATOR STATUS ===
Phase: <N> (<status>)
Active agent: <name>
Tickets: <counts>
Last cycle: <timestamp> (cycle #<N>)

Blocks awaiting Owner: <count>
  - BLOCKED-<id>: <title>
    Q: <question>
    Recommend: <option>

Recent autonomous decisions:
  - <timestamp> <agent>: <one-line>
  ...

Unprocessed OWNER_INBOX notes: <count>
```

Do not make recommendations; just report.
