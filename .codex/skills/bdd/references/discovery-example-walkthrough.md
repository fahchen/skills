# Example Walkthrough: Order Cancellation

A compact worked example showing the discovery-to-consolidation flow for an order cancellation feature on an e-commerce platform.

## Progress File Snapshot

```markdown
# Discovery: Order Cancellation

## Story
Allow customers to cancel orders they no longer want, subject to fulfilment constraints.

## Actor
Customer

## Value
Receive a refund without waiting for delivery

## Rules Discovered
- [x] Orders can be cancelled before dispatch
- [x] Orders cannot be cancelled after dispatch
- [x] Cancellation triggers a full refund to the original payment method
- [x] Customers receive confirmation of cancellation

## Decisions Made
- **No cancellation after dispatch**: hard cutoff at dispatch
- **Partial cancellation not supported**: deferred for a future iteration
```

## Consolidation Outcome

- feature file created at `spec/orders/features/cancellation.feature`
- BDR created for the hard dispatch cutoff
- deferred question recorded for partial cancellation

Use this example when you need to explain what "finished discovery" should look like before consolidation.
