---
description: Show recent autonomous decisions (REVIEW.md) so Owner can audit drift
---

Show the Owner recent autonomous decisions taken by agents without escalation.

Instructions for Claude:

Do NOT dispatch any agent. Read `REVIEW.md` and show:

1. The last 30 entries (or all if fewer), newest first
2. Group by agent so Owner can see which roles made which calls
3. For each entry, show: timestamp, agent, one-line summary, why, reversible-by

At the end, remind the Owner:
> "Mark entries `[OK]` (prefix) if you've reviewed and accepted them, or `[REVERT]` if you want to undo. Agents will see these marks on their next turn."
