# ESCALATION.md — When to Wake the Owner

**Prime directive: DO NOT BLOCK unless you absolutely must.** The Owner started this workflow to sleep / work on other things. Every unnecessary escalation defeats the purpose.

---

## The decision ladder

When you face any ambiguity, apply this ladder in order. Stop at the first level that applies.

### Level 1 — Is it answered in the docs?
Check in this order:
1. Current ticket's acceptance criteria
2. `docs/PRD.md` and `docs/user-stories.md`
3. `docs/ARCHITECTURE.md` and relevant `docs/adr/*.md`
4. `docs/DESIGN.md`
5. `docs/VISION.md`

If answered → apply the answer, cite the source in REVIEW.md, continue.

### Level 2 — Is the decision REVERSIBLE and LOW-STAKES?
Reversible = we can change it later without data loss, user impact, or material cost.

Default: **DECIDE AND LOG**. Do NOT escalate.

Examples that are ALWAYS reversible:
- Naming (variables, files, routes, components)
- Folder structure
- Library choice within an approved stack (e.g., zod vs yup)
- Copy/microcopy (you can change text later)
- Default values (timeout = 30s, page size = 20, etc.)
- Color shades within the design palette
- Test strategy details (unit vs integration for a given function)
- Code style (one-line vs multi-line when both work)
- Error message wording
- Log levels
- Which of two equivalent API shapes to use

For these: pick the most popular/conventional option, add a one-line note to `REVIEW.md`, continue.

### Level 3 — Is there a reasonable default you can document and proceed?
Examples:
- Ambiguous requirement → pick the NARROWER interpretation (ship less, not more)
- Missing spec detail → infer from similar existing patterns in the codebase
- Two paths look equally valid → pick the one that's simpler to reverse

Log to `REVIEW.md` as:
```
DECISION: <what I did>
WHY: <one-line reasoning>
REVERSIBLE BY: <how to change course later>
```

### Level 4 — Can you work around it and flag it for later?
If you can't decide and can't default, but you can:
- Skip the ticket and pick another unblocked one
- Mock the unknown piece
- Build a smaller version

Then do so. Add a note to `BACKLOG.md` with the open question. **Do not stop the workflow.**

### Level 5 — Is it TRULY blocking?
Escalate ONLY IF:
- Every other reasonable unblocked ticket is also done/blocked, AND
- One of the P1 conditions below is true

---

## P1 conditions — when escalation is justified

Escalate to Owner (write to `BLOCKED.md`) only when:

| # | Condition | Example |
|---|---|---|
| P1.1 | **Vision conflict** — Owner's stated VISION conflicts with what the PRD/ticket is asking for, and PM can't resolve from VISION.md alone | PRD says "B2B SaaS" but new feature request implies "consumer app" |
| P1.2 | **Money required** — new paid service, vendor, or subscription beyond Owner's stated budget in VISION.md | Need to sign up for Twilio ($) or a paid AI API |
| P1.3 | **External account required** — needs Owner's personal credentials, OAuth setup, DNS, domain, App Store Connect, etc. | Need Stripe API keys, need Apple developer account access |
| P1.4 | **Irreversible change** — production data deletion, schema migration without rollback, published API break | Dropping a column with customer data |
| P1.5 | **Legal / security / compliance** — data residency, PII handling, license compatibility | Storing EU user data, GPL library in proprietary product |
| P1.6 | **Strategist "pivot" or "no_go" verdict** | See PHASE 1 in WORKFLOW.md |
| P1.7 | **Release gate** (G3) | Before production deploy |
| P1.8 | **Deadlock** — every remaining ticket is blocked on something Owner must answer | Literally no more work possible |
| P1.9 | **Explicit scope expansion** — a proposed change would add >20% to TASKS.md count | Someone proposes adding a whole new feature area |

**Not on the list? Not P1. Apply Levels 1-4.**

---

## How to escalate correctly

When you MUST escalate, follow these rules:

### 1. Keep working
First, identify what OTHER work can proceed without this answer. Put that work in progress BEFORE writing the BLOCKED entry.

### 2. Batch questions
Don't escalate one question at a time. If you know more questions are coming, wait for the workflow to accumulate them, then escalate once.

### 3. Write to BLOCKED.md with structure

Every entry MUST have:

```markdown
## BLOCKED-NNN — <one-line title>
- raised_by: <agent>
- raised_at: <ISO timestamp>
- condition: <which P1 rule triggered>
- context: <what we were doing>
- question: <the actual question>
- options:
    - A) <option> — tradeoff: <...>
    - B) <option> — tradeoff: <...>
    - C) <option> — tradeoff: <...>
- recommendation: <A/B/C and why>
- impact_if_no_answer: <what can't proceed>
- can_proceed_on: <list of tickets/work that IS proceeding>
```

### 4. Let Orchestrator decide when to notify
Individual agents write to `BLOCKED.md`. They do NOT notify Owner directly. The Orchestrator aggregates and decides when to ping (typically: when all other work is done/blocked, or at the end of a phase).

### 5. While blocked, keep the loop alive
The agent that raised the block returns a `status: blocked_on_owner` handoff packet to the Orchestrator and then releases control. The Orchestrator picks the next unblocked work. The agent does NOT spin waiting.

---

## How Owner answers

The Owner will write answers in one of two places:
1. `OWNER_INBOX.md` — free-form note ("on BLOCKED-003, go with option B")
2. Direct edit to `BLOCKED.md` — adding a `resolution:` field to the entry

At the start of every turn, every agent (starting with Orchestrator) MUST:
1. Read `OWNER_INBOX.md` — if any notes, parse them and act on them first
2. Read `BLOCKED.md` — if any entries have new `resolution:` fields, mark those blocks as resolved in STATE.md and queue the associated work

Once an `OWNER_INBOX.md` note is acknowledged, move it to `REVIEW.md` with a `ACKNOWLEDGED:` prefix so we don't process it twice.

---

## Anti-patterns (do NOT do these)

- **Asking permission for conventional choices.** "Should I use TypeScript for this?" is not a P1 if TypeScript is already in the stack.
- **Asking for confirmation of a completed step.** "I finished X, should I proceed to Y?" No — check the workflow, proceed.
- **Turning Level-2 reversibles into questions.** "Which color for the primary button?" → pick one from the design system.
- **Waiting on a non-blocking answer.** If the answer only affects ticket 14, don't block tickets 15-30 waiting.
- **Re-asking a question already in BLOCKED.md.** Check first.
- **Ending with "Ready to proceed?".** Just proceed.
- **Summarizing at the end of a turn.** Produce the HANDOFF packet, nothing else.

---

## Anti-anti-pattern reminder

You are NOT being reckless by proceeding. You ARE being reckless by stopping work at 9:01 PM for a question the Owner will answer at 8:00 AM. Twelve hours of silent agents is the failure mode we're designing against. Decide, log, move on.
