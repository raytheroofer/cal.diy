# CLAUDE.md — Agentic Operating System

## Project Context

A standing system of AI agents (Dispatcher, Lead Agent, Money Agent, Jobs Agent, Reporter) that run the business's operational loops on Claude scheduled tasks, with shared state and charters versioned in an `ops/` directory in this repo. This is an ops/automation feature, not a Cal.diy product feature — it adds markdown and configuration, not application code.

## Before Starting Work

1. Read specs/agentic-operating-system/design.md
2. Check specs/agentic-operating-system/implementation.md for current progress
3. Read specs/agentic-operating-system/decisions.md before re-litigating structure choices

## Patterns

- One agent = one charter file in `ops/agents/` + one scheduled task pointing at it
- Charters declare permissions using the three-ring model in design.md; anything unlisted is Ring 2 (never)
- Behavior changes go through charter/SOP edits in PRs, not through rewriting stored scheduled-task prompts
- Agents write only to the state files they own; everyone else reads

## Don't

- Don't add features not in design.md
- Don't touch `packages/*` or `apps/*` — this feature lives in `specs/` and `ops/` only
- Don't give any agent autonomous send/write access to customer-facing systems (email sends, QuickBooks writes) — drafts only
- Don't store payment details or anything beyond name/address/contact in `ops/state/`
- Don't build more than one phase per PR
