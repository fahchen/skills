# Skills

Personal Claude Code plugins, with matching Codex skills published directly under `.codex/skills/`.

## Installation

### Claude Code

```bash
# Add marketplace (once)
/plugin marketplace add fahchen/skills

# Install specific plugin
/plugin install <plugin-name>@fahchen-skills
```

### Codex

Copy the skill you want from `.codex/skills/` into `~/.codex/skills/`, then restart Codex.

```bash
cp -R .codex/skills/<skill-name> ~/.codex/skills/
```

This repo currently ships five Codex skills:

- `aura`
- `bdd`
- `agent-docs`
- `handoff`
- `workflow`

## Available Plugins

### aura

Real-time HUD integration for Claude Code and Codex sessions.

- Claude Code: `/plugin install aura@fahchen-skills`
- Codex: `cp -R .codex/skills/aura ~/.codex/skills/`
- Requires: [Aura daemon](https://github.com/fahchen/aura) running

### bdd

BDD discovery and consistency-checking workflows for specification work.

- Claude Code: `/plugin install bdd@fahchen-skills`
- Codex: `cp -R .codex/skills/bdd ~/.codex/skills/`

### agent-docs

Tiered project knowledge base bootstrap and maintenance for agent-driven projects.

- Claude Code: `/plugin install agent-docs@fahchen-skills`
- Codex: `cp -R .codex/skills/agent-docs ~/.codex/skills/`

### handoff

Concise transfer notes for ongoing work, with explicit human decisions and one clear next step.

- Claude Code: `/plugin install handoff@fahchen-skills`
- Codex: `cp -R .codex/skills/handoff ~/.codex/skills/`

### workflow

Engineering workflow guidance for planning, implementation, and code review.

- Claude Code: `/plugin install workflow@fahchen-skills`
- Codex: `cp -R .codex/skills/workflow ~/.codex/skills/`

## Structure

```text
skills/
├── .claude-plugin/
│   └── marketplace.json
├── .codex/
│   └── skills/
│       ├── agent-docs/
│       ├── aura/
│       ├── bdd/
│       ├── handoff/
│       └── workflow/
└── plugins/
    └── <plugin>/
        ├── .claude-plugin/
        │   └── plugin.json
        ├── commands/
        ├── hooks/
        ├── references/
        └── skills/
```

Claude stays plugin-based. Codex gets one self-contained skill per plugin, in the same style as repositories like `planning-with-files`.
