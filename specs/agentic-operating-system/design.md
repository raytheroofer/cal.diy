# Agentic Operating System Design

## Overview

An "operating system" for running the business's day-to-day operations with AI agents. A small set of named agents — each with a written charter, a schedule, and explicit permission boundaries — run on Claude scheduled tasks against the tools the business already uses (Gmail, Google Calendar, CompanyCam, QuickBooks, Slack, Google Drive/Sheets, Cal.diy bookings). Shared state lives in a versioned `ops/` directory in this repo so every agent reads the same facts, SOPs, and open loops.

## Problem Statement

Running a roofing business means the same operational loops every day: triage inbound leads fast, keep jobs documented, chase unpaid invoices, prep payroll, and know the state of the business each morning. Today these loops are manual, interrupt-driven, and depend on one person remembering them. One-off Claude sessions help, but each session starts from zero — no shared memory, no standing schedule, no consistent guardrails.

This spec defines the standing structure: which agents exist, what each is allowed to do on its own vs. draft-for-approval, where shared state lives, and in what order to roll them out.

## OS Metaphor (Architecture)

| OS concept | Implementation |
|---|---|
| Kernel / scheduler | Claude scheduled tasks — each agent is a recurring task with a stored prompt |
| Processes | Named agents (Dispatcher, Lead Agent, Money Agent, Jobs Agent, Reporter) |
| Filesystem / memory | `ops/` directory in this repo: charters, SOPs, business facts, run logs |
| Device drivers | Connectors: Gmail, Google Calendar, CompanyCam, QuickBooks, Slack, Drive/Sheets |
| Permission rings | Per-agent guardrail policy (see Permission Model) |
| Shell / console | Daily digest to the owner (email or Slack) + this repo's run logs |

## The Agents

### 1. Dispatcher (daily brief) — Phase 1

Runs every weekday morning. Reads: today's calendar, unread email headers, open CompanyCam projects, overdue invoices. Writes: one digest message to the owner ("today's schedule, new leads overnight, 3 invoices overdue, job X has no photos in 4 days"). Read-only against every system; its only output is the digest.

### 2. Lead Agent (speed-to-lead) — Phase 2

Runs several times per business day. Scans the inbox for new inbound leads (quote requests, missed-call texts forwarded to email, web form submissions). For each lead: extracts name/address/need into the lead log (`ops/state/leads.md` or a Sheet), **drafts** a reply with available estimate slots (from Google Calendar / Cal.diy availability), and flags it in the digest. Never sends — drafts only.

### 3. Money Agent (invoice chase + cash snapshot) — Phase 3

Runs weekly (chase) and weekly (snapshot). Chase: pulls open invoices from QuickBooks, applies the dunning SOP (gentle at 7 days, firm at 21, call-list at 45), **drafts** reminder emails, and lists them for one-click approval. Snapshot: AR aging + cash position summary appended to the weekly report. Read access to QuickBooks; writes limited to email drafts and the report.

### 4. Jobs Agent (field documentation) — Phase 4

Runs daily. Audits active CompanyCam projects: jobs with no photos in N days, checklists incomplete, projects missing a customer record. Posts findings into the digest and comments on the CompanyCam project where a nudge belongs. No project creation/archival on its own.

### 5. Reporter (weekly pulse) — Phase 5

Runs Friday afternoon. Compiles the week: leads in → quotes out → jobs closed, invoice collections, documentation health, and anything the other agents flagged but nobody closed. Produces one written report (Drive doc or Slack post) and updates `ops/state/scorecard.md`.

## Shared State: the `ops/` Directory

```
ops/
├── README.md              # How the system works, index of agents
├── agents/                # One charter per agent
│   ├── dispatcher.md      # Mission, schedule, inputs, outputs, permissions
│   ├── lead-agent.md
│   ├── money-agent.md
│   ├── jobs-agent.md
│   └── reporter.md
├── sops/                  # Written procedures agents must follow
│   ├── lead-intake.md     # What counts as a lead, response template, SLA
│   ├── invoice-dunning.md # Reminder cadence and tone by age bucket
│   └── job-documentation.md
└── state/
    ├── facts.md           # Stable business facts (service area, pricing basics, crew)
    ├── leads.md           # Rolling lead log (or pointer to the Sheet that holds it)
    ├── scorecard.md       # Weekly metrics history
    └── runlog.md          # One line per agent run: date, agent, outcome, flags
```

Charters are the contract: each agent's scheduled-task prompt says "read your charter at `ops/agents/<name>.md` and the SOPs it references, then do today's run." Changing an agent's behavior = editing a markdown file in a PR, not rewriting a stored prompt.

## Permission Model

Three rings, declared in each charter:

- **Ring 0 — autonomous**: read any connected system; write to `ops/state/`; post the digest/report to the agreed channel.
- **Ring 1 — draft-for-approval**: anything leaving the business (customer emails, invoice reminders, quotes). Agent produces a draft; the owner sends.
- **Ring 2 — never**: sending money, changing QuickBooks records, deleting/archiving anything, contacting a customer without a draft having been approved, committing to a price or date.

Default for anything not listed in a charter: Ring 2.

## Technical Design

### Database Changes

None. This system does not touch `packages/prisma`. All state is markdown in `ops/` plus the external systems of record (QuickBooks, CompanyCam, Gmail).

### API Changes

None. Agents consume existing connectors and, where scheduling data is needed, the existing Cal.diy/calendar surfaces. No new endpoints.

### UI Changes

None in the app. The "UI" is the morning digest, the weekly report, and this repo.

### Scheduling

Each agent = one Claude scheduled task (fresh session per fire) whose prompt is three lines: identity, pointer to its charter in this repo, and the instruction to log its run to `ops/state/runlog.md` (via commit or digest line). Cadences: Dispatcher weekday mornings; Lead Agent 3×/business day; Money Agent weekly; Jobs Agent daily; Reporter Friday PM.

## Edge Cases

- **Connector down / auth expired**: agent reports the failure in its digest line and skips, never guesses. Dispatcher surfaces repeated failures.
- **Ambiguous lead** (spam vs. real, duplicate customer): log it with a `needs-review` flag rather than drafting a reply.
- **Conflicting state** (invoice paid in QuickBooks after chase draft was made): QuickBooks is the system of record; drafts are regenerated from live data at send-approval time, never sent stale.
- **Two agents touching the same thing**: only the agent that owns a domain writes to it (Lead Agent owns `leads.md`, Reporter owns `scorecard.md`); everyone else reads.
- **PII**: lead log stores name + job address + contact info only; no payment details ever land in this repo.

## Out of Scope

- Autonomous sending of any customer communication (permanently Ring 1 until explicitly revisited).
- Payroll execution, payments, or any QuickBooks writes.
- Hiring/recruiting automation (future-work).
- A web dashboard UI (the digest is the dashboard for v1; see future-work).
- Changes to Cal.diy product code — this spec adds documentation and `ops/` content only.
