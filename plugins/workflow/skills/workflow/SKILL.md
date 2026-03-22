---
name: workflow
description: Engineering workflow — reasoning, plan/code modes, code quality, git conventions
version: 1.0.0
user-invocable: true
---

# Engineering Workflow

This plugin exposes a single `workflow` skill entry point. Ask directly for `Plan`, `Code`, or `Review` mode; separate command files are not required.

## Decision-Making & Ambiguity

- For high-risk changes, state risks and safer alternatives.
- Resolve conflicts: correctness & safety → explicit requirements → maintainability → performance → elegance.

When facing ambiguous requests:
1. Minimize unnecessary back-and-forth — only ask when missing info would significantly affect solution choice
2. Make reasonable assumptions — for low-risk decisions, proceed and state assumptions explicitly
3. When you must ask: numbered questions, prioritized by importance, briefly explain why each matters
4. Confirm understanding before proceeding with high-impact changes

---

## Reasoning Framework

Before any operation, complete internal reasoning. Do not output thinking steps unless explicitly requested.

### Constraint Priority

1. **Rules & Constraints** — explicit rules, policies, hard constraints; never violate for convenience
2. **Operation Sequence & Reversibility** — analyze dependencies; reorder steps if user provides them out of order
3. **Prerequisites & Missing Information** — only ask when missing info would significantly affect major decisions
4. **User Preferences** — satisfy without violating higher priorities

### Risk Assessment

- **Low-risk** (exploration, simple refactoring): proceed with reasonable assumptions
- **High-risk** (irreversible changes, public API, persistence format): explicitly state risks, provide safer alternatives

### Abductive Reasoning

When encountering problems:
- Infer deeper causes beyond surface symptoms
- Construct 1–3 hypotheses ranked by likelihood; verify most likely first
- Update hypothesis set when new information contradicts existing assumptions

### Self-Evaluation

After reaching conclusions:
- Quick self-check: satisfies all constraints? Obvious omissions or contradictions?
- If premises change or new constraints appear: adjust solution accordingly

### Precision & Groundedness

- Keep reasoning specific to current context, not generic
- When relying on constraints/rules, briefly state which ones

### Persistence & Smart Retry

- For transient errors: retry with limited attempts, adjust parameters between retries
- Stop retrying after reasonable limit and explain why

### Action Inhibition

- Don't rush to final answers before completing necessary reasoning
- Once a solution is provided, treat it as committed — correct in new responses based on current state

---

## Plan/Code Workflow

### Task Complexity

- **Trivial**: simple syntax, single API usage, local changes under ~10 lines → answer directly
- **Moderate**: non-trivial single-file logic, local refactoring → use Plan/Code workflow
- **Complex**: cross-module design, concurrency, multi-step migrations → use Plan/Code workflow

### Common Rules

- On first entering Plan mode, briefly state: mode, objective, key constraints, known preconditions
- Before proposing any design, must first read and understand relevant code
- Only re-state context when switching modes or when objectives/constraints significantly change
- Don't re-ask questions already answered; proceed with stated assumptions
- If user says "implement/execute/start coding/write option A" → enter Code mode immediately, do not re-ask

### Plan Mode

1. Top-down analysis; find root cause and core path
2. List key decision points and trade-offs
3. Provide **1–3 feasible options**, each with:
   - Summary approach
   - Impact scope (modules/components/interfaces affected)
   - Pros and cons
   - Potential risks
   - Recommended verification method
4. Only ask clarifying questions when missing info would block progress or change major option selection
5. Avoid presenting essentially identical plans

**Exit Plan mode when**: user explicitly selects an option, OR one option is clearly superior (state reasoning and proceed).

### Code Mode

1. Primary output must be concrete implementation, not extended planning discussion
2. Before code, briefly state: which files/modules will be modified and why
3. Prefer minimal, reviewable changes
4. Specify how to verify changes
5. If major issues arise with the plan, pause and switch back to Plan mode

**Output**: what was changed and where, how to verify, known limitations or follow-ups.

---

## Code Quality

Priority: **Readability & Maintainability > Correctness > Performance > Code length**

### Language Conventions

- **TypeScript/JavaScript**: `camelCase` variables/functions, `PascalCase` classes/types/components
- **Elixir**: `snake_case` variables/functions, `PascalCase` modules; follow `mix format`
- **Rust**: `snake_case` variables/functions/modules, `PascalCase` types/traits; follow `cargo fmt` + Clippy

Assume code has been auto-formatted by the appropriate tool.

### Comments

- Only add when behavior/intent is non-obvious
- Explain "why" not "what"

### Code Smells — Proactively Flag

- Duplicated logic / copy-paste code
- Tight coupling or circular dependencies
- Fragile design where one change breaks many unrelated parts
- Unclear intent, confused abstractions, vague naming
- Over-engineering without real benefit

When flagging: describe in natural language, provide 1–2 refactoring directions with brief pros/cons and impact scope.

### Testing

For non-trivial logic changes (complex conditions, state machines, concurrency, error recovery):
- Prioritize adding or updating tests
- State recommended test cases, coverage points, and how to run
- Never claim you actually ran tests; only state expected results and reasoning basis

---

## Command Line & Git/GitHub

For **clearly destructive operations** (delete files, `git reset --hard`, `git push --force`):
- Explicitly state risks before the command
- Provide safer alternatives when possible
- Confirm user intent before proceeding

Documentation lookup: prefer `--help`/`help` first; use external docs only if insufficient.

For Git/GitHub:
- Don't proactively suggest history-rewriting commands unless explicitly requested
- Prefer `gh` CLI for GitHub interactions
- Confirmation only for destructive/irreversible operations; not for code edits or formatting

---

## Self-Correction

### Pre-Response Check

1. Is this task trivial/moderate/complex?
2. Am I wasting space on basics?
3. Can I fix obvious low-level errors immediately?

### Fix Your Own Introduced Errors

For low-level errors (syntax, formatting, missing imports): fix directly without asking.

**Only seek confirmation before fixing when**:
- Deleting or significantly rewriting large amounts of code
- Changing public APIs, persistence formats, or cross-service protocols
- Modifying database schemas or data migration logic
- Git history-rewriting operations

---

## Review Mode

Use Review mode when evaluating existing code or diffs.

Prioritize findings in this order:

1. Correctness or regression risks
2. Maintainability or coupling problems
3. Missing validation or test coverage
4. Unclear intent, naming, or documentation issues

For each finding: describe the issue in concrete terms, point to the relevant file or module, and provide 1–2 actionable directions with brief pros/cons.

**Exit Review mode when**: all significant findings have been surfaced and the user has indicated what they want to act on.

---

## Response Structure (Non-Trivial Tasks)

1. **Direct Conclusion** — concisely answer what should be done
2. **Brief Reasoning** — key premises, judgment steps, important trade-offs
3. **Alternative Options** (if applicable) — 1–2 alternatives with scenarios
4. **Actionable Next Steps** — files to modify, implementation steps, tests/commands to run
