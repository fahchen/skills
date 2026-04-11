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

Copy the plugin directory into `~/.codex/plugins/`, then restart Codex. Codex discovers skills via `.codex-plugin/plugin.json`.

```bash
cp -R plugins/<plugin-name> ~/.codex/plugins/<plugin-name>
```

For plugins without a `.codex-plugin/` manifest, copy the `codex/` subdirectory as a skill:

```bash
cp -R plugins/<plugin-name>/codex ~/.codex/skills/<plugin-name>
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
- Codex: `cp -R plugins/agent-docs/codex ~/.codex/skills/agent-docs`

### handoff

Concise transfer notes for ongoing work, with explicit human decisions and one clear next step.

- Claude Code: `/plugin install handoff@fahchen-skills`
- Codex: `cp -R plugins/handoff/codex ~/.codex/skills/handoff`

### workflow

Engineering workflow guidance for planning, implementation, and code review.

- Claude Code: `/plugin install workflow@fahchen-skills`
- Codex: `cp -R plugins/workflow/codex ~/.codex/skills/workflow`

## Releasing a New Version

When updating a plugin, bump the version in the plugin manifest(s):

- `plugins/<name>/.claude-plugin/plugin.json` — Claude Code plugin version
- `plugins/<name>/.codex-plugin/plugin.json` — Codex plugin version (if unified structure)
- `plugins/<name>/codex/SKILL.md` — Codex skill version frontmatter (if legacy structure)

Use semantic versioning: patch for fixes/copy, minor for new features/rules, major for breaking API changes.

## Structure

Plugins use either a unified or split layout:

### Unified (preferred — e.g., bdd)

Both platforms share `skills/`, discovered via their respective manifests.

```text
plugins/<plugin>/
├── .claude-plugin/plugin.json    # Claude Code manifest
├── .codex-plugin/plugin.json     # Codex manifest (skills: ./skills/)
└── skills/
    └── <skill>/
        ├── SKILL.md              # Shared skill definition
        ├── agents/openai.yaml    # Codex UI metadata
        └── references/
```

### Split (legacy — e.g., aura, handoff)

Separate directories per platform.

```text
plugins/<plugin>/
├── claude-code/
│   ├── .claude-plugin/plugin.json
│   └── skills/
└── codex/
    ├── agents/openai.yaml
    ├── SKILL.md
    └── references/
```
