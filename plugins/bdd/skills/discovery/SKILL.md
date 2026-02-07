---
name: discovery
description: This skill should be used when the user asks to "discover a feature", "explore behaviour", "write a feature file", "BDD discovery", "formulate BDD specs", "example mapping", "start a discovery session", "resume a discovery session", "explore a user story", or wants to interactively explore and define system behaviour using BDD patterns.
user-invocable: true
---

# BDD Discovery

A unified skill that interleaves feature discovery and behaviour decisions. Run once per feature, or revisit earlier discoveries to refine rules and add examples. A single session can cover multiple features sequentially — complete one feature's discovery and consolidation before starting the next. Each feature uses its own temporary progress file as working memory. Once discovery consolidates into final outputs (.feature files and/or BDR files), remove the progress file.

## File Structure

Organize BDD artifacts within a top-level `spec/` directory (or the project's established convention):

```
spec/
├── glossary.md                            # shared domain terminology
├── .discovery/
│   ├── order-cancellation.md              # temporary — removed after consolidation
│   └── loyalty-rewards.md                 # multiple discoveries can coexist
├── features/
│   ├── orders/
│   │   └── order-cancellation.feature
│   ├── authentication/
│   │   └── login.feature
│   └── checkout/
│       └── shopping-cart.feature
└── decisions/
    ├── BDR-0001-no-cancellation-after-dispatch.md
    └── BDR-0002-partial-cancellation-not-supported.md
```

Conventions:

- **Group .feature files by domain** — e.g., `spec/features/checkout/`, `spec/features/authentication/`. Avoid a flat list at the top level.
- **One feature per file** — name the file after the capability (`shopping-cart.feature`, not `test-1.feature`).
- **Centralize BDRs in `spec/decisions/`** — keeps decision records discoverable regardless of which feature they relate to. The `**Feature**` field in the BDR body links back.
- **Progress files live in `spec/.discovery/`** — named per feature: `<feature-slug>.md` (e.g., `order-cancellation.md`). Hidden directory to avoid clutter. Multiple discoveries can coexist.
- **Glossary at `spec/glossary.md`** — shared domain terminology (ubiquitous language) maintained across all discoveries. Table format with Term and Definition columns.

Adapt to an existing project layout when one is already established. These conventions apply when starting fresh.

## Progress File

Create a progress file at the start of discovery. Place it in `spec/.discovery/<feature-slug>.md` (e.g., `spec/.discovery/order-cancellation.md`). If resuming a previous discovery, read the existing progress file and continue from where it left off. This file is working memory — update it incrementally as the conversation progresses.

### Structure

```markdown
# Discovery: [Feature Name]

## Story
[One-line summary of what we're exploring]

## Actor
[Who benefits]

## Rules Discovered
- [ ] Rule 1: ...
  - Example: ...
  - Example: ...
- [ ] Rule 2: ...

## Open Questions
- [ ] Question about ...
- [ ] Clarify whether ...

## Decisions Made
- **[Decision title]**: Chose A over B because ... (-> BDR candidate)
- **[Decision title]**: Deferred X because ...

## Rejected Behaviours
- Behaviour X: rejected because [reason]
```

Check items off as they get resolved. Add new sections or entries as rules, examples, and decisions emerge.

## Discovery Phase

Adapt depth based on the quality of input. Rough ideas warrant more exploratory questions; detailed input warrants confirmation and gap-filling. Discovery is iterative — small steps, not a single pass.

### Kickoff

1. **Check for existing discoveries** — Scan `spec/.discovery/` for leftover progress files. If any are found, read them and present the user with options:
   - **Resume** — Continue the existing discovery where it left off
   - **Discard** — Delete the leftover file and start fresh
   - **Set aside** — Leave it alone and start a new, separate discovery
   - **Merge** — Incorporate the existing progress into the new discovery (specify whether existing content goes before or after the new idea)
2. **Create the progress file** — Initialize with the user's idea, even if sparse. Commit what is known; leave unknowns as open questions.
3. **Restate the idea** — Parse the input and summarize the core capability in one sentence. Confirm alignment before going deeper.
4. **Identify the actor** — Determine who benefits. Ask directly if the input does not name a role. Record in the progress file.

### Iterative Questioning

Do not dump all questions at once. Work in small batches, grouped by topic.

**Grouping**: Organize questions thematically (e.g., preconditions, boundaries, error cases, permissions). Tackle one group at a time.

**Batching**: Within a group, ask 2–3 questions per turn. If the group has more questions, hold the rest for the next turn.

**Reacting**: After each user response, update the progress file, then formulate the *next* questions based on what the user just said — not by mechanically repeating remaining questions from a pre-planned list. The user's answers will reveal new angles, shift priorities, and close off entire lines of questioning.

**Loop**: Repeat this cycle until the current topic group stabilizes, then move to the next group. Continue looping across groups until discovery feels complete.

```
┌─→ Ask (2–3 questions from current topic group)
│     ↓
│   Listen (read user's answers)
│     ↓
│   Update (record rules, examples, decisions, questions in progress file)
│     ↓
│   Adapt:
│     - Formulate follow-ups based on what the user just revealed
│     - Drop questions now answered or irrelevant
│     - Surface new questions the answers raised
│     - If topic group stabilizes → switch to next group
└─── Loop back to Ask
```

**What to elicit** (not a sequential checklist — weave these in as the conversation naturally reaches them):

- **Rules** — Business rules, constraints, boundaries, preconditions, invariants
- **Examples** — Concrete instances of each rule: happy path, boundary, failure
- **Decisions** — When the user chooses between competing behaviours, capture immediately with reasoning. Flag non-trivial decisions as BDR candidates.
- **Open questions** — Anything needing stakeholder input or further research. Do not silently assume answers.
- **Rejected behaviours** — What was explicitly ruled out and why
- **Domain terms** — New or ambiguous terminology that surfaces during conversation. Flag candidates for the glossary.

### Glossary Maintenance

During discovery, watch for domain terms that are new, ambiguous, or used inconsistently. Read `spec/glossary.md` (if it exists) and check for:

- **New terms** — Domain concepts not yet in the glossary
- **Conflicting definitions** — A term used differently than its glossary definition
- **Term merges** — Two terms that refer to the same concept

Do not silently add or modify glossary entries. Present proposed changes to the user for confirmation before applying. The glossary uses a table format:

```markdown
| Term | Definition |
|------|------------|
| Churned subscriber | A customer who cancelled their subscription within the last 30 days |
| Member | A customer with an active subscription |
```

If no glossary exists yet, propose creating `spec/glossary.md` with the terms discovered so far.

### Conflict Detection

Throughout the iterative loop — not just at the end — scan existing `.feature` files and BDRs in `spec/` for conflicts with the rules and decisions being formulated. Check as new rules and decisions emerge, not as a single pass after discovery. Look for:

- **Contradictory rules** — A new rule that directly contradicts a rule in an existing feature
- **Overlapping scenarios** — Scenarios that describe the same behaviour with different expected outcomes
- **Decision reversals** — A new decision that contradicts an accepted BDR

When a conflict is found, do not simply report the problem. Present 2–3 resolution options to help the user think through the trade-off:

1. **Keep existing, adjust new** — Modify the current discovery to align with what is already established
2. **Supersede existing** — Proceed with the new behaviour; the existing feature/BDR will be updated during consolidation
3. **Scope separation** — The two behaviours apply to different contexts; clarify the boundary so both can coexist

Record the resolution in the progress file under Decisions Made.

### Checkpoint

When the current topic groups feel stable, present a summary of the progress file. Confirm understanding. Resolve any remaining open questions that block consolidation — or explicitly mark them as deferred.

## Consolidation Phase

Enter consolidation when the Quality Criteria checklist below is satisfied: rules are stable with concrete examples, open questions are resolved or explicitly deferred, and conflicts are addressed.

### 1. Generate .feature File(s)

Transform discovered content into well-formed Gherkin:

- Story becomes the Feature narrative (As a / I want / So that)
- Each discovered rule becomes a `Rule:` keyword block
- Each example becomes a `Scenario:` or `Scenario Outline:` under its rule
- Unresolved open questions become `# TODO:` comments or `@wip` tags
- Group related scenarios under the same Rule block; split into separate .feature files only when features are genuinely distinct

Place generated files in `spec/features/` (or the established convention).

**Feature files keep only the latest version.** If a `.feature` file already exists for this feature, overwrite it with the new content. There is no versioning for feature files — they represent the current specification.

### 2. Generate BDR File(s)

Create Behaviour Decision Records only for significant decisions — especially rejections and contested trade-offs where "why not" provides lasting value.

- Use the standard format for decisions with meaningful context and alternatives
- Use the lightweight format for minor decisions where a one-liner suffices
- Place BDR files in `spec/decisions/` (or the established convention)

Skip BDR generation entirely if no non-trivial decisions were made during discovery.

**BDR body content is immutable** — never modify the decision rationale or reasoning of an existing BDR. Frontmatter metadata (`status`, `superseded-by`) may be updated for lifecycle tracking. When a decision changes:

1. Create a new BDR with the updated decision, including `**Supersedes**: BDR-XXXX` in its body alongside the `**Feature**` and `**Rule**` fields (or inside `## Scope` for lightweight format)
2. In the old BDR, update frontmatter: set `status: superseded` and add `superseded-by: BDR-YYYY` pointing to the new BDR
3. Do not change the old BDR's body or any other frontmatter fields

This creates a bidirectional link — the old BDR points forward, the new BDR points back — and preserves the full decision history.

### 3. Post-Consolidation Conflict Check

After generating all files, scan the full `spec/` directory for:

- Rules in other `.feature` files that now contradict the newly generated content
- BDRs that reference rules or features affected by this discovery

If conflicts are found, present them to the user with resolution options (same approach as the discovery-phase conflict detection). Do not silently overwrite or ignore.

### 4. Update the Glossary

Review all domain terms that surfaced during discovery. Compare against `spec/glossary.md` and propose changes to the user:

- **Add** new terms not yet in the glossary
- **Update** definitions that evolved during discovery
- **Merge** terms discovered to be synonyms (keep one, note the alias)

Present all proposed changes as a batch for the user to confirm before applying.

When a glossary term is updated or merged, scan all existing `.feature` files for usages of the old term. Propose updates to affected scenarios to maintain consistency. Check whether the term change affects the intent of any rules — if so, flag the affected features for the user to review before modifying.

### 5. Remove the Progress File

Delete the progress file (e.g., `spec/.discovery/order-cancellation.md`). If no other discoveries are in progress, remove the `spec/.discovery/` directory as well. All discovery state is now captured in the generated .feature and BDR files. The progress file has served its purpose.

## Feature File Anatomy

A well-formed `.feature` file follows this structure. Use this as a reference when generating output.

```gherkin
@checkout
Feature: Shopping Cart Checkout
  As a customer
  I want to complete my purchase
  So that I receive the products I selected

  Background:
    Given a customer with items in their cart

  Rule: Authenticated customers can checkout

    Scenario: Logged-in customer checks out
      Given the customer is logged in
      When the customer proceeds to checkout
      Then the order should be created

    Scenario: Guest is prompted to log in
      Given the customer is not logged in
      When the customer proceeds to checkout
      Then a login prompt should appear

  Rule: Order total reflects applicable discounts

    Scenario Outline: Discount tiers
      Given a cart total of <subtotal>
      When the customer applies discount code "<code>"
      Then the total should be <final>

      Examples:
        | subtotal | code    | final  |
        | $100     | SAVE10  | $90    |
        | $200     | SAVE20  | $160   |
```

Key conventions:

- **Tags** (`@checkout`) -- Categorize and filter scenarios. Apply at Feature or Scenario level.
- **Feature narrative** -- Always name the actor and the value delivered.
- **Background** -- Shared preconditions for all scenarios in the feature (or within a Rule block).
- **Rule:** -- Groups scenarios that illustrate a single business rule. Prefer Rule blocks over flat scenario lists.
- **Scenario** -- A concrete example of the rule. One behaviour per scenario.
- **Scenario Outline + Examples** -- Parameterized scenarios for data-driven rules. Use when multiple inputs exercise the same logic path.
- **Given/When/Then** -- Declarative language describing state, action, and outcome. Avoid implementation details (no CSS selectors, API endpoints, or UI element names).

## BDR (Behaviour Decision Record) Format

BDRs capture the reasoning behind behaviour decisions. Frontmatter holds scannable metadata; the body holds the reasoning.

### Standard Format

Use for decisions with meaningful context, multiple alternatives considered, or non-obvious trade-offs.

```markdown
---
id: BDR-NNNN
title: [Decision Title]
status: accepted | rejected | deferred | superseded
date: YYYY-MM-DD
summary: [One-line summary capturing the essence of the decision]
---

**Feature**: [path relative to spec/features/ directory]
**Rule**: [which Rule: this relates to]
**Supersedes**: BDR-XXXX  <!-- optional, only when overriding a previous decision -->

## Context
[What behaviour was being discussed?]

## Behaviours Considered
### Option A: ...
### Option B: ...

## Decision
[What was chosen and why]

## Rejected Alternatives
[Why not -- the most valuable part]
```

### Lightweight Format

Use for minor decisions. Frontmatter plus a brief body — no full Context/Alternatives structure needed, but always include a reason.

```markdown
---
id: BDR-NNNN
title: Allow guest checkout without account
status: rejected
date: YYYY-MM-DD
summary: Rejected guest checkout; authentication required for payment security
---

## Scope

**Feature**: checkout/shopping-cart.feature
**Rule**: Customers must be authenticated to checkout
**Supersedes**: BDR-XXXX  <!-- optional -->

## Reason

Security requirements mandate authenticated sessions for payment processing.
Guest checkout would bypass identity verification, which conflicts with PCI
compliance obligations.
```

### When to Write a BDR

- A behaviour was explicitly rejected and the reasoning matters for future reference
- Two or more viable options were debated and one was chosen with trade-offs
- A decision was deferred and the context needs to be preserved
- A previous decision is being superseded

Do not create BDRs for obvious or uncontested decisions.

### BDR Numbering

Determine the next BDR number by scanning `spec/decisions/` for the highest existing `BDR-NNNN` and incrementing. If no BDRs exist yet, start at `BDR-0001`.

## Quality Criteria

Apply this checklist before consolidation:

- [ ] Each rule has at least one concrete example
- [ ] Scenarios use declarative language (what, not how)
- [ ] No implementation details leak into step wording
- [ ] Open questions are explicitly flagged, not silently assumed
- [ ] Feature narrative names the actor and the value
- [ ] Rejected behaviours have recorded reasoning
- [ ] BDR candidates identified for non-trivial decisions
- [ ] No conflicts with existing .feature files and BDRs (or conflicts resolved)
- [ ] Superseded BDRs have status updated; no existing BDR content was modified
- [ ] Glossary updated with new/changed terms (confirmed with user)
- [ ] Terms in .feature files consistent with glossary definitions
- [ ] Progress file will be removed after consolidation

## Reference

- **`references/example-walkthrough.md`** — Complete worked example (order cancellation) showing a filled-in progress file, the consolidated .feature file, standard and lightweight BDRs, and the final file tree. Consult when unsure how a discovery session maps to output files.

## Common Pitfalls

- **Premature consolidation** -- Jumping to .feature files before discovery is thorough. Stay in discovery until rules stabilize.
- **Imperative scenarios** -- Writing scenarios that describe UI clicks ("When the user clicks the Submit button") instead of behaviour ("When the user submits the form"). Focus on what happens, not how.
- **Conflated rules** -- Cramming multiple business rules into a single scenario. Each scenario should illustrate one rule.
- **Missing rejections** -- Forgetting to capture "why not" for rejected options. The reasoning behind what was excluded is often more valuable than what was included.
- **Orphaned progress file** -- Leaving progress files in `spec/.discovery/` behind after consolidation. Always clean up.
- **Over-specifying** -- Turning every edge case into a scenario instead of noting it as an open question or deferring it. Not every edge case warrants a scenario in the first pass.
- **Assumed answers** -- Silently deciding an open question instead of flagging it. When uncertain, ask or mark it explicitly.
