# Examples

## Example: creating the skill from this conversation

```md
# Handoff

## Task
Create an initial `handoff` skill for Codex.

## Current Status
The skill scope was simplified to a lightweight, user-invoked handoff note.

## Plan
- review existing skill patterns
- define the minimal skill shape
- record human decisions clearly
- choose a default storage location for generated handoff docs
- write the initial skill files

## Completed
- Vercel-inspired patterns were reviewed
- the scope was reduced from a broader handoff system
- `Human Decisions` was made a required section
- the default output location was set to `.agents/.handoff/<task-slug>.md`

## Human Decisions
- the skill must be explicitly invoked by the user
- the handoff note must include decisions made by a human
- the generated document should live under `.agents/.handoff/`

## AI Assumptions or Suggestions
- the next iteration may want a plugin-side workflow for writing the handoff note
- keep the handoff doc concise and avoid recording speculative items unless they affect the next step

## Open Questions
- should the project later add a plugin version of the same skill
- should plan items be annotated with status markers in the generated note

## Blockers
None.

## Next Step
Draft the final handoff note for the current task and store it under `.agents/.handoff/`.
```
