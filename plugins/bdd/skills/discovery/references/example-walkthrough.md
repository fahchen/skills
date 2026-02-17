# Example Walkthrough: Order Cancellation

A complete worked example showing the discovery-to-consolidation flow for an order cancellation feature on an e-commerce platform.

---

## Progress File (at consolidation)

This is `spec/.discovery/order-cancellation.md` at the point of consolidation. All rules are confirmed and all open questions are resolved or explicitly deferred.

```markdown
# Discovery: Order Cancellation

## Story
Allow customers to cancel orders they no longer want, subject to fulfilment constraints.

## Actor
Customer

## Value
Receive a refund without waiting for delivery

## Rules Discovered
- [x] Rule 1: Orders can be cancelled before dispatch
  - Example: Customer places order at 10:00, cancels at 10:15 while status is "confirmed" -- cancellation succeeds
  - Example: Customer cancels order that is still in "payment processing" -- cancellation succeeds
- [x] Rule 2: Orders cannot be cancelled after dispatch
  - Example: Order status is "dispatched" -- cancellation is refused with explanation
  - Example: Order status is "delivered" -- cancellation is refused, customer directed to returns
- [x] Rule 3: Cancellation triggers a full refund to the original payment method
  - Example: Order paid by credit card -- refund issued to same card
  - Example: Order paid with store credit -- store credit balance restored
  - Example: Order still has a pending payment authorisation -- authorisation is voided
- [x] Rule 4: Customers receive confirmation of cancellation
  - Example: Successful cancellation -- confirmation sent with cancellation reference number

## Open Questions
- [x] Can a customer cancel individual items from a multi-item order (partial cancellation)? **Resolved: Deferred -- full-order cancellation only for initial release. Simplifies inventory and refund logic. (-> Decision: "Partial cancellation not supported"; BDR candidate)**
- [x] What happens if a refund fails (e.g., expired card)? **Resolved: Deferred -- needs input from payments team before policy can be defined. (-> Decision: "Refund failure handling") Marked @wip in feature file.**
- [x] Is there a time window after dispatch where cancellation is still possible (e.g., same-day recall)? **Resolved: No -- once dispatched, the order enters the returns flow.**
- [x] Is "customers receive confirmation of cancellation" a business rule or an implementation detail? **Resolved: Business rule -- the business requires verifiable proof of cancellation regardless of delivery mechanism (email, SMS, in-app). Assessed with Implementation Swap Test.**

## Decisions Made
- **No cancellation after dispatch**: Chose to cut off cancellation at the point of dispatch rather than allowing a grace window. Dispatch triggers downstream logistics that are expensive to reverse. (-> BDR candidate)
- **Partial cancellation not supported**: Deferred -- may revisit in a future iteration. Full-order cancellation only for now. (Resolves: "Can a customer cancel individual items..." -> BDR candidate)
- **Refund failure handling**: Deferred -- requires input from payments team before policy can be defined. (Resolves: "What happens if a refund fails...") Marked @wip in the feature file.

## Out-of-Scope Behaviours
<!-- Valid behaviours that describe implementation details, not business rules -->
- Email delivery retry logic for cancellation confirmations: noted because the retry mechanism is an infrastructure concern; the business rule is that the customer receives confirmation (Rule 4)
- Refund processing latency (3–5 business days): noted because the timeline depends on payment provider SLAs, not a business policy the system controls

## Rejected Behaviours
<!-- Behaviours categorically excluded from this feature — not deferred for later, and not just the "other option" in a decision. Deferrals belong in Decisions Made. -->
- Admin-initiated cancellations on behalf of customers: rejected because this feature covers customer self-service only; admin actions belong in a separate back-office feature.

## Glossary Candidates
- **dispatch**: the point at which a warehouse hands a parcel to a carrier
- **partial cancellation**: cancelling individual items rather than the entire order
- **store credit**: balance held in a customer's account usable as payment
- **returns flow**: post-delivery process for returning products
- **payment authorisation**: hold placed on a payment method before funds are captured
```

The refund failure question was deferred and marked `@wip` in the consolidated feature file.

---

## Consolidated Feature File

Generated as `spec/orders/features/cancellation.feature` from the progress file above.

```gherkin
@orders @cancellation
Feature: Order Cancellation
  As a customer
  I want to cancel an order I no longer need
  So that I receive a refund without waiting for delivery

  Rule: Orders can be cancelled before dispatch

    Scenario: Customer cancels a confirmed order
      Given a customer has a confirmed order
      When the customer requests cancellation
      Then the order should be cancelled

    Scenario: Customer cancels an order still processing payment
      Given a customer has an order in payment processing
      When the customer requests cancellation
      Then the order should be cancelled

  Rule: Orders cannot be cancelled after dispatch

    Scenario: Customer attempts to cancel a dispatched order
      Given a customer has an order that has been dispatched
      When the customer requests cancellation
      Then the cancellation should be refused
      And the customer should be informed that the order is already in transit

    Scenario: Customer attempts to cancel a delivered order
      Given a customer has an order that has been delivered
      When the customer requests cancellation
      Then the cancellation should be refused
      And the customer should be directed to the returns process

  # Passive "When the order is cancelled" because these scenarios test the refund
  # consequence, not the cancellation action itself (already covered by Rule 1).
  Rule: Cancellation triggers a full refund to the original payment method

    Scenario: Refund issued to credit card
      Given a customer has a confirmed order paid by credit card
      When the order is cancelled
      Then a full refund should be issued to the original credit card

    Scenario: Refund issued as store credit
      Given a customer has a confirmed order paid with store credit
      When the order is cancelled
      Then the store credit balance should be restored in full

    Scenario: Payment authorisation is voided on cancellation
      Given a customer has an order with a pending payment authorisation
      When the order is cancelled
      Then the payment authorisation should be voided

    @wip
    # TODO: Clarify with payments team what happens when a refund fails (e.g., expired card)
    Scenario: Refund to an expired payment method
      Given a customer has a confirmed order paid with a credit card that has since expired
      When the order is cancelled
      Then the refund should be handled according to the failed-refund policy

  # Rule 4 was assessed with the Implementation Swap Test: the business requires
  # customers to have verifiable proof of cancellation regardless of delivery
  # mechanism (email, SMS, in-app). This is a business rule, not implementation.
  Rule: Customers receive confirmation of cancellation

    Scenario: Customer receives cancellation confirmation
      Given a customer has a confirmed order
      When the order is cancelled
      Then the customer should receive a cancellation confirmation with a reference number
```

