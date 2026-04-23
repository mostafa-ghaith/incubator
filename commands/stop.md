---
description: Stop the Incubator workflow cleanly at the next handoff
---

Request a clean stop of the workflow.

Instructions for Claude:

1. Write the following to `OWNER_INBOX.md`:
   ```
   STOP_WORKFLOW: Owner requested clean stop at <ISO timestamp>. Finish current ticket's handoff, write final STATE.md summary, do not start new work.
   ```

2. Do NOT force-kill. The Orchestrator checks OWNER_INBOX.md at the start of every cycle and will honor the stop after the in-flight agent finishes its current turn.

3. Tell the Owner:
   > "Stop requested. Workflow will finish the current agent's turn then halt. Run `/status` to confirm and `/start-workflow` or `/resume` to restart."
