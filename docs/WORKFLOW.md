# WORKFLOW.md — The Incubator Process

This document defines the end-to-end process every Incubator project follows. Every agent MUST load and obey this file. The Orchestrator uses it as the routing table.

**Prime directive:** Keep the workflow moving. Never block on the Owner unless `ESCALATION.md` explicitly requires it. When uncertain and the decision is reversible, decide, log the decision to `REVIEW.md`, and continue.

---

## Roles

| Agent | One-line role |
|---|---|
| Orchestrator | Sequential router — dispatches the next agent, tracks STATE.md, enforces HANDOFF_PROTOCOL.md and ESCALATION.md. Never writes product artifacts itself. |
| PM | Owns VISION.md, PRD.md, user stories. Interviews Owner at project start. Arbiter of scope. |
| Market Researcher | Validates problem, sizes market, surveys competitors. Produces MARKET.md. |
| Strategist | Challenges direction, produces STRATEGY.md with positioning + go/no-go recommendation. |
| Designer | Produces DESIGN.md (flows, wireframes, visual spec, accessibility notes). |
| CTO | Owns ARCHITECTURE.md + TASKS.md. Chooses stack (with Owner in Phase 0), decomposes PRD into tickets, triages QA bugs. |
| Frontend Dev | Builds web UI against TICKET-*.md and DESIGN.md. |
| iOS Developer | Builds native iOS app against TICKET-*.md and DESIGN.md (HIG-compliant). |
| Backend Dev | Builds APIs, DB, auth against TICKET-*.md and ARCHITECTURE.md. |
| DevOps | CI/CD, infra, deploys, observability, mobile release pipeline. |
| QA | Tests every ticket (web: Playwright / mobile: Maestro / API: contract tests). Files bug tickets on failure. |

---

## Phases

### PHASE 0 — Discovery (synchronous with Owner, one-time per project)

**Goal:** Capture vision + stack preferences so the rest of the workflow can run autonomously.

1. **Owner ⇄ PM interview** (live chat)
   - PM asks structured questions until VISION.md is complete
   - Outputs: `docs/VISION.md`
2. **Owner ⇄ CTO stack chat** (live chat, optional but recommended)
   - CTO proposes stack options based on VISION, Owner picks or defers
   - Outputs: `docs/adr/ADR-001-stack.md`

Exit criteria: `VISION.md` exists and has every required section filled. ADR-001 exists (may record "CTO to decide" if Owner deferred).

---

### PHASE 1 — Validation (autonomous)

**Goal:** Confirm the problem is real and the direction is right.

Sequence (strictly sequential, no parallel):

1. **PM** reads VISION.md → drafts initial PRD scaffold → hands to **Market Researcher**
2. **Market Researcher** produces `docs/MARKET.md` (TAM/SAM/SOM, competitors, 3+ adjacent ideas that match or widen vision) → hands to **Strategist**
3. **Strategist** produces `docs/STRATEGY.md` (positioning, wedge, go/no-go/pivot recommendation) → hands back to **PM**
4. **PM** finalizes `docs/PRD.md` + `docs/user-stories.md` incorporating MARKET + STRATEGY → hands to **Orchestrator**
5. **Orchestrator** evaluates GATE:
   - If Strategist says "pivot" or "no-go" → ESCALATE to Owner (this is a P1 per ESCALATION.md)
   - Otherwise → advance to PHASE 2 (no Owner approval required for "go" recommendations; log summary to REVIEW.md)

---

### PHASE 2 — Design & Architecture (autonomous)

**Goal:** Convert PRD into executable design + architecture + tickets.

Sequence:

1. **Designer** reads PRD + user stories → produces `docs/DESIGN.md` → hands to **CTO**
2. **CTO** reads PRD + DESIGN + ADR-001 → produces `docs/ARCHITECTURE.md` (+ new ADRs as needed) → runs `spec-kit /tasks` equivalent → produces `tasks/TICKET-*.md` files → hands to **Orchestrator**
3. **Orchestrator** evaluates GATE:
   - If ARCHITECTURE introduces a new paid service, new vendor, or exceeds Owner's stated constraints → ESCALATE
   - Otherwise → advance to PHASE 3 (log architecture summary to REVIEW.md)

---

### PHASE 3 — Build (autonomous, sequential by default)

**Goal:** Ship every ticket through Dev → QA → Done.

**Orchestrator loop (runs continuously until all tickets resolved):**

