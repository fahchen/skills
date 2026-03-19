# BDD Discovery

A unified workflow that interleaves feature discovery and behavior decisions. Run once per feature, or revisit earlier discoveries to refine rules and add examples. A single session can cover multiple features sequentially. Complete one feature before starting the next. Each feature uses its own temporary progress file as working memory. Once discovery consolidates into final outputs (`.feature` files and/or BDR files), remove the progress file.

## File Structure

Organize BDD artifacts within a top-level `spec/` directory, or follow the project's established convention:

```text
spec/
├── glossary.md
├── .discovery/
│   ├── order-cancellation.md
│   └── loyalty-rewards.md
├── orders/
│   ├── features/
│   │   └── cancellation.feature
│   └── decisions/
│       ├── BDR-0001-no-cancellation-after-dispatch.md
│       └── BDR-0002-partial-cancellation-not-supported.md
└── checkout/
    ├── features/
    └── decisions/
```

Conventions:

- Group by domain
- One feature per file
- Progress files live in `spec/.discovery/`
- Glossary lives at `spec/glossary.md`

## Progress File

Create a progress file at `spec/.discovery/<domain>-<feature>.md`. Update it incrementally as the conversation progresses.

### Suggested Structure

```markdown
# Discovery: [Feature Name]

## Story
[One-line summary]

## Actor
[Who benefits]

## Value
[Why it matters]

## Rules Discovered
- [ ] Rule 1: ...

## Open Questions
- [ ] Question about ...

## Decisions Made
- **[Decision title]**: Chose A over B because ...

## Out-of-Scope Behaviours
- ...

## Rejected Behaviours
- ...

## Glossary Candidates
- **[Term]**: ...
```

Check rules off when the rule is confirmed and at least one example is agreed. Check open questions off when resolved and annotate the resolution.

## Discovery Phase

### Kickoff

1. Check `spec/.discovery/` for leftover progress files.
2. Create or resume the progress file.
3. Restate the idea in one sentence.
4. Identify the actor if the user did not name one.

### Iterative Questioning

Work in small batches. Ask 2-3 questions per turn. After each answer:

- update the progress file
- drop questions that are now irrelevant
- add follow-ups based on what the user just revealed

Elicit:

- rules
- examples
- decisions
- open questions
- rejected behaviors
- domain terms

### Layer Check

When a proposed behavior sounds like an implementation detail instead of a business rule, reframe it to the underlying business rule or mark it as out of scope.

### Glossary Maintenance

Read `spec/glossary.md` if it exists. Watch for:

- new terms
- conflicting definitions
- duplicate concepts under different names

Do not silently edit the glossary. Propose changes first.

### Conflict Detection

Throughout discovery, scan existing `.feature` files and BDRs for conflicts with newly discovered rules and decisions.

When a conflict appears, present options such as:

1. Keep existing, adjust new
2. Supersede existing
3. Scope separation

Record the resolution in the progress file.

### Checkpoint

When the topic groups stabilize:

- summarize the progress file
- confirm understanding
- resolve or explicitly defer any open questions blocking consolidation

### Pausing

If the user pauses before consolidation:

1. update the progress file
2. summarize current status
3. keep the progress file

## Consolidation Phase

Enter consolidation only when every rule has examples, conflicts are handled, and open questions are resolved or explicitly deferred.

### Generate `.feature` Files

Transform discovered content into Gherkin:

- story -> feature narrative
- each rule -> `Rule:`
- each example -> `Scenario:` or `Scenario Outline:`
- unresolved items -> `# TODO:` or `@wip`

Exclude implementation-detail scenarios.

Place generated files in `spec/<domain>/features/`.

Overwrite existing `.feature` files for the same capability; they represent the latest version.

### Generate BDRs

Create BDRs for non-trivial decisions. Skip BDR creation if no non-trivial decisions were made.

### Update Glossary

Propose glossary additions or updates discovered during the session.

### Cleanup

Delete the progress file when discovery is fully consolidated.
