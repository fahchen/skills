---
description: Capture learnings from current session into agent documentation
---

# Update Project Knowledge

When new knowledge is gained during development, update the appropriate knowledge files.

## Files to Update

- `docs/agents/knowledge.md`: Patterns, design decisions, context
- `docs/agents/improvements.md`: Trials, errors, fixes
- `docs/agents/patterns.md`: Standard implementations with file references

## Rules

1. **Prefer pointers to copies.** Use `file:line` references instead of code snippets.

2. **Keep entries concise.** State the rule/pattern, why it matters, and reference the implementation.

3. **Verify references.** When referenced files are modified, check that `file:line` pointers are still accurate.

4. **Use consistent format:**
   - `**Rule:**` for prescriptive guidance
   - `**Why:**` for rationale
   - `**Reference:**` for file:line pointers
   - `**Problem/Fix/Lesson:**` for improvements

5. **Categorize appropriately:**
   - New pattern learned → `knowledge.md`
   - Bug fix or trial/error → `improvements.md`
   - Reusable implementation → `patterns.md`

## Task

1. Review the current conversation and identify any new knowledge that should be captured.
2. Update the appropriate files with concise entries using file references.
3. Verify existing `file:line` references are still accurate if those files were modified.
