# Agentic Operating System Decisions

## ADR-001: Shared state lives in this repo (`ops/`), not in Drive or a database

### Context

Agents need shared, durable memory: charters, SOPs, business facts, and run history. Candidates: Google Drive docs, a Google Sheet, a database table, or markdown in this repo.

### Options Considered

1. **Markdown in this repo** — versioned, reviewable in PRs, readable by every Claude session that clones the repo; requires a commit to update; not great for high-churn tabular data
2. **Google Drive/Sheets** — easy for the owner to edit by hand; no version history worth auditing; agents need connector access just to read their own charter
3. **Database (Prisma)** — overkill; couples business ops to the product schema; violates "ask first" on schema changes

### Decision

Markdown in `ops/` for charters, SOPs, facts, and the run log. High-churn tabular data (the lead log) may point to a Google Sheet from `ops/state/leads.md` if commit-per-lead proves too noisy — decide during Phase 2.

### Consequences

- Behavior changes are PRs: auditable, revertable, reviewable
- Every scheduled session gets the same source of truth by cloning the repo
- The repo gains a non-product directory; acceptable since this is the owner's fork (revisit extraction to a dedicated ops repo in future-work if it grows)

## ADR-002: Scheduled tasks with fresh sessions, charters as the contract

### Context

Each agent needs to run on a cadence. The prompt could carry the full instructions, or point at a versioned charter.

### Options Considered

1. **Full instructions in the stored prompt** — self-contained, but editing behavior means rewriting stored prompts, with no history
2. **Three-line prompt pointing at a charter file** — behavior is versioned in git; prompt almost never changes

### Decision

Option 2. Each scheduled task fires a fresh session whose prompt names the agent, points at `ops/agents/<name>.md`, and requires a runlog entry.

### Consequences

- One mechanism to change behavior (edit the charter in a PR)
- A broken charter breaks the next run visibly rather than silently drifting
- Fresh sessions mean no hidden state between runs; everything an agent "remembers" must be written to `ops/state/`

## ADR-003: Drafts-only for everything customer-facing (three-ring permission model)

### Context

The biggest risk is an agent sending a wrong price, a rude dunning email, or committing to a date. The biggest value is speed on exactly those loops.

### Options Considered

1. **Full autonomy with good prompts** — fastest, but one bad send damages a customer relationship
2. **Drafts-only everywhere** — safe, but the owner becomes the bottleneck even for internal reports
3. **Three rings** — autonomous for reads + internal state, drafts for anything leaving the business, hard-never for money and records

### Decision

Three rings, declared per charter, default-deny (unlisted = never). Revisit ring assignments per agent after each has a few weeks of clean drafts.

### Consequences

- Owner reviews outbound drafts daily — this is the intended human-in-the-loop, not a defect
- Earning autonomy is an explicit, per-agent charter change later, with evidence
