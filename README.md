# Aura Claude Code Plugin

Claude Code plugin for [Aura](https://github.com/fahchen/aura) - a real-time HUD that monitors AI coding sessions.

## Installation

```bash
# Add marketplace
/plugin marketplace add fahchen/aura-claude-code-plugin

# Install plugin
/plugin install aura@aura-claude-code-plugin
```

## Prerequisites

The Aura daemon must be running for the plugin to work:

```bash
# Build and run the daemon (from the main aura repo)
cargo build --release -p aura-daemon
./target/release/aura-daemon
```

Or install the pre-built binary and add it to your PATH.

## What This Plugin Does

- **Hooks**: Sends session lifecycle events to the Aura daemon via IPC
  - Session start/end
  - Tool execution (Read, Edit, Bash, etc.)
  - Permission requests (needs attention)
  - Context compacting

- **Skills**: Provides the `aura:session-naming` skill for setting HUD session names

## Related

- [Aura](https://github.com/fahchen/aura) - Main repository with the daemon and HUD
