# Consistency Check — Example Walkthrough

Two worked examples: one incremental, one full-mode check.

---

## Example 1: Incremental Check

The user modified `spec/domains/orders/features/cancellation.feature` to allow cancellation up to 1 hour after dispatch (previously: no cancellation after dispatch).

### Findings Ledger

```markdown
# Consistency Check — incremental
<!-- Started: 2026-03-15 -->
<!-- Scope: spec/domains/orders/features/cancellation.feature -->

## Points Observed

- [P1] domains/orders/features/cancellation.feature:14 — Orders can be cancelled up to 1 hour after dispatch (CHANGED)
- [P2] domains/orders/features/cancellation.feature:22 — Cancellation after 1-hour window is rejected (CHANGED)
- [P3] decisions/BDR-0001.md — No cancellation after dispatch (accepted)
- [P4] domains/shipping/features/dispatch.feature:10 — Dispatch triggers carrier pickup immediately
- [P5] domains/orders/features/cancellation.feature:30 — Refund is issued on cancellation (CHANGED — now includes post-dispatch partial refund)
- [P6] glossary.md — "Dispatched" = order handed to carrier, irreversible

## Conflicts Detected

### C1: Cancellation window contradicts BDR-0001
- **Points**: P1 vs P3
- **Nature**: decision reversal
- **Detail**: Changed feature allows cancellation up to 1h after dispatch, but BDR-0001 explicitly decided "no cancellation after dispatch" and rejected time-window approaches.
- **Severity**: breaking
- **Source priority**: P1 is changed (takes priority)

### C2: "Irreversible" dispatch vs post-dispatch cancellation
- **Points**: P1 vs P6
- **Nature**: glossary inconsistency
- **Detail**: Glossary defines "Dispatched" as irreversible, but the change allows reversing (cancelling) a dispatched order within 1 hour.
- **Severity**: misleading
- **Source priority**: P1 is changed (takes priority)

## Resolutions

### C1: Cancellation window contradicts BDR-0001
- **Decision**: Supersede BDR-0001 with new BDR allowing 1h cancellation window
- **Action**: Created BDR-0003 (supersedes BDR-0001). Updated BDR-0001 frontmatter: status: superseded, superseded-by: BDR-0003

### C2: "Irreversible" dispatch vs post-dispatch cancellation
- **Decision**: Update glossary definition to reflect cancellation window
- **Action**: glossary.md "Dispatched" definition updated to: "Order handed to carrier. Cancellation possible within the grace window; irreversible after."
```

### Conflict Presentation to User

> **C1 (breaking): Cancellation window contradicts BDR-0001**
>
> Your change allows cancellation up to 1h after dispatch, but BDR-0001 explicitly decided against post-dispatch cancellation and rejected time-window approaches in its Rejected Alternatives.
>
> Since this is incremental mode, your change takes priority. Proposed resolution:
> - Create **BDR-0003** documenting the new 1h cancellation window decision, with `Supersedes: BDR-0001`
> - Update BDR-0001 frontmatter: `status: superseded`, `superseded-by: BDR-0003`
> - BDR-0001 body remains unchanged (immutability rule)
>
> **C2 (misleading): Glossary "Dispatched" says irreversible**
>
> The glossary defines "Dispatched" as irreversible, which conflicts with the new cancellation window.
>
> Proposed resolution:
> - Update glossary definition to: *"Order handed to carrier. Cancellation possible within the grace window; irreversible after."*
> - Cascade check: `domains/shipping/features/dispatch.feature` uses "Dispatched" but doesn't claim irreversibility in its scenarios — no cascade needed.
>
> Confirm both resolutions?

---

## Example 2: Full Check

A full scan of `spec/` with three domains: `domains/orders`, `domains/shipping`, `domains/authentication`.

### Findings Ledger (excerpt)

