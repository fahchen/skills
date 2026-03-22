---
name: workflow
description: Use this skill when the user wants an engineering workflow with plan/code/review modes, reasoning guardrails, code quality expectations, and git conventions.
version: 1.0.0
user-invocable: true
---

# Workflow

This Codex skill bundles the Claude `workflow` plugin into one entry point for engineering workflow guidance.

## Workflow Selection

Choose the mode that matches the user's request:

- Use **Plan** when the user wants to:
  - analyze a task before implementation
  - compare options and trade-offs
  - clarify scope, constraints, or risks
  - decide on an approach for a moderate or complex task
- Use **Code** when the user wants to:
  - implement a confirmed plan
  - start coding immediately
  - make concrete changes with minimal, reviewable edits
  - specify how to verify the changes
- Use **Review** when the user wants to:
  - review current changes
  - check code quality or maintainability
  - find code smells or test gaps
  - assess conventions and documentation quality

## Common Rules

- Read the relevant code before making design claims.
- Keep reasoning specific to the current context.
- State important assumptions when proceeding under ambiguity.
- For high-risk changes, surface risks and safer alternatives.
- Prefer minimal, reviewable changes.
- Never claim tests or commands were run unless they actually were.

## Plan Mode

Use Plan mode for moderate or complex tasks that need alignment before implementation.

When entering Plan mode:

1. State the objective, key constraints, and known preconditions.
2. Do top-down analysis and identify the core path.
3. Provide 1-3 feasible options with:
   - impact scope
   - pros and cons
   - risks
   - verification method
4. Ask clarifying questions only when missing information would materially change the approach.

Exit Plan mode when the user selects an option or one option is clearly superior.

## Code Mode

Use Code mode when the plan is confirmed or when the user explicitly asks to implement.

When entering Code mode:

1. Briefly state which files or modules will change and why.
2. Implement concrete changes instead of extending the planning discussion.
3. Keep the diff minimal and reviewable.
4. Explain how to verify the result.
5. If the plan breaks down, pause and return to Plan mode.

## Review Mode

Use Review mode when evaluating existing code or diffs.

Prioritize findings in this order:

1. correctness or regression risks
2. maintainability or coupling problems
3. missing validation or test coverage
4. unclear intent, naming, or documentation issues

For each finding, describe the issue in concrete terms, point to the relevant file or module, and provide 1–2 actionable directions with brief pros/cons.

**Exit Review mode when**: all significant findings have been surfaced and the user has indicated what they want to act on.
