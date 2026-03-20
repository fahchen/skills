---
name: consistency-check
description: This skill should be used when the user asks to "check consistency", "find conflicts", "consistency check", "conflict detection", "incremental check", "full check", "check my specs", "validate BDD specs", "check for contradictions", or wants to detect and resolve conflicts between BDD spec files (.feature files, BDRs, and glossary).
user-invocable: true
---

# BDD Consistency Check

Detect and resolve contradictions across BDD spec artifacts — `.feature` files, BDR files, and glossary. Two modes:

- **Incremental** — Check whether *recent changes* (staged/unstaged git diff, or user-specified files) conflict with the rest of the spec. Changes take priority; conflicting existing specs are adjusted.
- **Full** — Scan the entire `spec/` directory for internal contradictions. No side has priority; all conflicts are presented for the user to resolve.

Ask the user which mode to use if not specified. If the user says "check my changes" or similar, default to incremental.

## Findings Ledger

Create a temporary findings file at `spec/.consistency-check.md` to record observations as they are discovered. This file is working memory — write to it incrementally during the check. Remove it after the check completes and all resolutions are applied.

### Structure

```markdown
# Consistency Check — [mode: incremental | full]
<!-- Started: YYYY-MM-DD -->
<!-- Scope: [files or "full spec/"] -->

## Points Observed

<!-- Each point is a factual statement extracted from a spec artifact. -->
<!-- Format: [ID] source_file:line — statement -->

- [P1] orders/features/cancellation.feature:12 — Orders can only be cancelled before dispatch
- [P2] shipping/features/dispatch.feature:8 — Dispatch status is set when carrier confirms pickup
- [P3] orders/decisions/BDR-0001.md — No cancellation after dispatch (accepted)

## Conflicts Detected

<!-- Each conflict references the points that contradict each other. -->

### C1: [short title]
- **Points**: P1 vs P5
- **Nature**: [contradictory rules | overlapping scenarios | decision reversal | glossary inconsistency | stale reference]
- **Detail**: [explain the contradiction]
- **Severity**: [breaking | misleading | cosmetic]
- **Source priority** (incremental only): [changed | existing]

## Resolutions

### C1: [short title]
- **Decision**: [what was decided]
- **Action**: [files modified and how]
```

## Incremental Mode

### 1. Identify Changed Files

Determine the scope of changes:

- If the user specifies files, use those
- Otherwise, use `git diff --name-only` (staged + unstaged) filtered to `spec/` paths
- Include only `.feature`, BDR (`.md` in `decisions/`), and `glossary.md` files

If no spec files have changed, inform the user and exit.

### 2. Extract Points from Changes

Read each changed file. For every business-relevant statement, record a point in the findings ledger:

- **Feature files**: Each `Rule:` statement, each `Scenario:` title + its Then outcomes, Feature narrative
- **BDRs**: The decision, status, supersession links, scope (Feature/Rule references)
- **Glossary**: Each term and its definition

Label each point with a `[P<n>]` identifier, the source file and line, and a concise statement.

### 3. Scan Existing Specs for Conflicts

Read all *other* spec files (not in the change set). For each, extract points using the same method. As each new point is recorded, compare it against all previously recorded points. Look for:

- **Contradictory rules** — Two rules that cannot both be true (e.g., "orders can be cancelled anytime" vs "orders cannot be cancelled after dispatch")
- **Overlapping scenarios** — Two scenarios describing the same trigger with different outcomes
- **Decision reversals** — A changed decision that contradicts an existing accepted BDR (without supersession)
- **Glossary inconsistency** — A term used in a feature file differently than its glossary definition, or a changed glossary definition that invalidates existing scenario wording
- **Stale references** — A BDR referencing a rule or feature that no longer exists or has changed meaning

When a conflict is found, record it immediately in the findings ledger under **Conflicts Detected**. Do not wait until all files are scanned.

### 4. Present Conflicts

After scanning completes, extract all conflicts from the ledger. For each conflict:

1. Show the conflicting points with their source files
2. State which side is the "changed" side (takes priority in incremental mode)
3. Propose a concrete resolution that adjusts the *existing* side to align with the change
4. If the resolution would cascade (e.g., changing a rule affects other scenarios), note the cascade

Present all conflicts as a batch. Ask the user to confirm, adjust, or reject each resolution.

### 5. Apply Resolutions

For confirmed resolutions:

- Modify the existing spec files to align with the changes
- If a BDR requires a **conflicting change** (decision reversal or contradictory reasoning), create a new superseding BDR rather than editing the existing body — never overwrite a BDR body when the change introduces a contradiction
- If a BDR only needs **completion or supplementation** (adding missing detail or expanding context without changing the decision conclusion), the body may be edited directly
- Update glossary if term definitions changed
- Record each resolution in the findings ledger

