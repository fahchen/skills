---
name: handoff
description: Use this skill when the user explicitly asks for a handoff note, resume checkpoint, or transfer summary for ongoing work.
version: 0.1.0
user-invocable: true
---

# Handoff

Create a concise handoff note so another human or agent can continue without re-discovery.

## When to use

Use this skill only when the user explicitly asks to:

- hand off work
- prepare a transfer note
- create a resume checkpoint
- summarize progress for another human or agent

Do not invoke this skill implicitly.

## Workflow

1. Capture the task and current status.
2. Include the plan if the conversation contains one.
3. Separate completed work from open work.
4. List confirmed human decisions explicitly.
5. Record blockers, open questions, and the next step.
6. Write the handoff doc to `.agents/.handoff/`.

## Rules

- Keep the note concise.
- Include `Plan` only when the conversation already contains a concrete plan.
- Preserve plan structure when possible, and mark completed, in-progress, or pending items when that information is available.
- Do not invent a plan, blocker, or open question that does not appear in the conversation.
- Keep confirmed human decisions separate from AI assumptions or suggestions.
- Only include `AI Assumptions or Suggestions` when they help the next person continue the work, and keep them clearly separate from `Human Decisions`.
- End with one concrete next step. It must be a single clear action; sub-steps are allowed only when they all serve that one action.

## Output

Follow [references/template.md](references/template.md).

Use [references/examples.md](references/examples.md) for a worked example.

Use [references/placement.md](references/placement.md) for the default output location and naming rules.
