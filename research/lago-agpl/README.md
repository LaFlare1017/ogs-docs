# R-4 Evidence Pack — Lago (AGPL — concepts only; no code, no creative UI)

Target: getlago.com public docs + public repo README/wiki descriptions. Method: public documentation study, Oct 18 2026. **AGPLv3 boundary:** Lago's platform is AGPL-licensed — this pack records documentary concepts only; no source code, code-derived implementation details, or creative UI may enter `ledger/`. (Their separate SDK/MCP repos being MIT does not change the platform's status.) [Lago license](https://github.com/getlago/lago)

## Architecture

Documented flow: **usage events → metering → pricing/credits → entitlements → invoices → payments → revenue**; metering/pricing independent from payments. Public wiki (dated): Postgres for transactional data + ClickHouse/Kafka for raw-event analytics; API/worker/clock services with dedicated queues. [Repo overview](https://github.com/getlago/lago) · [ClickHouse events wiki](https://github.com/getlago/lago/wiki/Using-Clickhouse-to-scale-an-events-engine) · [Dev environment](https://github.com/getlago/lago/wiki/Development-Environment)

## Plans/charges

Plan = pricing + cadence + taxes + entitlements + fixed charges + usage charges + minimum commitments + invoicing rules; must attach to a subscription. Metered metrics reset per period; **recurring metrics carry forward** (terminology note: "current vs renewable" is not Lago's documented wording). Graduated (per-tier rates) vs volume (total-volume rate) tiering; billing in advance/arrears; proration; filters let one metric price differently by dimension. **Key risk they document:** unfinalized-period usage is re-rated with *current* charge config — finalized invoices lock; replay without a versioned pricing snapshot diverges. [Plans](https://getlago.com/docs/guide/plans/overview) · [Usage charges](https://getlago.com/docs/guide/plans/charges/usage-based-charges) · [Graduated](https://getlago.com/docs/guide/plans/charges/charge-models/graduated) · [Volume](https://getlago.com/docs/guide/plans/charges/charge-models/volume) · [Filters](https://getlago.com/docs/guide/plans/charges/charges-with-filters)

## Subscription lifecycle

Future-start/pending subscriptions; payment-gated activation. Upgrade/downgrade classified by comparing **daily subscription-fee amounts**: upgrade applies immediately (credit note for unused advance-paid days, or immediate prorated old-plan invoice in arrears); downgrade schedules at period end. External subscription ID persists across changes. [Upgrades/downgrades](https://getlago.com/docs/guide/subscriptions/upgrades-downgrades)

## Invoices / credit notes

Invoice object has `version_number` (increment rules unverified — treat as attribute, not an immutability guarantee). Void paths: ordinary void of unpaid finalized invoices, or forced void + generated credit note; credit notes can offset, refund, or store future value; multiple per invoice with unique numbers; manual issuance is Premium. [Void API](https://getlago.com/docs/api-reference/invoices/void) · [Credit notes](https://getlago.com/docs/guide/invoicing/credit-notes/overview)

## Wallets (prepaid drawdown)

Scoped to fee types/metrics; applied automatically on eligible invoices **post-tax subtotal after credit notes**; configurable application priority (expiry/creation tie-breaks); invoiced Balance vs **Ongoing Balance** (incl. current usage + drafts); purchased credits activate after payment; threshold-triggered top-ups; recurring top-ups Premium. [Wallet overview](https://getlago.com/docs/guide/wallet-and-prepaid-credits/overview) · [Top-ups](https://getlago.com/docs/guide/wallet-and-prepaid-credits/wallet-top-up-and-void)

## What to adopt as design (10 ideas) — and what to harden beyond Lago

Adopt (as designs): append-only usage journal with stable IDs/timestamps/supersession; versioned + effective-dated rating rules persisted per result; metering/rating separation with stable metric identities; typed aggregation (period-reset vs carry-forward); declarative tier models with published test vectors; dimension pricing with recorded matched rule; scheduled prorations with before/after snapshots; invoice composition trace (events→fees→tax→coupons→wallet→credit notes); wallet-as-transactions ledger with deterministic ordering; corrections as append-only compensating records (never erasure).

Harden beyond Lago (their documented risks): stronger idempotency than "transaction ID + can-appear-again-with-new-timestamp" ([ingestion](https://getlago.com/docs/guide/events/ingesting-usage)); price-version snapshots on every re-rate; checkpointed/idempotent async jobs; explicit threshold-crossing semantics for progressive billing ([progressive billing](https://getlago.com/docs/guide/plans/progressive-billing)); full immutable revision history for every invoice.
