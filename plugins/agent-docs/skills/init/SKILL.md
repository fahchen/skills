---
name: init
description: Initialize agent documentation structure (docs/agents/) for a project. Use when the user asks to "initialize agent docs", "bootstrap docs/agents/", or "set up project knowledge files".
user-invocable: true
allowed-tools: Write, Edit, Read, Glob, Bash(mkdir:*)
---

# Initialize Agent Documentation

Set up a `docs/agents/` knowledge base for AI agents working on this project.

## What This Creates

```
project/
├── docs/agents/
│   ├── knowledge.md      # Compact guardrails — always loaded into context
│   ├── patterns.md       # Implementation catalog — always loaded, read source on demand
│   ├── improvements.md   # Lessons learned — only titles loaded, read detail on demand
│   └── postmortems/      # Wrong-path writeups — only frontmatter loaded, read on demand
└── CLAUDE.md             # Updated with Project Knowledge section
```

## Execution Steps

### Step 1: Ask for location

Ask the user where to put the agent documentation. Suggest `docs/agents/` as the default.

Example: "Where should I create the agent docs? (default: `docs/agents/`)"

### Step 2: Create directory

```bash
mkdir -p <chosen-path>
```

### Step 3: Create `<chosen-path>/knowledge.md`

```markdown
# Knowledge

Compact rules and constraints. This file is **always loaded into agent context** — keep it concise. Prefer prose; add a short code block only when it meaningfully clarifies a rule that would otherwise be ambiguous. Put implementation examples in `patterns.md`.

When a lesson from `improvements.md` becomes a standing rule, **graduate it here** and remove the improvement entry to avoid duplication.

> **Rules must be generic and principle-based.** Do not include specific module names, function signatures, file paths, or implementation examples — those are derivable from the code. Capture only non-obvious design decisions, constraints, and invariants that cannot be inferred by reading the source. Before adding a new rule, check for similar or conflicting entries and merge/resolve rather than accumulate.

<!-- Entry format:

### [Title]

**Rule:** One-line actionable constraint.

**Why:** One-line rationale (skip if self-evident).

**Reference:** `path/to/file:line` (optional)
-->
```

### Step 4: Create `<chosen-path>/patterns.md`

```markdown
# Patterns

Implementation catalog with file references. This file is **always loaded into agent context** — keep entries short. Source files are authoritative; this file points to them.

When adding a pattern, check if a matching rule exists in `knowledge.md`. Rules say "what/why"; patterns say "where to look for how."

<!-- Entry format:

### [Title]

One-line description of when to use this pattern.

**Reference:** `path/to/file:line-line`
-->
```

### Step 5: Create `<chosen-path>/improvements.md`

```markdown
# Improvements

Lessons from wrong turns. Only **titles are loaded into agent context** — the agent reads full entries on demand when a title seems relevant to the current task.

If a lesson becomes a standing rule, **graduate it to `knowledge.md`** and remove it from here.

Detailed incident writeups with full context live in `postmortems/`.

<!-- Entry format:

## YYYYMMDD: [Title that describes the lesson, not just the incident]

**Problem:** What went wrong

**Root Cause:** Why it happened

**Fix:** How it was resolved

**Guardrail:** What quick check prevents this next time

**Reference:** `path/to/file` or `postmortems/YYYY-MM-DD-slug.md`
-->
```

### Step 6: Create postmortems directory

```bash
mkdir -p <chosen-path>/postmortems
```

### Step 7: Update CLAUDE.md

Add the following section to `CLAUDE.md` in the project root. If `CLAUDE.md` does not exist, create it. If it already contains a `## Project Knowledge` section, update it in place.

```markdown

## Project Knowledge

Before coding, load project knowledge into context:

1. **Read fully:** `<chosen-path>/knowledge.md` and `<chosen-path>/patterns.md` — these are compact and always relevant
2. **Scan titles only:** `<chosen-path>/improvements.md` — read the `## YYYYMMDD: Title` headings. Read the full entry only when a title is relevant to your current task
3. **Scan frontmatter only:** `<chosen-path>/postmortems/*.md` — read the YAML `description:` field and file path. Read the full postmortem only when relevant

Use `/agent-docs:update-knowledge` to capture new learnings after a session.
```

### Step 8: Report completion

List created files and remind the user:
- `knowledge.md` — add rules as constraints are discovered (keep concise, no code blocks)
- `patterns.md` — add implementation pointers as patterns emerge (reference source files)
- `improvements.md` — graduate recurring lessons into `knowledge.md` rules
- Use `/agent-docs:update-knowledge` after sessions to capture learnings
