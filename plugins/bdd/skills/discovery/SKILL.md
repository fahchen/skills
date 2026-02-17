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
├── orders/
│   ├── features/
│   │   └── cancellation.feature
│   └── decisions/
│       ├── BDR-0001-no-cancellation-after-dispatch.md
│       └── BDR-0002-partial-cancellation-not-supported.md
├── authentication/
│   ├── features/
│   │   └── login.feature
│   └── decisions/
│       └── BDR-0003-require-mfa-for-password-reset.md
└── checkout/
    ├── features/
    │   └── cart.feature
    └── decisions/
        └── BDR-0004-no-guest-checkout.md
```

Conventions:

- **Group by domain** — Each domain gets a top-level directory under `spec/` (e.g., `spec/orders/`, `spec/checkout/`). Features and decisions for that domain live together: `spec/<domain>/features/` and `spec/<domain>/decisions/`.
- **One feature per file** — name the file after the capability (`cancellation.feature`, not `test-1.feature`). The domain directory provides context, so don't repeat it in the filename.
- **Progress files live in `spec/.discovery/`** — named `<domain>-<feature>.md` (e.g., `order-cancellation.md`). Include the domain in the filename since `.discovery/` is flat. Hidden directory to avoid clutter. Multiple discoveries can coexist.
- **Glossary at `spec/glossary.md`** — shared domain terminology (ubiquitous language) maintained across all discoveries. Table format with Term and Definition columns.

Adapt to an existing project layout when one is already established. These conventions apply when starting fresh.

## Progress File

Create a progress file at the start of discovery. Place it in `spec/.discovery/<domain>-<feature>.md` (e.g., `spec/.discovery/order-cancellation.md`). If resuming a previous discovery, read the existing progress file and continue from where it left off. This file is working memory — update it incrementally as the conversation progresses.

### Structure

```markdown
# Discovery: [Feature Name]

