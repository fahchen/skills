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

Copy the plugin directory into `~/.codex/plugins/`. Codex discovers skills via `.codex-plugin/plugin.json`.

```bash
cp -R plugins/<plugin-name> ~/.codex/plugins/<plugin-name>
```

For aura (Codex-specific skill with inline hooks):

```bash
cp -R plugins/aura/codex ~/.codex/skills/aura
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
- Codex: `cp -R plugins/bdd ~/.codex/plugins/bdd`

### agent-docs

Tiered project knowledge base bootstrap and maintenance for agent-driven projects.

- Claude Code: `/plugin install agent-docs@fahchen-skills`
- Codex: `cp -R plugins/agent-docs ~/.codex/plugins/agent-docs`

### handoff

Concise transfer notes for ongoing work, with explicit human decisions and one clear next step.

- Claude Code: `/plugin install handoff@fahchen-skills`
- Codex: `cp -R plugins/handoff ~/.codex/plugins/handoff`

### workflow

Engineering workflow guidance for planning, implementation, and code review.

- Claude Code: `/plugin install workflow@fahchen-skills`
- Codex: `cp -R plugins/workflow ~/.codex/plugins/workflow`

## Releasing a New Version

When updating a plugin, bump the version in both manifests:

- `plugins/<name>/.claude-plugin/plugin.json`
- `plugins/<name>/.codex-plugin/plugin.json`

Use semantic versioning: patch for fixes/copy, minor for new features/rules, major for breaking API changes.

## Structure

```text
plugins/<plugin>/
├── .claude-plugin/plugin.json    # Claude Code manifest
├── .codex-plugin/plugin.json     # Codex manifest (skills: ./skills/)
├── hooks/                        # Claude Code hooks (optional, e.g., aura)
├── skills/
│   └── <skill>/
│       ├── SKILL.md              # Shared skill definition
│       ├── agents/openai.yaml    # Codex UI metadata
│       └── references/           # Supporting docs
└── codex/                        # Codex-only skill (only when platform-specific, e.g., aura)
```

Aura is the only plugin that keeps a `codex/` directory because its Codex SKILL.md has platform-specific inline hooks that can't be shared with the Claude Code variant.
