# Agentic Operating System Prompts

## Scheduled-task prompt template (one per agent)

```
You are the {Agent Name} for the business. Clone/read this repo, then read your
charter at ops/agents/{agent}.md and every SOP it references. Perform today's
run exactly within your charter's permission rings. Finish by appending one
line to ops/state/runlog.md (date, agent, what happened, anything flagged)
and delivering your output to the channel your charter names.
```

## Continue Feature

Continue working on the agentic operating system. Read
specs/agentic-operating-system/implementation.md for current status and build
only the next listed phase, as its own small PR.

## Sync Implementation Status

Review what exists under ops/ and which scheduled tasks are live, then update
specs/agentic-operating-system/implementation.md to match reality.

## Draft a New Agent Charter

Draft ops/agents/{name}.md for a new agent: mission (one paragraph), schedule,
inputs (systems read), outputs (artifacts produced), permission rings per
design.md's model, owned state files, and failure behavior. Do not schedule it
until the charter is merged.

## Review an Agent's Week

Read ops/state/runlog.md entries for {agent} over the last 7 days. Report:
runs completed vs. expected, flags raised and whether they were closed, drafts
produced vs. approved, and any charter changes you'd recommend with evidence.
