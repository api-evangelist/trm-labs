---
name: Screen a blockchain address for sanctions exposure
description: Use the TRM Sanctions API and Chainabuse to determine whether one or more blockchain addresses have sanctions exposure before transacting.
api: openapi/trm-labs-sanctions-openapi.yml
operations: [PublicV1SanctionsScreeningPost, CheckSanctionedAddress]
---

# Screen a blockchain address for sanctions exposure

Determine whether blockchain addresses carry sanctions exposure before allowing
a transaction, onboarding a counterparty, or releasing funds.

## Auth
- HTTP Basic. For the TRM Sanctions API supply your API key as **both** the
  username and password. Request a key at https://www.trmlabs.com/products/sanctions.
- Default limit without a key: 1 req/sec, 100 req/day. With a key: 1000 req/sec,
  100k req/day. Respect `X-RateLimit-*` headers and back off on `Retry-After` when
  you receive `429`.

## Steps
1. **Bulk screen** — `PublicV1SanctionsScreeningPost`
   `POST https://api.trmlabs.com/public/v1/sanctions/screening`
   Body is a JSON array of `{ "address": "<addr>" }`. The response is a parallel
   array of `{ address, isSanctioned }`. Treat `isSanctioned: true` as a block.
2. **Confirm and label (optional)** — `CheckSanctionedAddress`
   `GET https://api.chainabuse.com/v0/sanctioned-addresses/{address}`
   Returns `SanctionedAddressPayload[]` with `label` and `chain` when the address
   is on a list; an empty array means no match.

## Rules
- Never fabricate a screening verdict — always call the API for each address.
- On `401` re-check that the API key is passed as both Basic username and password.
- Handle the documented error envelope `{ reason }` (see errors/trm-labs-problem-types.yml).
