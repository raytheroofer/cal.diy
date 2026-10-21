# Business Ops — Agent System

This directory is the shared memory for the agents that run Mrs Roofers' operational loops. The full design lives in `specs/agentic-operating-system/design.md`.

## How it works

- Each agent is a Claude scheduled task. Its stored prompt is three lines: who it is, a pointer to its charter here, and the rule to log its run.
- `agents/` holds one charter per agent — the contract for what it reads, what it produces, and what it may do on its own.
- `state/` holds what agents remember between runs. An agent writes only to the files its charter says it owns.
- Changing an agent's behavior = editing its charter in a PR. Never by rewriting the stored prompt.

## Live agents

| Agent | Schedule | Status |
|---|---|---|
| Dispatcher (morning brief) | Weekday mornings | Live |
| Lead Agent | — | Not built (Phase 2) |
| Money Agent | — | Not built (Phase 3) |
| Jobs Agent | — | Not built (Phase 4) |
| Reporter | — | Not built (Phase 5) |

## Permission rings (summary)

- **Ring 0 (autonomous)**: read any connected system; write to `ops/state/`; deliver the digest/report.
- **Ring 1 (draft only)**: anything leaving the business — customer emails, reminders, quotes.
- **Ring 2 (never)**: money movement, record changes, deletions, price/date commitments. Anything a charter doesn't list is Ring 2.