## Story
[One-line summary of what we're exploring]

## Actor
[Who benefits]

## Value
[Why this matters — the "So that" clause]

## Rules Discovered
- [ ] Rule 1: ...
  - Example: ...
  - Example: ...
- [ ] Rule 2: ...

## Open Questions
- [ ] Question about ...
- [x] Resolved question? **Resolved: Answer and reasoning (-> BDR candidate if non-trivial)**

## Decisions Made
<!-- Choices between competing options, with reasoning. May include deferrals. -->
- **[Decision title]**: Chose A over B because ... (-> BDR candidate)
- **[Decision title]**: Deferred X because ...

## Out-of-Scope Behaviours
<!-- Valid behaviours that describe implementation details, not business rules -->
- [Behaviour]: noted because [why it's implementation, not a business rule]

## Rejected Behaviours
<!-- Behaviours categorically excluded from this feature — not deferred for later, and not just the "other option" in a decision. Deferrals belong in Decisions Made. -->
- Behaviour X: rejected because [reason]

## Glossary Candidates
- **[Term]**: [working definition or question about meaning]
```

Check rules off when the rule statement is confirmed and at least one concrete example has been agreed. Check open questions off when resolved (add **Resolved:** annotation with the answer). Add new sections or entries as rules, examples, and decisions emerge.

**Deferrals**: When a behaviour is explicitly deferred (not just unresolved), record it in **Decisions Made** with a "Deferred" prefix and the reason. If the deferral resolves an open question, also mark that question as resolved with a cross-reference.

## Discovery Phase

Adapt depth based on the quality of input. Rough ideas warrant more exploratory questions; detailed input warrants confirmation and gap-filling. Discovery is iterative — small steps, not a single pass.

### Kickoff

1. **Check for existing discoveries** — Scan `spec/.discovery/` for leftover progress files. If any are found, read them and present the user with options:
   - **Resume** — Continue the existing discovery where it left off. Before resuming, scan the progress file's rules and decisions against current `.feature` files and BDRs to detect conflicts that may have arisen since the session was paused.
   - **Discard** — Delete the leftover file and start fresh
   - **Set aside** — Leave it alone and start a new, separate discovery
   - **Merge** — Combine the existing progress with the new discovery into a single progress file. For each section (Rules, Open Questions, Decisions, etc.), integrate entries from both sources. When entries conflict (e.g., contradictory rules, different answers to the same question), present each contradiction to the user with resolution options before proceeding. Discard the old progress file after the merge is complete.
2. **Create the progress file** — Initialize with the user's idea, even if sparse. Commit what is known; leave unknowns as open questions.
3. **Restate the idea** — Parse the input and summarize the core capability in one sentence. Confirm alignment before going deeper.
4. **Identify the actor** — Determine who benefits. Ask directly if the input does not name a role. Record in the progress file.

### Iterative Questioning

Do not dump all questions at once. Work in small batches, grouped by topic.

**Grouping**: Organize questions thematically (e.g., preconditions, boundaries, error cases, permissions). Tackle one group at a time.

**Batching**: Within a group, ask 2–3 questions per turn. If the group has more questions, hold the rest for the next turn.

**Reacting**: After each user response, update the progress file, then formulate the *next* questions based on what the user just said — not by mechanically repeating remaining questions from a pre-planned list. The user's answers will reveal new angles, shift priorities, and close off entire lines of questioning.

**Loop**: Repeat this cycle until the current topic group stabilizes, then move to the next group. Continue looping across groups until the Quality Criteria checklist can be satisfied.

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

### Layer Check

When a proposed behaviour describes an implementation detail rather than a business rule, apply the Implementation Swap Test (see *Business Rules vs Implementation Details*). Reframe as the underlying business rule, or note as out-of-scope in the progress file.

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

Throughout the iterative loop — not just at the end — scan existing `.feature` files and BDRs across all domains in `spec/` for conflicts with the rules and decisions being formulated. Check as new rules and decisions emerge, not as a single pass after discovery. Look for:

- **Contradictory rules** — A new rule that directly contradicts a rule in an existing feature
- **Overlapping scenarios** — Scenarios that describe the same behaviour with different expected outcomes
- **Decision reversals** — A new decision that contradicts an accepted BDR

When a conflict is found, do not simply report the problem. Present 2–3 resolution options to help the user think through the trade-off:

1. **Keep existing, adjust new** — Modify the current discovery to align with what is already established
2. **Supersede existing** — Proceed with the new behaviour; the existing feature/BDR will be updated during consolidation
3. **Scope separation** — The two behaviours apply to different contexts; clarify the boundary so both can coexist

Record the resolution in the progress file under Decisions Made.

### Checkpoint

When the current topic groups have rules with examples and no immediate follow-up questions remain, present a summary of the progress file. Confirm understanding. Resolve any remaining open questions that block consolidation — or explicitly mark them as deferred.

If the summary review reveals no further gaps and the Quality Criteria checklist is satisfied, proceed to Consolidation.

### Pausing a Session

When the user wants to stop before consolidation is reached:

1. **Update the progress file** — Ensure all rules, examples, decisions, and open questions from the current conversation are recorded. Add a brief `## Session Status` note at the end of the progress file recording the current phase (discovery/checkpoint), which topic groups have been explored, and which remain.
2. **Summarize status** — Present a brief summary of what has been covered, what remains open, and which topic groups have not yet been explored.
3. **Keep the progress file** — Do not delete it. The file will be picked up when the user resumes (see Kickoff step 1).

## Consolidation Phase

Enter consolidation only when the Quality Criteria checklist (below) is satisfied: every rule has confirmed examples, open questions are resolved or explicitly deferred, and conflicts are addressed. Do not generate .feature files while rules are still being discovered.

### 1. Generate .feature File(s)

Transform discovered content into well-formed Gherkin:

- Story becomes the Feature narrative (As a / I want / So that)
- Each discovered rule becomes a `Rule:` keyword block
- Each example becomes a `Scenario:` or `Scenario Outline:` under its rule
- Unresolved open questions become `# TODO:` comments or `@wip` tags. `@wip` scenarios may use placeholder Then steps when the actual outcome is unresolved — the placeholder should be accompanied by a `# TODO` comment referencing the open question it depends on.
- Exclude scenarios about implementation details (see *Business Rules vs Implementation Details*)
- When a rule's classification as a business rule was contested during discovery, add a brief Gherkin comment above the `Rule:` block noting why the Implementation Swap Test was satisfied
- Group related scenarios under the same Rule block; split into separate .feature files only when features are genuinely distinct

Place generated files in `spec/<domain>/features/` (or the established convention).

**Feature files keep only the latest version.** If a `.feature` file already exists for this feature, overwrite it with the new content. There is no versioning for feature files — they represent the current specification.

### 2. Generate BDR File(s)

Create Behaviour Decision Records only for significant decisions — especially rejections and contested trade-offs where "why not" provides lasting value.

- Use the standard format for decisions with meaningful context and alternatives
- Use the lightweight format for minor decisions where a one-liner suffices
- Place BDR files in `spec/<domain>/decisions/` (e.g., `spec/orders/decisions/`) or the established convention

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

Present all proposed changes as a batch for the user to confirm before applying. If the user rejects specific terms from the batch, apply only the accepted changes and proceed. Do not re-propose rejected terms unless the user raises them again in a future session.

When a glossary term is updated or merged, scan all existing `.feature` files for usages of the old term. Propose updates to affected scenarios to maintain consistency. Check whether the term change affects the intent of any rules — if so, flag the affected features for the user to review before modifying.

If glossary updates result in changes to existing `.feature` files, re-run the Post-Consolidation Conflict Check (step 3) on the affected files.

### 5. Surface Out-of-Scope Behaviours

Before removing the progress file, check for items under **Out-of-Scope Behaviours**. If any exist, present them to the user as a summary — these are behaviours identified during discovery that don't belong in .feature files but may need coverage elsewhere (E2E tests, integration tests, etc.). Let the user decide how to handle them.

### 6. Remove the Progress File

Delete the progress file (e.g., `spec/.discovery/order-cancellation.md`). If no other discoveries are in progress, remove the `spec/.discovery/` directory as well. All discovery state is now captured in the generated .feature and BDR files. The progress file has served its purpose.

## Feature File Anatomy

A well-formed `.feature` file follows this structure. Use this as a reference when generating output.

```gherkin
@checkout
Feature: Shopping Cart Checkout
  As a customer
  I want to complete my purchase
  So that I receive the products I selected

  Rule: Authenticated customers can checkout

    Background:
      Given a customer with items in their cart

    Scenario: Checkout succeeds when customer is logged in
      Given the customer is logged in
      When the customer proceeds to checkout
      Then the order should be created

    Scenario: Checkout requires login for unauthenticated customer
      Given the customer is not logged in
      When the customer proceeds to checkout
      Then the customer should be required to log in

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
- **Background** -- Shared preconditions. When placed at Feature level, applies to all scenarios. When placed inside a Rule block, applies only to that rule's scenarios.
- **Rule:** -- Groups scenarios that illustrate a single business rule. Prefer Rule blocks over flat scenario lists.
- **Scenario** -- A concrete example of the rule. One behaviour per scenario.
- **Scenario Outline + Examples** -- Parameterized scenarios for data-driven rules. Use when multiple inputs exercise the same logic path.
- **Given/When/Then** -- Declarative language describing state, action, and outcome. Express intent, not mechanism — see *Business Rules vs Implementation Details* below.
- **And / But** -- Continue the preceding Given, When, or Then step. Multiple `And` after `Then` are fine when outcomes are interdependent aspects of the same rule. Multiple `And` after `When` are a smell — they often signal conflated actions that should be separate scenarios.
- **Active vs passive When** -- Use active voice ("When the customer requests cancellation") when the scenario tests an actor's action. Use passive voice ("When the order is cancelled") when the scenario tests a system consequence of an action already covered by another rule — the focus is on what happens next, not who triggered it.

## Business Rules vs Implementation Details

BDD features express **business rules** — constraints and policies that remain valid regardless of how the system is built. When a proposed scenario describes an implementation detail rather than a business rule, it belongs in other test layers (E2E, integration, unit), not in a .feature file.

### The Implementation Swap Test

> "If the implementation changed — different UI framework, different API protocol, different data store, different infrastructure — would this scenario still be valid?"

- **Yes** → It describes a business rule. Keep it in the .feature file.
- **No** → It describes an implementation detail. Note it as out-of-scope in the progress file.

### Examples

When a scenario drifts into implementation details, reframe it around the underlying business rule:

| Implementation detail (out-of-scope) | Business rule (keep in .feature) | Why |
|---|---|---|
| "click the Active tab" | "filter by Active status" | The filter is the rule; the tab is one possible control |
| "scroll to bottom of list" | "request the next page of results" | Pagination is the rule; scroll-trigger is UI implementation |
| "POST to /api/orders" | "place an order" | Order placement is the rule; the endpoint is an API detail |
| "write a row to the orders table" | "record the order" | Recording is the rule; the storage mechanism is infrastructure |
| "toast notification appears" | "the user is notified of the result" | Notification is the rule; the delivery mechanism is implementation |
| "retry with exponential backoff" | "handle transient failures gracefully" | Resilience is the rule; the retry strategy is implementation |

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
# superseded-by: BDR-YYYY  # added when this BDR is superseded
---

**Feature**: [path relative to spec/ directory, e.g., orders/features/cancellation.feature]
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
# superseded-by: BDR-YYYY  # added when this BDR is superseded
---

## Scope

**Feature**: checkout/features/cart.feature  <!-- path relative to spec/ directory -->
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

Determine the next BDR number by scanning all `spec/*/decisions/` directories for the highest existing `BDR-NNNN` and incrementing. BDR numbers are global across all domains. If no BDRs exist yet, start at `BDR-0001`.

## Quality Criteria

Apply this checklist before consolidation:

- [ ] Each rule has at least one concrete example
- [ ] Every scenario has Given (precondition), When (action), and Then (outcome)
- [ ] Scenarios express business rules, not implementation details (Implementation Swap Test)
- [ ] Implementation-level behaviours noted as out-of-scope, not discarded
- [ ] Each scenario illustrates a single rule (preconditions may reference other rules, but the scenario's purpose is to demonstrate one)
- [ ] No conflated When (multiple independent actions) or Then (unrelated outcomes)
- [ ] Scenario names describe the rule being illustrated
- [ ] Open questions are explicitly flagged, not silently assumed
- [ ] Deferred questions have corresponding entries in Decisions Made with reasoning
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

## Anti-Patterns

Ref: [Cucumber Anti-Patterns](http://www.thinkcode.se/blog/2016/06/22/cucumber-antipatterns)

### Abstraction & Language

- **Layer mixing** -- Two forms: (1) *Imperative phrasing* -- "clicks Submit" instead of "submits the form"; (2) *Misplaced scenarios* -- entire scenario about implementation details (UI mechanics, API specifics, infrastructure) that belongs in another test layer. Apply the Implementation Swap Test.
- **Incidental details** -- Irrelevant setup (passwords, navigation, timestamps) that obscures the business rule. Keep only details essential to understanding the rule; push the rest to Background or remove entirely.
- **Generic "I"** -- Using "I" or "the user" instead of a named role. Use the actor from discovery (e.g., "the recruiter", "a hiring manager").

### Scenario Structure

- **Conflated rules** -- Multiple business rules in one scenario. Each scenario illustrates one rule.
- **Multiple When clauses** -- Several independent actions in one scenario. Multiple When/And steps signal multiple behaviours that should be split.
- **Unrelated Then clauses** -- Independent outcomes (e.g., customer refunded AND inventory updated AND finance notified) belong in separate scenarios. Keep multiple Then only when outcomes are genuinely interdependent.
- **Scenario Outline overuse** -- Outlines work for data-driven rules but cause combinatorial explosion if overused. Don't use them to enumerate unrelated cases.
- **Poor scenario names** -- Generic names like "Test login flow" don't communicate purpose. Apply the "This is the one where..." test -- the name should state the rule being illustrated.
- **Coupled scenarios** -- Scenarios that depend on execution order or side effects of other scenarios. Each scenario must set up its own state via Given steps (or Background) and be executable in isolation.

### Process

- **Premature consolidation** -- Jumping to .feature files before rules stabilize. Stay in discovery until the Quality Criteria checklist is satisfied.
- **Missing rejections** -- Forgetting to capture "why not" for rejected options. The reasoning behind exclusions is often more valuable than what was included.
- **Orphaned progress file** -- Leaving progress files in `spec/.discovery/` after consolidation. Always clean up.
- **Over-specifying** -- Turning every edge case into a scenario. Not every edge case warrants a scenario in the first pass -- note it as an open question or defer it.
- **Assumed answers** -- Silently deciding an open question instead of flagging it. When uncertain, ask or mark it explicitly.
