# Agent Docs Plugin

Initialize agent documentation structure for any project.

## Usage

```
/agent-docs:init
```

## What It Creates

```
project/
├── docs/agents/
│   ├── knowledge.md      # Compact guardrails — always in context
│   ├── patterns.md       # Implementation catalog — always in context
│   ├── improvements.md   # Lessons learned — titles in context, detail on demand
│   └── postmortems/      # Wrong-path writeups — frontmatter in context, detail on demand
└── CLAUDE.md             # Updated with tiered loading instructions
```

## Commands

- `/agent-docs:init` - Initialize docs/agents/ structure
- `/agent-docs:update-knowledge` - Capture learnings from current session

## How Context Loading Works

Agents load project knowledge in tiers:

1. **Always loaded (full):** `knowledge.md` + `patterns.md` — compact by design
2. **Titles only:** `improvements.md` headings — agent reads full entry when relevant
3. **Frontmatter only:** `postmortems/*.md` descriptions — agent reads full postmortem when relevant

## Workflow

1. Run `/agent-docs:init` in a new project
2. As you work, add rules to `knowledge.md` (no code blocks, just constraints)
3. Add implementation pointers to `patterns.md` (reference source files)
4. Record wrong turns in `improvements.md`
5. Write postmortems for significant incidents in `postmortems/`
6. Graduate recurring improvement lessons into `knowledge.md` rules
7. Use `/agent-docs:update-knowledge` after sessions

## Philosophy

- **Tiered loading**: Critical rules always in context; details on demand
- **No duplication**: Each piece of knowledge lives in ONE file
- **Pointers over copies**: Reference `file:line` instead of copying code
- **Graduate lessons**: Recurring improvements become standing rules
