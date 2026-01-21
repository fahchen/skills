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
│   ├── knowledge.md      # Patterns, design decisions
│   ├── patterns.md       # Standard implementations
│   └── improvements.md   # Debugging lessons
└── CLAUDE.md             # Updated with Project Knowledge section
```

## Commands

- `/agent-docs:init` - Initialize docs/agents/ structure
- `/agent-docs:update-knowledge` - Capture learnings from current session

## Workflow

1. Run `/agent-docs:init` in a new project
2. As you work, document patterns in `knowledge.md`
3. Record debugging lessons in `improvements.md`
4. Add reusable code patterns in `patterns.md`
5. Use `/agent-docs:update-knowledge` after sessions

## Philosophy

- **Pointers over copies**: Reference `file:line` instead of copying code
- **Concise entries**: State the rule, why it matters, where to look
- **Living docs**: Update as the codebase evolves
