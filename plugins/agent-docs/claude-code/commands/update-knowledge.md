---
description: Capture learnings from current session into agent documentation
---

# Update Project Knowledge

When new knowledge is gained during development, update the appropriate knowledge files.

## File Roles

| File | Context loading | Purpose | Keep it... |
|------|----------------|---------|------------|
| `knowledge.md` | Full file — always | Rules, constraints, guardrails | Concise. No code blocks. One rule per entry. |
| `patterns.md` | Full file — always | Implementation catalog with file pointers | Short entries. Source files are authoritative. |
| `improvements.md` | Titles only | Lessons from wrong turns | Actionable. Graduate recurring lessons to `knowledge.md`. |
| `postmortems/` | Frontmatter only | Detailed wrong-path writeups | Thorough. Full context for complex incidents. |

## Rules

1. **Good code over documentation.** Before writing a `patterns.md` or `knowledge.md` entry for a pattern, ask: can this be expressed as well-written code? A shared component, named function, or clear API is better than an external doc entry. Document only constraints, non-obvious decisions, and context that cannot live in the code itself. Bug fixes already reflected in the code do not need a knowledge entry.

2. **No duplication across files.** A lesson appears in ONE place. If an improvement becomes a standing rule, move it to `knowledge.md` and remove the improvement entry. If a pattern overlaps a rule, the rule goes in `knowledge.md` (what/why) and the pattern goes in `patterns.md` (where to look).

3. **No code blocks in knowledge.md.** Rules are constraints, not implementations. Point to `patterns.md` or source files for code examples.

4. **Prefer pointers to copies.** Use `file:line` references instead of code snippets. Source files are authoritative — copied code goes stale.

5. **Verify references.** When referenced files are modified, check that `file:line` pointers are still accurate.

6. **Graduate improvements.** After an improvement entry is applied 2+ times, or when it represents a permanent constraint, extract the rule into `knowledge.md` and remove the improvement entry.

## Entry Formats

### knowledge.md

```markdown
### [Clear, descriptive title]

**Rule:** One-line actionable constraint.

**Why:** One-line rationale (skip if self-evident).

**Reference:** `path/to/file:line` (optional)
```

### patterns.md

```markdown
### [Title — matches knowledge.md rule if related]

One-line description of when to use this pattern.

**Reference:** `path/to/file:line-line`
```

### improvements.md

```markdown
## YYYYMMDD: [Title that describes the lesson, not just the incident]

**Problem:** What went wrong

**Root Cause:** Why it happened

**Fix:** How it was resolved

**Guardrail:** What quick check prevents this next time

**Reference:** `path/to/file` or `postmortems/YYYY-MM-DD-slug.md`
```

## Categorize

- New constraint or convention → `knowledge.md`
- Implementation recipe or reference file → `patterns.md`
- One-time bug fix or trial/error → `improvements.md`
- Wrong path with important context that a short entry would lose → `postmortems/` (see Postmortem Workflow below)

## Postmortem Workflow

When a session includes a significant wrong path (user correction, surprising failure, or context-heavy lesson):

1. Write a full postmortem at `postmortems/YYYY-MM-DD-brief-slug.md` using the postmortem template (see `references/postmortem-template.md`)
2. Extract a short improvement entry into `improvements.md` linking back to the postmortem
3. If the lesson reveals a standing rule, also add it to `knowledge.md`

### When to write a postmortem

- The agent explored a wrong path before reaching the right solution
- The user corrected an approach and that correction should become reusable guidance
- The fix only makes sense with the surrounding context, tradeoffs, or timeline
- A short improvement entry would lose important detail about why the mistake happened

### Style

- Record the wrong assumption, not just the bad outcome
- Prefer guardrails that future agents can apply before coding
- Keep references concrete: files, specs, logs, or commits
- Distinguish between user correction and agent self-correction

## Task

1. Review the current conversation and identify new knowledge to capture.
2. For each piece of knowledge, determine the right file (see Categorize above).
3. Check for duplication — does a similar entry already exist? Update rather than duplicate.
4. Check for graduation — should any improvement entries be promoted to rules in `knowledge.md`?
5. For postmortem-worthy incidents, follow the Postmortem Workflow above.
6. Verify existing `file:line` references are still accurate if those files were modified.
