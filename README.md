# Skills

Personal Claude Code skills and plugins.

## Installation

```bash
# Add marketplace (once)
/plugin marketplace add fahchen/skills

# Install specific plugin
/plugin install <plugin-name>@fahchen-skills
```

## Available Plugins

### aura

Real-time HUD for Claude Code sessions - displays status, tools, and attention indicators.

```bash
/plugin install aura@fahchen-skills
```

**Requires:** [Aura daemon](https://github.com/fahchen/aura) running

## Structure

```
skills/
├── .claude-plugin/
│   └── marketplace.json    # Plugin registry
├── aura/                   # Aura HUD plugin
│   ├── hooks/
│   └── skills/
└── <future-plugin>/        # Add more plugins here
```
