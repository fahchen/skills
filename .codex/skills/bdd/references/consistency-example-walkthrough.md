# Consistency Check Example Walkthrough

Two compact examples: one incremental check and one full-mode scan.

## Example 1: Incremental Check

Changed file:

- `spec/orders/features/cancellation.feature`

Conflict found:

- changed rule allows cancellation up to 1 hour after dispatch
- existing BDR says no cancellation after dispatch

Resolution shape:

1. create a new superseding BDR
2. mark the old BDR as superseded
3. update glossary if dispatch semantics changed

## Example 2: Full Check

Files scanned:

- `glossary.md`
- `orders/features/*.feature`
- `shipping/features/*.feature`
- `orders/decisions/*.md`

Typical findings:

- refund timeline mismatch across features
- scenario contradicts a deferred BDR
- cross-domain wording inconsistency that is misleading rather than strictly contradictory

Use this example when you need to show the difference between incremental and full-mode resolution behavior.
