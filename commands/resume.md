---
description: Resume the Incubator workflow after answering BLOCKED.md or leaving a note in OWNER_INBOX.md
---

Resume the Incubator workflow.

Instructions for Claude:

1. Confirm that `STATE.md` exists. If not, tell the Owner to run `/start-workflow` first.

2. Dispatch the `orchestrator` agent via the Task tool with this prompt:
   > "Resume the Incubator workflow. Read OWNER_INBOX.md for new notes from Owner. Read BLOCKED.md for any entries now having `resolution:` fields. Process notes and resolutions per WORKFLOW.md Step 1. Then continue the cycle from current STATE.md position. Do not stop until WORKFLOW_COMPLETE, ALL_BLOCKED, or FATAL_ERROR."

3. Relay the Orchestrator's returned handoff status to the Owner in one line.