```markdown
# Consistency Check — full
<!-- Started: 2026-03-15 -->
<!-- Scope: full spec/ -->

## Points Observed

- [P1] glossary.md — "Member" = customer with active subscription
- [P2] glossary.md — "Churned subscriber" = customer who cancelled within last 30 days
- [P3] domains/orders/features/cancellation.feature:14 — Orders can be cancelled before dispatch
- [P4] domains/orders/features/cancellation.feature:28 — Refund issued within 5 business days
- [P5] domains/shipping/features/returns.feature:15 — Returns accepted within 30 days; refund within 3 business days
- [P6] domains/authentication/features/login.feature:10 — Members can access order history
- [P7] domains/authentication/features/login.feature:18 — Churned subscribers retain read-only access for 90 days
- [P8] domains/orders/features/loyalty.feature:12 — Members earn 1 point per dollar spent
- [P9] domains/orders/features/loyalty.feature:20 — Churned subscribers lose all points immediately
- [P10] decisions/BDR-0002.md — Partial cancellation not supported (deferred)
- [P11] domains/orders/features/cancellation.feature:35 — Scenario: "Customer cancels one item from multi-item order"
- [P12] backlog.md:5 — Deferred: "Gift card payments" (discovered: 2026-02-10)
- [P13] domains/checkout/features/gift-card-payments.feature:1 — Feature: Gift Card Payments

## Conflicts Detected

### C1: Refund timeline inconsistency
- **Points**: P4 vs P5
- **Nature**: contradictory rules
- **Detail**: Cancellation refunds take 5 business days, but return refunds take 3 business days. No justification for the difference found in any BDR.
- **Severity**: misleading

### C2: Partial cancellation scenario vs deferred BDR
- **Points**: P11 vs P10
- **Nature**: decision reversal
- **Detail**: Cancellation feature has a scenario for single-item cancellation from a multi-item order, but BDR-0002 explicitly deferred partial cancellation.
- **Severity**: breaking

### C4: Gift card payments listed as deferred but feature exists
- **Points**: P12 vs P13
- **Nature**: stale backlog
- **Detail**: Backlog lists "Gift card payments" as a deferred feature, but `domains/checkout/features/gift-card-payments.feature` now exists.
- **Severity**: cosmetic

### C3: Churned subscriber point forfeiture vs retention access
- **Points**: P9 vs P7
- **Nature**: contradictory rules (cross-domain)
- **Detail**: Not directly contradictory but worth flagging: churned subscribers lose points immediately (P9) yet retain account access for 90 days (P7). A churned subscriber viewing their account would see 0 points despite being within retention window. This may be intentional but the intent is not documented.
- **Severity**: misleading
```

### Conflict Presentation to User

> **C1 (misleading): Refund timeline — 5 days vs 3 days**
>
> - `domains/orders/features/cancellation.feature:28` says refunds take 5 business days
> - `domains/shipping/features/returns.feature:15` says refunds take 3 business days
>
> Options:
> 1. **Standardize to 3 days** — Update cancellation refund timeline
> 2. **Standardize to 5 days** — Update returns refund timeline
> 3. **Document the difference** — Create a BDR explaining why cancellation refunds are slower (e.g., different processing pipeline)
>
> **C2 (breaking): Partial cancellation scenario exists despite deferral**
>
> - `domains/orders/features/cancellation.feature:35` has a scenario for cancelling one item from a multi-item order
> - `decisions/BDR-0002` explicitly deferred partial cancellation
>
> Options:
> 1. **Remove the scenario** — Align with BDR-0002's deferral decision
> 2. **Supersede BDR-0002** — Partial cancellation is now in scope; create BDR-NNNN to document this
> 3. **Tag as @wip** — Keep the scenario but mark it as provisional pending the deferred decision
>
> **C3 (misleading): Churned subscriber — points gone but access remains**
>
> - `domains/orders/features/loyalty.feature:20` forfeits points immediately on churn
> - `domains/authentication/features/login.feature:18` retains read-only access for 90 days
>
> Not contradictory, but the interaction may surprise users. Options:
> 1. **Document as intentional** — Add a comment or BDR noting the deliberate asymmetry
> 2. **Grace period for points too** — Align point forfeiture with the 90-day retention window
> 3. **No action** — Accept the current state as-is (different policies for different concerns)
>
> **C4 (cosmetic): Gift card payments — backlog says deferred but feature exists**
>
> - `backlog.md:5` lists "Gift card payments" as a deferred feature
> - `domains/checkout/features/gift-card-payments.feature` now implements it
>
> Options:
> 1. **Remove from backlog** — The feature has been implemented; the deferral is stale
> 2. **No action** — Keep the backlog entry as historical context

---

## Key Takeaways

- **Incremental mode** always favors the changes — resolutions adjust existing specs
- **Full mode** presents options neutrally — no side has automatic priority
- **Points are recorded incrementally** — each new point is compared against all previous points as it's extracted
- **Cascade awareness** — resolving C1 in the full example might affect other scenarios referencing refund timelines; always trace the impact
- **BDR immutability** — when a decision changes, create a new BDR and supersede the old one; never edit the old BDR's body
