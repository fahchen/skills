# Update Project Knowledge

When new knowledge is gained during development, update the appropriate knowledge files.

## File Roles

| File | Context loading | Purpose |
|------|----------------|---------|
| `knowledge.md` | full | rules, constraints, guardrails |
| `patterns.md` | full | implementation catalog with file pointers |
| `improvements.md` | titles only | lessons from wrong turns |
| `postmortems/` | frontmatter only | detailed wrong-path writeups |

## Rules

1. No duplication across files
2. No code blocks in `knowledge.md`
3. Prefer pointers to copies
4. Verify file references after edits
5. Graduate repeated improvements into `knowledge.md`

## Categorize

- new constraint or convention -> `knowledge.md`
- implementation recipe or file pointer -> `patterns.md`
- one-time bug fix or trial/error -> `improvements.md`
- context-heavy wrong path -> `postmortems/`

## Postmortem Workflow

When a session includes a significant wrong path:

1. write a full postmortem in `postmortems/YYYY-MM-DD-brief-slug.md`
2. extract a short entry into `improvements.md`
3. if the lesson becomes a standing rule, also add it to `knowledge.md`

Use [postmortem-template.md](postmortem-template.md) for the full writeup format.

## Task

1. Review the session and identify new knowledge
2. Put each item in the right file
3. Update instead of duplicating similar existing entries
4. Promote repeated improvements to standing rules when appropriate
5. Verify any touched file references remain accurate
