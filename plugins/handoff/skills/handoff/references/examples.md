# Examples

## Example: creating the skill from this conversation

```md
# Handoff

## Task
Add a `handoff` skill that works in both Claude and Codex.

## Current Status
The skill scope is a lightweight, user-invoked handoff note with matching Claude plugin packaging and Codex skill packaging.

## Plan
- review existing skill and plugin patterns
- define the minimal handoff skill shape
- record human decisions clearly
- choose a default storage location for generated handoff docs
- wire the skill into both Claude and Codex packaging

## Completed
- existing plugin and skill patterns were reviewed
- the scope was reduced from a broader handoff system
- `Human Decisions` was made a required section
- the default output location was set to `.agents/.handoff/<task-slug>.md`
- Claude plugin packaging and Codex skill packaging were aligned

## Human Decisions
- the skill must be explicitly invoked by the user
- the handoff note must include decisions made by a human
- the generated document should live under `.agents/.handoff/`
- the repository should ship matching Claude and Codex support for `handoff`

## AI Assumptions or Suggestions
- the next iteration may want a small writer helper to create the handoff note file
- keep speculative items out unless they help the next person continue the work

## Open Questions
- should plan items be annotated with status markers in the generated note

## Blockers
None.

## Next Step
Publish the `handoff` plugin in the Claude marketplace and document installation for both agents.
```
