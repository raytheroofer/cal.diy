# Agentic Operating System Future Work

Ideas and enhancements deferred from initial implementation.

## Enhancements

- **Earned autonomy**: after N weeks of clean drafts, promote specific Lead Agent replies (e.g. "send available estimate slots" template) from Ring 1 to Ring 0 with a bounded template
- **Hiring agent**: Indeed search + screening prep for crew hires (small-business plugin has building blocks)
- **Review/reputation agent**: post-job review requests and response drafts
- **Cal.diy integration**: expose estimate-slot availability through a Cal.diy event type so the Lead Agent links a real booking page instead of listing slots
- **Dashboard artifact**: a published scorecard page fed from `ops/state/scorecard.md` once Reporter has a few weeks of history
- **Meeting capture**: pipe Wispr Flow meeting notes into the runlog/digest for jobs discussed by phone

## Technical Debt

- If `ops/` churn gets noisy in this product fork, extract it to a dedicated ops repository (keeps ADR-001's versioning benefits)
- Lead log may outgrow markdown → move to a Sheet with a pointer from `ops/state/leads.md` (flagged in ADR-001)

## Nice to Have

- Slack as the console (digest + approve-drafts flow in a channel) instead of email
- Monthly "state of the business" report assembled from weekly scorecards