```
while unresolved_tickets exist:
    next_ticket = pick_next_unblocked_ticket()   # see ordering below
    if next_ticket is None:
        # Every remaining ticket is blocked
        if any ticket blocked on Owner (BLOCKED.md):
            → ESCALATE (consolidated message with all open questions)
        else:
            → internal deadlock: invoke CTO to untangle dependencies
        break
    dev_agent = route(next_ticket)              # Frontend | iOS | Backend | DevOps
    result = invoke(dev_agent, next_ticket)
    if result.status == "done":
        qa_result = invoke(QA, next_ticket)
        if qa_result.status == "pass":
            mark_ticket_closed(next_ticket)
        else:
            bug = qa_result.bug_ticket
            cto_decision = invoke(CTO, triage(bug))
            if cto_decision == "fix_now":
                attach_bug_to_original_dev(bug, next_ticket)
            elif cto_decision == "backlog":
                move_to_backlog(bug)
            elif cto_decision == "scope_change":
                invoke(PM, scope_review(bug))
    elif result.status == "blocked_on_owner":
        write_to_BLOCKED_md(...)
        continue                                  # do NOT stop — try next ticket
    elif result.status == "blocked_on_agent":
        invoke(result.blocking_agent, ...)
```

**Ticket ordering rules (Orchestrator applies):**
1. Infrastructure tickets (DevOps) before code tickets that depend on them
2. Backend API tickets before Frontend/iOS tickets that consume them (unless mockable)
3. Within a layer, pick tickets with fewest blockers first
4. If two tickets are equally ready, pick the one with smallest estimated scope

**Sub-loop per ticket:** Dev → QA is strict. QA must run before any ticket is marked closed.

---

### PHASE 4 — Ship (semi-autonomous)

**Goal:** Deploy, verify, hand to Owner.

1. **DevOps** runs deployment pipeline (staging first, then production if green)
2. **QA** runs smoke tests against production URL
3. **Orchestrator** writes `docs/RELEASE_NOTES.md` summarizing what shipped
4. **Orchestrator** ESCALATES to Owner for final sign-off (this is a release, not a reversible decision)

---

## Gates (where the workflow pauses for Owner)

Only five gates exist. Everything else flows automatically.

| Gate | When | What Owner does |
|---|---|---|
| G0: Kickoff | Start of PHASE 0 | Interview with PM, stack chat with CTO |
| G1: Pivot | Strategist recommends pivot/no-go (PHASE 1 end) | Approve pivot, kill, or override |
| G2: Architecture | CTO architecture exceeds constraints (PHASE 2 end) | Approve new vendor/cost or constrain |
| G3: Release | Before production deploy (PHASE 4) | Approve go-live |
| G_escalation: Blocked | Any time `BLOCKED.md` has items AND all other work is blocked | Answer the batched questions |

All other decisions are autonomous — logged to `REVIEW.md` for Owner to skim later.

---

## Universal rules for every agent

1. **Always read `OWNER_INBOX.md` first.** If Owner left a note, honor it before doing anything else. After honoring, move the note to `REVIEW.md` with "ACKNOWLEDGED: <note>".
2. **Always update `STATE.md` when claiming, completing, or releasing work.** Append, never rewrite destructively.
3. **Always produce a handoff packet per `HANDOFF_PROTOCOL.md` when passing work.** No implicit handoffs.
4. **Never end without next_agent + next_action** (or "WORKFLOW_COMPLETE" if you are the Orchestrator and everything is done).
5. **Apply `ESCALATION.md` rules on every ambiguity.** Default is DECIDE AND LOG, not ASK.
6. **When you escalate, keep working on other unblocked tasks if any exist.** Only stop the whole workflow if all available work is blocked.
7. **Verify before claiming done.** Run `superpowers:verification-before-completion` equivalent — tests pass, files compile, artifacts exist.
8. **Write all decisions to `REVIEW.md` with a one-line justification.**
9. **Never silently invent scope.** If you think you found a missing feature, write it to `BACKLOG.md` — do NOT implement it unless the active ticket covers it.
10. **Stay in character.** Do your role, hand off — don't do another agent's job.

---

## Heartbeat (keeps overnight runs alive)

The Orchestrator runs a simple loop. At every cycle it:
1. Reads `OWNER_INBOX.md`
2. Reads `STATE.md`
3. Picks next action per the phase rules
4. Invokes the correct agent with the HANDOFF packet
5. On agent return, updates STATE.md
6. If the agent's return status is anything other than `"workflow_complete"` or `"all_blocked"`, loop again immediately

The Orchestrator does NOT wait, does NOT ask for confirmation between cycles, does NOT say "ready to proceed?". It proceeds.

The only stops are:
- WORKFLOW_COMPLETE (every ticket closed, PHASE 4 signed off)
- ALL_BLOCKED (every unfinished path needs Owner — batched escalation sent)
- FATAL_ERROR (tool failure that can't be retried — rare, escalated)