### 6. Cleanup

Remove `spec/.consistency-check.md`. Summarize what was changed.

## Full Mode

### 1. Scan All Spec Files

Read every `.feature` file, BDR, and `glossary.md` under `spec/`. Process them in a consistent order:

1. `glossary.md` first (establishes term definitions)
2. `.feature` files grouped by domain
3. BDRs grouped by domain

### 2. Extract and Compare Points

For each file, extract points and record them in the findings ledger. After recording each point, compare it against *all previously recorded points*. This incremental comparison is critical — it catches conflicts as they emerge rather than requiring a combinatorial comparison at the end.

For each pair of points being compared, check:

- Do they make contradictory claims about the same concept?
- Do they describe the same scenario with different outcomes?
- Does one reference something that the other has changed or removed?
- Do they use the same term with different meanings?

### 3. Multi-Pass Scanning

A single pass may miss conflicts that only become apparent after examining later files. After the initial pass:

- Review all detected conflicts to see if they reveal patterns (e.g., a term used inconsistently across 3+ files)
- Do a targeted second pass on files adjacent to conflicts (same domain, shared terms)
- Stop when a pass reveals no new conflicts

### 4. Present Conflicts

Group conflicts by severity:

1. **Breaking** — Contradictory rules or decisions that cannot coexist. The spec is internally inconsistent.
2. **Misleading** — Stale references, outdated wording, or scenarios that imply something no longer true. Not broken but confusing.
3. **Cosmetic** — Inconsistent naming, style differences, minor glossary drift. Low priority.

For each conflict, present:

- The conflicting points with source files
- The nature of the contradiction
- 2-3 resolution options (no side has automatic priority in full mode):
  1. **Keep A, adjust B** — with concrete changes
  2. **Keep B, adjust A** — with concrete changes
  3. **Reconcile** — both sides need adjustment to a new shared position

Ask the user to resolve each conflict (or group related conflicts for batch resolution).

### 5. Apply Resolutions

Same as incremental mode step 5. Follow BDR body editing rules — edit directly when supplementing, supersede when the decision itself conflicts. Track cascading changes.

### 6. Verification Pass

After all resolutions are applied, do one final scan of all modified files against the full spec to confirm no new conflicts were introduced by the resolutions.

### 7. Cleanup

Remove `spec/.consistency-check.md`. Summarize all changes made.

## Conflict Comparison Technique

When comparing two points, use this mental framework:

1. **Same subject?** — Do both points talk about the same entity, action, or concept? If not, no conflict.
2. **Compatible claims?** — Can both statements be true simultaneously? If yes, no conflict.
3. **Scope overlap?** — Do they apply to the same context/conditions? If they apply to different contexts, they may coexist (scope separation).
4. **Temporal validity?** — Is one point from a superseded BDR? If so, only the latest decision counts.

Only flag a conflict when two points make incompatible claims about the same subject in overlapping scope, and both are currently active (not superseded).

## What to Extract as Points

### From .feature Files

| Artifact | Extract as point |
|----------|-----------------|
| `Feature:` title + narrative | The capability and who it serves |
| `Rule:` statement | The business rule claim |
| `Scenario:` title | The specific behaviour being illustrated |
| `Then` steps | The expected outcomes / assertions |
| `Background:` steps | Assumed preconditions for all scenarios in scope |
| `@wip` scenarios | Flag as provisional — lower confidence in conflict detection |
| Tags | Categorization claims (e.g., `@orders` means this belongs to orders domain) |

### From BDRs

| Artifact | Extract as point |
|----------|-----------------|
| `status` in frontmatter | Whether this decision is active |
| `Decision` section | What was decided |
| `Rejected Alternatives` | What was explicitly ruled out |
| `Feature` / `Rule` references | Which specs this decision governs |
| `superseded-by` / `Supersedes` | Decision lineage |

### From Glossary

| Artifact | Extract as point |
|----------|-----------------|
| Each term + definition | The authoritative meaning of this domain concept |

## Anti-Patterns

- **Surface-level matching** — Don't flag conflicts just because two scenarios mention the same entity. Read the actual claims being made.
- **Phantom conflicts** — Don't flag scope-separated rules as conflicts. "Premium users get free shipping" and "Standard users pay shipping" are not contradictory.
- **Ignoring supersession** — Always check BDR status before flagging a decision conflict. Superseded BDRs are historical records, not active decisions.
- **Silent fixes** — Never modify spec files without presenting the conflict and getting user confirmation first.
- **Cascade blindness** — When resolving a conflict, trace the impact. Changing a rule may invalidate scenarios in other features.
