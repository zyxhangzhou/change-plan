> Superseded by [v2](checkout-redesign-v2.md) on 2026-03-05 — the payment SDK needs a server-issued token, so A splits in two.

# Checkout redesign — v1

- Status: confirmed
- Supersedes: none
- Date: 2026-03-02
- Session: a1b2c3d4-…
- Trigger: initial — product asked for the checkout redesign

## Background

Checkout is one long form. Card entry is a hand-rolled input; addresses are typed in full; the order summary sits below the fold.

## Changes

| Order | ID | Change | Ref | Depends on | Status |
|---|---|---|---|---|---|
| 1 | A | Card form on the provider's hosted fields | `checkout-card-form` | — | in-progress |
| 2 | B | Address autocomplete | `checkout-address-autocomplete` | — | planned |
| 3 | C | Sticky order summary | `checkout-order-summary` | — | planned |

### A — Card form on the provider's hosted fields

- Goal: card number, expiry and CVC come from the provider's hosted fields.
- Scope: in — the payment step. out — saved cards.
- Acceptance: a test card completes checkout in the sandbox.

### B — Address autocomplete

- Goal: typing an address suggests matches; picking one fills every field.
- Acceptance: picking a suggestion fills street, city, postcode, country.

### C — Sticky order summary

- Goal: the order summary stays visible while the form scrolls.
- Acceptance: at 1280×800 the total is visible at every step.

## Out of scope

- Saved cards: separate project next quarter.

## Follow-ups

(none yet)

## Open questions

(none)

## Status log

- 2026-03-02 · a1b2c3d4-… · v1 confirmed; A started
