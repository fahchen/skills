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

1. Good code over documentation — ask if a pattern can be expressed as well-written code before writing a doc entry. Document only constraints, non-obvious decisions, and context that cannot live in the code.
2. Generic rules only — rules must be principle-based, not tied to specific module names, function signatures, or file paths. If a rule only applies to one call site, it does not belong in `knowledge.md`.
3. Merge or resolve before adding — scan existing entries first. Merge similar entries; resolve conflicts. Prefer fewer, stronger rules over many narrow ones.
4. No duplication across files
5. No code blocks in `knowledge.md`
6. Prefer pointers to copies
7. Verify file references after edits
8. Graduate repeated improvements into `knowledge.md`

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
3. Before adding, scan for similar or conflicting entries — merge or resolve, don't accumulate
4. Promote repeated improvements to standing rules when appropriate
5. Verify any touched file references remain accurate
6. Post-update check: re-read modified files and remove entries that are too specific, redundant, or stale. The file should not grow unless genuinely new constraints were found.