---

## Standard BDR

Saved as `spec/orders/decisions/BDR-0001-no-cancellation-after-dispatch.md`.

```markdown
---
id: BDR-0001
title: No cancellation after dispatch
status: accepted
date: 2026-02-07
summary: Cut off cancellation at dispatch rather than offering a post-dispatch grace window
---

**Feature**: orders/features/cancellation.feature
**Rule**: Orders cannot be cancelled after dispatch

## Context

During discovery of order cancellation, the question arose whether customers
should be allowed to cancel an order that has already been dispatched but not
yet delivered. Some e-commerce platforms offer a short grace window (e.g.,
30 minutes after dispatch) during which a courier recall can be attempted.

## Behaviours Considered

### Option A: Hard cut-off at dispatch
Cancellation is only possible while the order status is before "dispatched".
Once the warehouse hands the parcel to the carrier, the order enters the
returns flow instead.

### Option B: Grace window after dispatch
Allow cancellation for up to 30 minutes after dispatch. The system would
attempt a courier recall via the carrier API. If the recall fails, the
customer is told to use the returns process.

## Decision

Chose Option A -- hard cut-off at dispatch. The dispatch event is a clean,
unambiguous boundary. Once a parcel is in carrier possession, recall success
rates vary widely by carrier (some do not support recall at all), making the
grace-window experience unreliable. Failed recalls would leave customers
confused about whether their order is cancelled or still arriving.

The returns process already exists and handles post-dispatch changes
consistently regardless of carrier.

## Rejected Alternatives

Option B was rejected because:

- Courier recall is not universally supported across carriers.
- Recall fees are unpredictable and would need to be absorbed or passed to the
  customer, both of which introduce friction.
- A partially-reliable grace window sets expectations the system cannot
  consistently meet, leading to support escalations.
- The existing returns flow already covers the post-dispatch case with a known
  cost structure.
```

---

## Lightweight BDR

Saved as `spec/orders/decisions/BDR-0002-partial-cancellation-not-supported.md`.

```markdown
---
id: BDR-0002
title: Partial cancellation not supported
status: deferred
date: 2026-02-07
summary: Deferred partial item cancellation; full-order only for initial release
---

## Scope

**Feature**: orders/features/cancellation.feature
**Rule**: Orders can be cancelled before dispatch

## Reason

Partial item cancellation introduces complex partial-refund calculations and
inventory re-reservation logic. Full-order cancellation is sufficient for the
initial release.
```

No BDR was generated for the "Refund failure handling" deferral because no alternative behaviours were considered — the decision was purely to wait for more information, and the `@wip` tag in the feature file preserves sufficient context. BDRs are most valuable when they capture reasoning behind a choice between alternatives (BDR-0001) or preserve context for a deferred capability (BDR-0002).

---

## Post-Consolidation Conflict Check

Scanned all domains in `spec/` for existing `.feature` files and BDRs. No other features or decisions existed yet, so no conflicts were found. In a project with existing specifications, any contradictions would be presented to the user with resolution options before proceeding.

---

## Glossary Update

After consolidation, the following terms were proposed for `spec/glossary.md` (confirmed with the user before applying):

| Term | Definition |
|------|------------|
| dispatch | The point at which a warehouse hands a parcel to a carrier for delivery. Cancellation is no longer possible after dispatch. |
| partial cancellation | Cancelling individual items from a multi-item order rather than the entire order. Not supported in the initial release. |
| payment authorisation | A hold placed on a customer's payment method before funds are captured. Voided when an order is cancelled before capture. |
| returns flow | The post-delivery process for returning products and obtaining a refund. Applies to orders that have already been dispatched. |
| store credit | A balance held in a customer's account that can be used as a payment method. Restored in full upon cancellation. |

---

## Out-of-Scope Behaviours Surfaced

Before removing the progress file, the following out-of-scope behaviours were presented to the user:

- **Email delivery retry logic** for cancellation confirmations — infrastructure concern, not a business rule
- **Refund processing latency** (3–5 business days) — depends on payment provider SLAs, not a business policy

The user noted these for future E2E/integration test coverage. They are not included in the `.feature` file.

---

## Final File Structure

After consolidation, the progress file is removed. The resulting tree:

```
spec/
├── glossary.md
└── orders/
    ├── features/
    │   └── cancellation.feature
    └── decisions/
        ├── BDR-0001-no-cancellation-after-dispatch.md
        └── BDR-0002-partial-cancellation-not-supported.md
```
