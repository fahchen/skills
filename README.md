# Skills

Personal Claude Code plugins and matching Codex skills, co-located per plugin under `plugins/`.

## Installation

### Claude Code

```bash
# Add marketplace (once)
/plugin marketplace add fahchen/skills

# Install specific plugin
/plugin install <plugin-name>@fahchen-skills
```

### Codex

Copy the skill you want from `plugins/<skill-name>/codex/` into `~/.codex/skills/`, then restart Codex.

```bash
cp -R plugins/<skill-name>/codex ~/.codex/skills/<skill-name>
```

## Available Plugins

### aura

Real-time HUD integration for Claude Code and Codex sessions.

- Claude Code: `/plugin install aura@fahchen-skills`
- Codex: `cp -R plugins/aura/codex ~/.codex/skills/aura`
- Requires: [Aura daemon](https://github.com/fahchen/aura) running

### bdd

BDD discovery and consistency-checking workflows for specification work.

- Claude Code: `/plugin install bdd@fahchen-skills`
- Codex: `cp -R plugins/bdd/codex ~/.codex/skills/bdd`

### agent-docs

Tiered project knowledge base bootstrap and maintenance for agent-driven projects.

- Claude Code: `/plugin install agent-docs@fahchen-skills`
- Codex: `cp -R plugins/agent-docs/codex ~/.codex/skills/agent-docs`

### handoff

Concise transfer notes for ongoing work, with explicit human decisions and one clear next step.

- Claude Code: `/plugin install handoff@fahchen-skills`
- Codex: `cp -R plugins/handoff/codex ~/.codex/skills/handoff`

### workflow

Engineering workflow guidance for planning, implementation, and code review.

- Claude Code: `/plugin install workflow@fahchen-skills`
- Codex: `cp -R plugins/workflow/codex ~/.codex/skills/workflow`

## Structure

```text
skills/
├── .claude-plugin/
│   └── marketplace.json
└── plugins/
    └── <plugin>/
        ├── claude-code/         # Claude Code plugin
        │   ├── .claude-plugin/
        │   │   └── plugin.json
        │   ├── commands/
        │   ├── hooks/
        │   ├── references/
        │   └── skills/
        └── codex/               # Codex skill
            ├── agents/
            ├── references/
            └── SKILL.md
```
