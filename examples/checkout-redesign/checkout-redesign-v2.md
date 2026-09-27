# Checkout redesign — v2

- Status: confirmed
- Supersedes: v1
- Date: 2026-03-05
- Session: e5f6a7b8-…
- Trigger: discovery — the provider SDK only mounts hosted fields with a short-lived client token that our server has to mint

## Changes since v1

| ID | Was | Now | Why |
|---|---|---|---|
| A | one change | split → A.1, A.2 | the token endpoint is backend work and can ship on its own; the form cannot start until it exists |
| D | — | new, order 3 | while testing A, found that guest checkout drops the email on step change |
| C | planned | dropped | design moved the summary into the step header, which B's layout change already covers |

## Background

Checkout is one long form. Card entry is a hand-rolled input; addresses are typed in full; the order summary sits below the fold.

**New in v2:** the provider's hosted fields need a client token from `POST /payments/client-token`, minted per session with our secret key. Without it the SDK renders nothing and logs no error, which is why A looked blocked for a day.

## Changes

| Order | ID | Change | Ref | Depends on | Status |
|---|---|---|---|---|---|
| 1 | A.1 | Client-token endpoint | `payments-client-token` | — | shipped #412 |
| 2 | A.2 | Card form on hosted fields | `checkout-card-form` | A.1 | pr #415 |
| 3 | D | Keep the guest email across steps | `checkout-guest-email` | — | planned |
| 4 | B | Address autocomplete | `checkout-address-autocomplete` | — | planned |
| — | A | Card form on the provider's hosted fields | — | — | split → A.1, A.2 |
| — | C | Sticky order summary | — | — | dropped: covered by B's step header |

### A.1 — Client-token endpoint

- Goal: the server mints a short-lived client token per checkout session.
- Acceptance: the endpoint returns a token the sandbox SDK accepts; the secret key never reaches the browser.

### A.2 — Card form on hosted fields

- Goal: as v1's A, now on top of A.1.
- Acceptance: a test card completes checkout in the sandbox.

### D — Keep the guest email across steps

- Goal: a guest's email survives moving between steps.
- Acceptance: enter email, go to payment and back — the email is still there.

### B — Address autocomplete

- Goal: typing an address suggests matches; picking one fills every field. Also moves the order total into the step header (absorbs C).
- Acceptance: picking a suggestion fills every field; the total is visible in the step header.

## Out of scope

- Saved cards: separate project next quarter.

## Follow-ups

| # | Found in | Item | Confirmed |
|---|---|---|---|
| 1 | A.1 | The SDK fails silently without a token; add a console warning in dev builds | yes |

## Open questions

(none)

## Status log

- 2026-03-05 · e5f6a7b8-… · v2: A split, D inserted, C dropped
- 2026-03-06 · e5f6a7b8-… · A.1 merged as #412
- 2026-03-06 · e5f6a7b8-… · A.2 opened as #415
