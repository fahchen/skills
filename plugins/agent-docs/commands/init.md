---
description: Initialize agent documentation structure (docs/agents/) for a project
allowed-tools: Write, Edit, Read, Glob, Bash(mkdir:*)
---

# Initialize Agent Documentation

Set up a `docs/agents/` knowledge base for AI agents working on this project.

## What This Creates

```
project/
├── docs/agents/
│   ├── knowledge.md      # Patterns, design decisions
│   ├── patterns.md       # Standard implementations
│   └── improvements.md   # Debugging lessons
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
# Project Knowledge

Implementation patterns and design decisions.

<!-- Add entries using this format:

## [Category]

### [Pattern Name]

**Rule:** What to do

**Why:** Why it matters

**Reference:** `path/to/file.ts:line` - brief description
-->
```

### Step 4: Create `<chosen-path>/patterns.md`

```markdown
# Patterns

Standard implementations. Use file references as authoritative source.

<!-- Add entries using this format:

## [Pattern Name]

Brief description of when to use this pattern.

**Reference:** `path/to/file.ts:line-line`
-->
```

### Step 5: Create `<chosen-path>/improvements.md`

```markdown
# Improvements

Trials, errors, and fixes from development.

<!-- Add dated entries using this format:

## YYYYMMDD: [Brief Title]

**Problem:** What went wrong

**Root Cause:** Why it happened

**Fix:** How it was resolved

**Lesson:** What to remember

**Reference:** `path/to/file.ts`
-->
```

### Step 6: Update CLAUDE.md

If `CLAUDE.md` exists in the project root, append this section:

```markdown

## Project Knowledge

**MUST read before coding:** Review [<chosen-path>](<chosen-path>) and follow the established patterns:

- `knowledge.md`: Naming conventions, design decisions
- `patterns.md`: Logging, testing, standard implementations
- `improvements.md`: Past mistakes to avoid

Use `/agent-docs:update-knowledge` to capture new learnings after a session.
```

If `CLAUDE.md` does not exist, skip this step.

### Step 7: Report completion

List created files and remind the user:
- Populate `knowledge.md` with patterns as they learn the codebase
- Use `/agent-docs:update-knowledge` after sessions to capture learnings
