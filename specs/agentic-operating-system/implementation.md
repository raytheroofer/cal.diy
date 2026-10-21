# Agentic Operating System Implementation

## Status: phase-1-in-progress

## Completed

- Spec drafted (design.md, decisions.md, prompts.md, future-work.md) — 2026-10-19
- Phase 1 `ops/` skeleton: README, Dispatcher charter, facts stub, runlog — 2026-10-21

## In Progress

- Phase 1: Dispatcher scheduled task (weekday mornings) — verify first real digest arrives

## Blocked

## Next Steps

1. **Phase 1 — Foundation + Dispatcher**: create `ops/` skeleton (README, `agents/dispatcher.md` charter, `state/facts.md`, `state/runlog.md`); create the Dispatcher scheduled task (weekday mornings) and verify one real digest arrives
2. **Phase 2 — Lead Agent**: write `sops/lead-intake.md` + `agents/lead-agent.md`; schedule 3×/business day; verify it logs leads and produces reply drafts without sending
3. **Phase 3 — Money Agent**: write `sops/invoice-dunning.md` + charter; weekly chase run with draft reminders; weekly cash/AR snapshot line in the digest
4. **Phase 4 — Jobs Agent**: write `sops/job-documentation.md` + charter; daily CompanyCam documentation audit
5. **Phase 5 — Reporter**: charter + Friday weekly pulse; starts `state/scorecard.md` history

Each phase is its own small PR; run a phase for at least a few days before starting the next.

## Session Notes

- 2026-10-19: Spec created. Owner chose "business ops automation hub" scope and spec-first approach. No application code involved; see decisions.md ADR-001 for why state lives in this repo.
