# HANDOFF_PROTOCOL.md — Agent-to-Agent Handoff Contract

Every agent MUST produce a handoff packet when finishing its turn. No implicit handoffs. No "I'm done, over to you" without a structured packet.

The Orchestrator reads handoff packets to decide the next step. If a packet is malformed, Orchestrator rejects it and re-invokes the producing agent to fix it.

---

## Handoff packet schema (all handoffs)

Every agent, at the end of its turn, MUST emit a markdown block that starts with the header `## HANDOFF` and contains exactly these fields:

```markdown
## HANDOFF
- from: <agent-name>
- to: <next-agent-name | "orchestrator" | "owner">
- status: <"done" | "blocked_on_owner" | "blocked_on_agent" | "needs_clarification" | "workflow_complete">
- phase: <0 | 1 | 2 | 3 | 4>
- ticket_id: <TICKET-NNN or "none">
- artifacts_produced:
    - <path/to/file.md>
    - <path/to/other.md>
- next_action: <one sentence — what the next agent must do>
- context_for_next: <1-3 sentences the next agent needs to start without re-reading everything>
- decisions_logged:
    - <1-line summary of each decision written to REVIEW.md this turn>
- open_questions:
    - <if status=blocked_on_owner, list questions with 3 options each and recommended option>
- state_updated: <true | false — did you update STATE.md this turn?>
```

If any field is missing or empty (where required), the packet is invalid.

---

## Per-handoff payload additions

Beyond the universal schema, specific handoffs require extra fields in the `context_for_next` area.

### PM → Market Researcher
- vision_summary: 3 bullets from VISION.md
- problem_statement: the one-sentence problem to validate
- target_user: persona sketch
- hypothesis_to_test: the core claim

### Market Researcher → Strategist
- market_summary: TAM/SAM/SOM numbers
- top_3_competitors: names + differentiation
- adjacent_ideas: 3+ ideas that match or widen VISION
- problem_validation_verdict: "confirmed" | "weak" | "not_validated"

### Strategist → PM
- direction_verdict: "go" | "pivot" | "no_go"
- positioning: one sentence
- wedge: the narrow entry point
- risks: top 3
- recommendation_if_pivot: what to pivot to

### PM → Designer
- user_stories_path: docs/user-stories.md
- prd_path: docs/PRD.md
- primary_flows: list of must-have flows in priority order
- platform_targets: [web | ios | both]
- design_constraints: brand, accessibility level (WCAG AA default), performance budgets

### Designer → CTO
- design_path: docs/DESIGN.md
- component_inventory: list of components with file references
- platform_specific_notes: web vs iOS differences
- accessibility_audit_summary: WCAG AA passed / exceptions

### CTO → Orchestrator (after task breakdown)
- architecture_path: docs/ARCHITECTURE.md
- stack_decisions: list of ADR IDs created
- tasks_count: N
- tasks_path: tasks/
- dependency_graph_summary: which tickets block which (Orchestrator uses this for ordering)
- risk_summary: top 3 technical risks

### CTO → Dev (per ticket)
- ticket_id: TICKET-NNN
- ticket_path: tasks/TICKET-NNN.md
- depends_on: list of other TICKET IDs that must be closed first
- blocks: list of TICKET IDs waiting on this
- acceptance_criteria: bulleted, testable
- related_design_sections: anchors into DESIGN.md

### Dev → QA (per ticket)
- ticket_id: TICKET-NNN
- deliverables:
    - <file paths changed>
- how_to_run: command(s) to start the artifact locally / deploy URL for staging
- test_fixtures: paths or seed data
- self_test_results: which tests I ran and passed
- known_limitations: things I deliberately did NOT do (with justification)

### QA → Orchestrator (pass)
- ticket_id: TICKET-NNN
- verdict: "pass"
- evidence:
    - test run output path
    - screenshots / video if applicable
- coverage_notes: what was tested / what was skipped

### QA → CTO (fail — files bug)
- ticket_id: TICKET-NNN (the original)
- bug_ticket_id: BUG-NNN
- bug_ticket_path: tasks/BUG-NNN.md
- severity: P0 | P1 | P2 | P3
- repro_steps: numbered list
- expected vs actual
- suggested_owner: original dev agent

### CTO → Dev (bug fix routing)
- bug_ticket_id: BUG-NNN
- decision: "fix_now" | "backlog" | "scope_change"
- justification: one sentence

### DevOps → QA (deploy available)
- environment: "staging" | "production"
- url: https://...
- commit_sha: <hash>
- rollback_plan: one-liner
- smoke_endpoints: list of URLs to hit

### Orchestrator → Owner (escalation)
- gate: G1 | G2 | G3 | G_escalation
- summary: 3 bullets
- questions: list of `{question, options: [A, B, C], recommendation, why}`
- will_continue_on: list of work that can proceed while Owner decides
- will_stop_on: list of work that CANNOT proceed without this answer

---

## Handoff validation rules (Orchestrator enforces)

When Orchestrator receives a packet:

1. **Schema check** — all required fields present and non-empty (where required by status)
2. **Status ↔ fields consistency:**
   - `status: done` → `next_action` and `to` must be set
   - `status: blocked_on_owner` → `open_questions` must be non-empty
   - `status: blocked_on_agent` → `to` must name that agent
   - `status: workflow_complete` → only Orchestrator itself may emit this
3. **Artifacts exist check** — paths listed in `artifacts_produced` must resolve to real files
4. **State updated check** — if `state_updated: false`, Orchestrator updates STATE.md on the agent's behalf and logs a reminder

On validation failure: Orchestrator re-invokes the producing agent with the specific defect. This happens silently — not escalated.

---

## Example: valid handoff packet

```markdown
## HANDOFF
- from: backend-dev
- to: qa
- status: done
- phase: 3
- ticket_id: TICKET-014
- artifacts_produced:
    - src/api/auth/login.ts
    - src/api/auth/login.test.ts
    - docs/api/auth.openapi.yaml
- next_action: Run api-testing skill against POST /api/auth/login using fixtures in test/fixtures/auth/, then run Playwright E2E login flow on staging URL from TICKET-014.
- context_for_next: Login endpoint is deployed to staging at https://staging.example.com. Rate-limit is 5/min/IP. Fixtures seed three users — see test/fixtures/auth/README.md.
- decisions_logged:
    - Chose argon2id over bcrypt for password hashing; logged ADR-007 reasoning.
    - Returned 429 not 401 on rate-limit; logged to REVIEW.md.
- open_questions: []
- state_updated: true
```
