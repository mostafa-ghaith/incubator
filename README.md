# Incubator

An autonomous product-building team for Claude Code. One Owner + ten role-based agents that plan, design, build, and ship software — sequentially, with clear handoffs, and without stopping to ask permission for every reversible decision.

Designed so you can kick off a project, close the laptop, and come back later to a workflow that's still running.

## The team

| Agent | Role |
|---|---|
| **Orchestrator** | Routes work, validates handoffs, maintains state, keeps the loop alive |
| **PM** | Vision interview, PRD, user stories, scope arbiter |
| **Market Researcher** | Problem validation, TAM/SAM/SOM, competitor scan, adjacent ideas |
| **Strategist** | Direction critique, positioning, go/pivot/no-go verdict |
| **Designer** | Flows, wireframes, visual system, accessibility |
| **CTO** | Stack + architecture + task breakdown + bug triage |
| **Frontend Dev** | Web UI (Next.js / React / TypeScript) |
| **iOS Dev** | Native iOS (Swift / SwiftUI / HIG) |
| **Backend Dev** | APIs, DB schema, auth (Supabase + Vercel Functions by default) |
| **DevOps** | CI/CD, deploys, env vars, observability, Fastlane |
| **QA** | Playwright / Maestro / API contract tests; files bugs |

## How it works

1. **You talk to the PM once** at project start (PHASE 0 interview) to capture your vision.
2. **You talk to the CTO once** (PHASE 0 stack chat) — or defer and let CTO pick defaults.
3. **From there, the workflow runs autonomously.** Agents hand off to each other per a fixed workflow. The Orchestrator keeps it moving.
4. **Only five gates pause for you** (see `docs/WORKFLOW.md`):
   - Strategist recommends pivot/kill
   - Architecture requires new vendor/money
   - Production release sign-off
   - Paid service / external credentials needed
   - Every remaining path is blocked on you (deadlock)
5. **Everything else is decided and logged** to `REVIEW.md`. You skim it when you come back.

## Install

From inside Claude Code, two steps:

```
/plugin marketplace add mostafaghaith/incubator
/plugin install incubator@incubator
```

Then reload:
```
/reload-plugins
```

To update later:
```
/plugin marketplace update incubator
/plugin update incubator@incubator
```

## Usage

Inside a project directory:

```
/start-workflow     # begin (or bootstrap files + begin)
/status             # see current phase, ticket board, blocks
/review             # see recent autonomous decisions
/resume             # continue after you answered a block
/stop               # clean halt at next handoff
```

### First session

1. Run `/start-workflow` in an empty directory.
2. Answer the PM's interview questions honestly. Takes ~5–15 minutes.
3. (Optional) Answer the CTO's stack preferences, or say "you decide."
4. Close the laptop. Check `/status` from Claude Code on your phone later.

### Subsequent sessions

- Nothing blocked? Run `/start-workflow` or `/resume` — workflow continues.
- Something blocked? Run `/status`. Open `BLOCKED.md`, fill in `resolution:` for each entry. Run `/resume`.
- Want to redirect? Write notes to `OWNER_INBOX.md`. Agents read it first every turn.

## Files the workflow creates in your project

```
<project>/
├── CLAUDE.md                # bootstraps workflow in every Claude session
├── STATE.md                 # live state (Orchestrator maintains)
├── REVIEW.md                # autonomous decisions log
├── BLOCKED.md               # open questions for you
├── OWNER_INBOX.md           # your notes to agents
├── BACKLOG.md               # deferred ideas
├── docs/
│   ├── VISION.md            # from PM interview
│   ├── PRD.md
│   ├── user-stories.md
│   ├── MARKET.md
│   ├── STRATEGY.md
│   ├── DESIGN.md
│   ├── ARCHITECTURE.md
│   ├── RELEASE_NOTES.md
│   └── adr/ADR-NNN-*.md
└── tasks/
    ├── TICKET-NNN.md
    └── BUG-NNN.md
```

## Default stack (if you defer to CTO)

- **Web:** Next.js App Router + TypeScript + Tailwind + shadcn/ui + Supabase + Vercel
- **iOS:** Swift 6 + SwiftUI + SwiftData + Supabase backend + Fastlane
- **Testing:** Vitest + Playwright (web), XCTest + Maestro (iOS), API contract tests
- **CI/CD:** GitHub Actions → Vercel (web) / TestFlight (iOS)

Override any of these during the PHASE 0 CTO chat.

## Philosophy

Built on three rules:

1. **Keep moving.** Every stop for Owner input is overhead. Default to deciding and logging.
2. **One agent at a time.** No parallel agent teams — cheaper, simpler state, easier to debug.
3. **Explicit handoffs.** Every handoff follows `HANDOFF_PROTOCOL.md` — no "uh, over to you."

## Docs you may want to read

- `docs/WORKFLOW.md` — the phases, roles, and orchestrator loop
- `docs/HANDOFF_PROTOCOL.md` — agent-to-agent packet schema
- `docs/ESCALATION.md` — when agents are allowed to pause for Owner

## Status

v0.1.0 — usable, not battle-tested. Expect to tune agent prompts based on your projects. PRs welcome.

## License

MIT — see `LICENSE`.
