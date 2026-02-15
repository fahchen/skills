---
name: session-naming
description: Set descriptive names for Aura HUD session display
version: 2.0.0
---

# Aura Session Naming

Set descriptive session names in the Aura HUD to help users track what each session is working on.

## Command

```bash
aura set-name "<name>"
```

## Important: Where This Works

Aura does not read `aura set-name ...` from arbitrary terminals. The HUD name updates only when Aura observes this command inside an agent event stream.

- Claude Code: run it inside the session via the `Bash` tool (hooks capture `tool_input.command`).
- Codex: run it inside the session via the `exec_command` tool (it appears in the rollout JSONL).

If you run `aura set-name ...` in another terminal, you'll see local stdout but the HUD will not change.

## Rules

### Session name = Overall goal, optionally with current step

The session name should describe **what we're trying to accomplish**. For larger tasks with distinct phases, use `Task - Step` format.

- Simple task: "Fix Login" (stays the same throughout)
- Multi-phase task: "Add Auth - Write Tests" -> "Add Auth - Integration" (task stays, step updates)

Use `Task - Step` format when:

- The task has 3+ distinct phases (design, implement, test, deploy)
- Each phase takes significant time
- User benefits from knowing current progress

### MUST set session name when

1. User requests a task (e.g., "fix the login bug", "add dark mode")
2. Starting work from a plan file
3. User explicitly asks to name the session

### DO NOT set/change session name when

- Exploring code or answering questions
- Moving to the next step of the same task
- Running commands as part of the current task
- Name already reflects the current goal

## Naming Rules

| Rule | Good | Bad |
|------|------|-----|
| 2-6 words | "Fix Login Auth Flow" | "Fix the authentication bug in the login system" |
| Start with verb | "Add Export" | "Export Feature" |
| No filler words | "Refactor API" | "Refactor the API Layer" |
| No paths/files | "Update Config" | "Update src/config.ts" |
| No jargon | "Speed Up Search" | "Optimize O(n^2)" |

## Examples

| User Request | Session Name |
|--------------|--------------|
| "Fix the bug where login fails" | "Fix Login" |
| "Add a button to export data" | "Add Export" |
| "Refactor the authentication code" | "Refactor Auth" |
| "Add dark mode to the app" | "Add Dark Mode" |
| "Help me understand this code" | (don't set) |
| "Run the tests" | (don't set) |
| Large feature with phases | "Add Auth - Design" -> "Add Auth - Implement" -> "Add Auth - Test" |

## Patterns

- `Fix [Thing]` -> "Fix Login", "Fix Cart"
- `Add [Thing]` -> "Add Export", "Add Dark Mode"
- `Refactor [Area]` -> "Refactor Auth", "Refactor API"
- `Update [Thing]` -> "Update Deps", "Update Config"
- `[Task] - [Step]` -> "Add Auth - Design", "Add Auth - Implement", "Add Auth - Test"
