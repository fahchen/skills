---
name: agent-docs
description: Use this skill to initialize or maintain a tiered docs/agents knowledge base for a project.
version: 2.0.0
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Bash
---

# Agent Docs

This Codex skill intentionally bundles the whole `agent-docs` plugin into one skill so Codex users install only one entry point.

## Workflow Selection

Choose the workflow that matches the user's request:

- Use **Init** when the user wants to:
  - initialize agent docs
  - bootstrap `docs/agents/`
  - set up project knowledge files
- Use **Update Knowledge** when the user wants to:
  - capture session learnings
  - update project knowledge
  - record improvements or postmortems

## Init Workflow

For initialization, follow [references/init-workflow.md](references/init-workflow.md).

## Update Knowledge Workflow

For knowledge maintenance, follow [references/update-knowledge-workflow.md](references/update-knowledge-workflow.md).

Use [references/postmortem-template.md](references/postmortem-template.md) when the workflow calls for a detailed postmortem.
