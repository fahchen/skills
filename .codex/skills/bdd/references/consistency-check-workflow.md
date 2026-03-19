# BDD Consistency Check

Detect and resolve contradictions across BDD spec artifacts: `.feature` files, BDR files, and glossary entries.

This workflow supports two modes:

- **Incremental**: check recent changes against the existing spec
- **Full**: scan the entire `spec/` tree for internal contradictions

If the user says "check my changes" or similar, default to incremental.

## Findings Ledger

Create a temporary findings file at `spec/.consistency-check.md` and update it incrementally while scanning.

### Suggested Structure

```markdown
# Consistency Check - [mode: incremental | full]

## Points Observed
- [P1] file:line - statement

## Conflicts Detected
### C1: [short title]
- **Points**: P1 vs P5
- **Nature**: contradictory rules | overlapping scenarios | decision reversal | glossary inconsistency | stale reference
- **Detail**: ...
- **Severity**: breaking | misleading | cosmetic

## Resolutions
### C1: [short title]
- **Decision**: ...
- **Action**: ...
```

## Incremental Mode

### 1. Identify Changed Files

Use user-specified files if provided. Otherwise use `git diff --name-only` and filter to `spec/` paths:

- `.feature`
- BDR `.md` files in `decisions/`
- `glossary.md`

If no spec files changed, tell the user and stop.

### 2. Extract Points from Changes

Record business-relevant statements as points:

- feature rules
- scenario outcomes
- BDR decisions and status
- glossary term definitions

### 3. Scan Existing Specs for Conflicts

Compare changed points against the rest of the spec. Look for:

- contradictory rules
- overlapping scenarios with different outcomes
- decision reversals
- glossary inconsistency
- stale references

Record conflicts as soon as they are found.

### 4. Present Conflicts

For each conflict:

1. show the conflicting points
2. identify which side is changed
3. propose a concrete resolution that adjusts the existing side
4. note cascade effects if any

Ask the user to confirm, adjust, or reject the proposed resolutions.

### 5. Apply Resolutions

For confirmed resolutions:

- update existing spec files
- if a BDR changes, create a superseding BDR instead of rewriting the old body
- update glossary as needed

### 6. Cleanup

Remove `spec/.consistency-check.md` and summarize changes.

## Full Mode

### 1. Scan All Spec Files

Process in a stable order:

1. `glossary.md`
2. `.feature` files grouped by domain
3. BDRs grouped by domain

### 2. Extract and Compare Points

After recording each point, compare it with all previously recorded points.

### 3. Multi-Pass Review

After the initial pass:

- review patterns among detected conflicts
- do a focused second pass near conflicting files or terms
- stop when no new conflicts appear

### 4. Present Conflicts

Group by severity:

1. breaking
2. misleading
3. cosmetic

For each conflict, present 2-3 options such as:

1. Keep A, adjust B
2. Keep B, adjust A
3. Reconcile both sides

### 5. Apply Resolutions

Same rules as incremental mode. Respect BDR immutability.

### 6. Verification Pass

Rescan modified files against the full spec to ensure no new conflicts were introduced.

### 7. Cleanup

Remove `spec/.consistency-check.md` and summarize changes.

## Conflict Comparison Technique

When comparing two points:

1. Are they about the same subject?
2. Can both be true at once?
3. Do they apply in overlapping scope?
4. Is one superseded historically?

Only flag a conflict when both points make incompatible claims about the same subject in overlapping active scope.

## Anti-Patterns

- surface-level matching
- phantom conflicts across separate scopes
- ignoring BDR supersession
- silent fixes without user confirmation
- cascade blindness
