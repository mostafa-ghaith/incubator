# STATE.md

> Live workflow state. Orchestrator appends to this file every cycle. Agents update their own sections. Never destructively rewrite — always append or edit specific entries.

## Current phase
PHASE: 0
PHASE_STATUS: not_started

## Active agent
none

## Last handoff
<!-- The most recent HANDOFF packet, for audit -->

## Ticket board

### Ready
<!-- Tickets where all dependencies are closed -->

### In progress
<!-- Tickets currently being worked -->

### Blocked
<!-- Tickets blocked on agent / owner -->

### In QA
<!-- Tickets awaiting QA verdict -->

### Closed
<!-- Tickets passed QA and closed -->

## Decisions taken this session
<!-- Summary of REVIEW.md entries since last Owner sign-in -->

## Blocks awaiting Owner
<!-- Summary of BLOCKED.md open entries -->

## Heartbeat
last_cycle_at: <ISO timestamp>
cycle_count: 0
