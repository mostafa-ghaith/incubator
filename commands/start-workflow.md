---
description: Start (or restart from scratch) the Incubator workflow for this project
---

Invoke the **orchestrator** agent to start the Incubator workflow.

Instructions for Claude:

1. Check if `docs/VISION.md` exists in the current working directory. If not, bootstrap project files:
   - Create `docs/` if missing
   - Copy `${CLAUDE_PLUGIN_ROOT}/templates/VISION.md` → `docs/VISION.md`
   - Copy `${CLAUDE_PLUGIN_ROOT}/templates/STATE.md` → `STATE.md`
   - Copy `${CLAUDE_PLUGIN_ROOT}/templates/REVIEW.md` → `REVIEW.md`
   - Copy `${CLAUDE_PLUGIN_ROOT}/templates/BLOCKED.md` → `BLOCKED.md`
   - Copy `${CLAUDE_PLUGIN_ROOT}/templates/OWNER_INBOX.md` → `OWNER_INBOX.md`
   - Copy `${CLAUDE_PLUGIN_ROOT}/templates/BACKLOG.md` → `BACKLOG.md`
   - Copy `${CLAUDE_PLUGIN_ROOT}/templates/CLAUDE.md` → `CLAUDE.md` (if no CLAUDE.md exists)
   - Create `docs/adr/`, `tasks/`, `tests/runs/` directories

2. Dispatch the `orchestrator` agent via the Task tool with this prompt:
   > "Start the Incubator workflow. Read WORKFLOW.md, HANDOFF_PROTOCOL.md, ESCALATION.md. Read OWNER_INBOX.md, STATE.md, VISION.md. Determine current phase and begin/resume the cycle. If VISION.md is empty or incomplete, dispatch PM for the PHASE 0 interview with the Owner. Otherwise advance from current state per WORKFLOW.md. Do not stop until WORKFLOW_COMPLETE, ALL_BLOCKED, or FATAL_ERROR."

3. When the Orchestrator returns its handoff packet, relay the status to the Owner in one line. Do not re-summarize agent output beyond that.
