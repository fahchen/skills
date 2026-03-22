---
name: aura
description: Use this skill when the user wants Aura HUD integration in Codex, especially for setting or updating the current session name.
version: 2.0.0
user-invocable: true
allowed-tools:
  - Bash
hooks:
  PreToolUse:
    - matcher: "*"
      hooks:
        - type: command
          command: "aura hook --agent codex"
  PostToolUse:
    - matcher: "*"
      hooks:
        - type: command
          command: "aura hook --agent codex"
  Stop:
    - hooks:
        - type: command
          command: "aura hook --agent codex"
---

# Aura

Use this skill when the user wants the Aura HUD to track a Codex session.

## What This Skill Covers

- Setting a concise session name with `aura set-name "<name>"`
- Keeping Aura in sync during Codex tool activity through the hook declarations in this skill

## Command

```bash
aura set-name "<name>"
```

## Important: Where This Works

Aura does not read `aura set-name ...` from arbitrary terminals. The HUD updates only when Aura observes the command inside an agent event stream.

- Claude Code: run it inside the session via the `Bash` tool
- Codex: run it inside the session via the `exec_command` tool

If you run `aura set-name ...` in another terminal, you may see local stdout but the HUD will not change.

## Session Naming Rules

The session name should describe what the current task is trying to accomplish.

- Simple task: "Fix Login"
- Multi-phase task: "Add Auth - Write Tests"

Use `Task - Step` format when:

- The task has 3 or more distinct phases
- Each phase is significant enough that the user benefits from seeing current progress

## Must Set the Name When

1. The user requests a concrete task
2. Work begins from a plan file
3. The user explicitly asks to name the session

## Do Not Change the Name When

- You are only exploring code or answering questions
- You are moving through routine commands for the same task
- The current name already reflects the actual goal

## Naming Patterns

- `Fix <Thing>`
- `Add <Thing>`
- `Refactor <Area>`
- `Update <Thing>`
- `<Task> - <Step>`
