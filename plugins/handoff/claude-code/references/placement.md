# Placement

Default generated output location:

`.agents/.handoff/<task-slug>.md`

Rules:

- use this location unless the project already has a clearly established working-notes directory
- create `.agents/.handoff/` first if it does not exist
- keep the filename short, kebab-cased, and task-specific
- overwrite the same file when resuming the same task unless the user asks for versioning
- keep generated handoff docs outside the skill directory

## Committing handoff notes

Commit handoff notes to the repository when the handoff is between people or long-running sessions where the note must survive outside the local environment. Treat them as ephemeral scratch (add to `.gitignore`) when the session is short-lived or the note is only for local continuity.

If unsure, commit — a stale handoff note is harmless; a lost one is not.
