# Postmortem Template

## File naming

`YYYY-MM-DD-brief-slug.md` stored directly under `postmortems/`.

Examples:
- `postmortems/2026-03-15-liveview-state-wrong-source-of-truth.md`
- `postmortems/2026-03-18-agent-tried-full-crud-facade-too-early.md`

## Full postmortem format

```markdown
---
id: PM-YYYY-MM-DD-01
date: YYYY-MM-DD
description: One-line summary of the wrong path and lesson learned
task: short task name
trigger: user_correction | self_correction
severity: low | medium | high
tags: [agent-behavior]
status: closed
related_improvement: YYYYMMDD
---

# Summary

One paragraph describing the failed attempt, the correction, and the final direction.

## Context

- What task was being worked on?
- What modules, docs, or specs were relevant?
- What constraints mattered?

## Failed Attempt

- What did the agent try?
- Why did that approach seem reasonable at the time?
- What assumption was being made?

## Why It Was Wrong

- Which project rule, design constraint, or observed behavior did it conflict with?
- Was the issue about correctness, scope, architecture, workflow, or tooling?

## Correction

- How was the mistake corrected?
- Was the correction made by the user or by the agent?
- What is the correct approach next time?

## Guardrail

- Write 1-3 short rules that a future agent can apply before repeating this mistake.

## Signals

- What symptoms, errors, or review comments would reveal the same mistake again?

## Follow-up

- Should `improvements.md` get a short entry?
- Should `knowledge.md`, `patterns.md`, or a spec be updated?
- Is there any missing test or verification step that should be standardized?

## References

- Relevant files:
- Relevant specs:
- Relevant logs or commits:
```

## Improvement entry format

After writing a full postmortem, extract a short entry into `improvements.md`:

```markdown
## YYYYMMDD: [Title that describes the lesson, not just the incident]

**Problem:** What went wrong

**Root Cause:** Why it happened

**Fix:** How it was resolved

**Guardrail:** What quick check prevents this next time

**Reference:** `postmortems/YYYY-MM-DD-brief-slug.md`
```
