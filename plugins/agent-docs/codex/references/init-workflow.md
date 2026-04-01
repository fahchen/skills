# Initialize Agent Documentation

Set up a `docs/agents/` knowledge base for AI agents working on this project.

## What This Creates

```text
project/
├── docs/agents/
│   ├── knowledge.md
│   ├── patterns.md
│   ├── improvements.md
│   └── postmortems/
└── AGENTS.md / CLAUDE.md
```

## Execution Steps

### Step 1: Ask for location

Ask the user where to put the agent documentation. Suggest `docs/agents/` as the default.

### Step 2: Create directory

```bash
mkdir -p <chosen-path>
```

### Step 3: Create `<chosen-path>/knowledge.md`

```markdown
# Knowledge

Compact rules and constraints. This file is always loaded into agent context, so keep it concise. No code blocks. Put implementation examples in `patterns.md`.

> Rules must be generic and principle-based — not tied to specific module names, function signatures, or file paths. Those are derivable from the code. Before adding, check for similar or conflicting entries and merge/resolve rather than accumulate.

<!-- Entry format:

### [Title]

**Rule:** One-line actionable constraint.
**Why:** One-line rationale.
**Reference:** `path/to/file:line` (optional)
-->
```

### Step 4: Create `<chosen-path>/patterns.md`

```markdown
# Patterns

Implementation catalog with file references. Keep entries short. Source files are authoritative.

<!-- Entry format:

### [Title]

One-line description of when to use this pattern.
**Reference:** `path/to/file:line-line`
-->
```

### Step 5: Create `<chosen-path>/improvements.md`

```markdown
# Improvements

Lessons from wrong turns. Only titles are loaded into context by default.

<!-- Entry format:

## YYYYMMDD: [Title]

**Problem:** ...
**Root Cause:** ...
**Fix:** ...
**Guardrail:** ...
**Reference:** `path/to/file` or `postmortems/YYYY-MM-DD-slug.md`
-->
```

### Step 6: Create postmortems directory

```bash
mkdir -p <chosen-path>/postmortems
```

### Step 7: Update root instruction files

Add or update a `## Project Knowledge` section in:

- `AGENTS.md` if it exists
- `CLAUDE.md` if it exists
- if neither exists, create `AGENTS.md`

Suggested section:

```markdown
## Project Knowledge

Before coding, load project knowledge into context:

1. Read fully: `<chosen-path>/knowledge.md` and `<chosen-path>/patterns.md`
2. Scan titles only: `<chosen-path>/improvements.md`
3. Scan frontmatter only: `<chosen-path>/postmortems/*.md`
```

### Step 8: Report completion

List created files and remind the user:

- `knowledge.md` holds concise rules
- `patterns.md` points to implementation patterns
- `improvements.md` records short lessons
- `postmortems/` holds longer incident writeups
