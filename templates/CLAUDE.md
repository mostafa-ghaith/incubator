# Project CLAUDE.md

This file is loaded automatically in every Claude Code session for this project. It bootstraps the Incubator workflow.

## Workflow mode

This project uses the **Incubator** plugin for autonomous product development.

The workflow is defined in:
- Plugin docs: `${CLAUDE_PLUGIN_ROOT}/docs/WORKFLOW.md`
- Plugin docs: `${CLAUDE_PLUGIN_ROOT}/docs/HANDOFF_PROTOCOL.md`
- Plugin docs: `${CLAUDE_PLUGIN_ROOT}/docs/ESCALATION.md`

Agents defined in the plugin are available and follow those documents strictly.

## Project artifacts

Live state and outputs live here:
- `docs/VISION.md` — Owner's vision (produced in PHASE 0)
- `docs/PRD.md`, `docs/user-stories.md` — Product definition (PHASE 1)
- `docs/MARKET.md`, `docs/STRATEGY.md` — Validation (PHASE 1)
- `docs/DESIGN.md` — Design spec (PHASE 2)
- `docs/ARCHITECTURE.md`, `docs/adr/` — Architecture + decisions (PHASE 2)
- `tasks/` — Tickets (PHASE 3)
- `STATE.md` — Live workflow state (Orchestrator maintains)
- `REVIEW.md` — Autonomous decisions log (Owner skims this)
- `BLOCKED.md` — Open questions for Owner (only read if not empty)
- `BACKLOG.md` — Out-of-scope ideas deferred
- `OWNER_INBOX.md` — Notes FROM Owner TO agents

## Activation

- `/start-workflow` — begin or resume the workflow
- `/status` — show STATE.md + any blocks
- `/review` — show recent REVIEW.md entries
- `/resume` — continue after answering blocks

## Normal mode

If you want to use Claude Code without the workflow (plain coding help), just chat normally. Agents auto-invoke ONLY when:
1. You run `/start-workflow` or `/resume`, OR
2. You explicitly address an agent by name ("PM, ..." / "CTO, ...")

Otherwise, Claude answers directly without routing through the team.

## Conventions this project follows

- All agents read `OWNER_INBOX.md` first
- All handoffs follow `HANDOFF_PROTOCOL.md`
- All decisions are autonomous unless listed in `ESCALATION.md`
- Verification runs before any ticket is marked done
