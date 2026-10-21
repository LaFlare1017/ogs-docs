# R-3 Evidence Pack — Apache/MIT-licensed billing engines (license-verified)

Targets: killbill/killbill · openmeterio/openmeter · dev-formata-io/limitr · hanzoai/commerce
Method: LICENSE-file verification + public docs/READMEs, Oct 18 2026. "License-verified" = the repo's own license file says Apache-2.0 or MIT; vendored/dependency terms still get checked before any actual code reuse (we currently take **designs only**). Code from these repos *may* be reused with attribution if we later choose.

## Kill Bill — Apache-2.0 (LICENSE verified)

- Account → Subscription Bundle → Subscription; catalog = products, plans, **plan phases**, price lists, billing periods, rules; phases support trial/discount/fixed-term/evergreen with optional usage sections. No general nested-contract hierarchy. [Subscription guide](https://docs.killbill.io/latest/userguide_subscription)
- Funding: account credits, child→parent credit transfer, **parent-payer delegation with summary invoices** — payer structures, not usage budgets/contract ceilings. [Hierarchical accounts](https://docs.killbill.io/latest/ha)
- Corrections: distinct primitives — account credit / draft-invoice credit / **invoice-item adjustment** / refund; plan-change rules with proration. [Invoice examples](https://docs.killbill.io/latest/invoice_examples)
- Traceability: invoice items carry plan/phase/usage/service dates/rates; billing events recomputed against persisted items; effective-dated catalogs. [Invoice subsystem](https://docs.killbill.io/latest/invoice_subsystem)
- Ingestion: core usage API takes rolled-up usage; external-key idempotency documented; raw-event metering (Aviate) is **premium**, not surveyed core. [API guide](https://killbill.github.io/slate/)
- Mazes: "Catalog as executable contract policy", "payer hierarchy without collapsing accounts", "separate correction primitives".

## OpenMeter — Apache-2.0 (LICENSE verified; `openmeterio/openmeter` — the `meter` URL 404s)

- Versioned plans, features, add-ons; flat/recurring/per-unit/tiered/package/dynamic prices; customer-specific plans. [README](https://github.com/openmeterio/openmeter)
- Entitlement notifications with % or numeric thresholds (80% warn, 100% block-trigger, 200% soft-limit examples); the event **informs** enforcement, it doesn't enforce by itself. [Entitlement events](https://openmeter.io/docs/integrations/notifications/entitlements)
- Meters aggregate CloudEvents; event→meter→usage→plan trail; invoice-correction semantics **not verified** — open item.
- Mazes: separation of measurement from commercial policy; entitlements as control plane; customer-specific plans.

## Limitr — Apache-2.0 (license verified) — policy engine, not a billing system

- Plans/entitlements/limits/prices/costs/credits in **one versionable policy document**; per-customer deals with expiry; caps, org budgets, prepaid credits, top-ups, resets, rollover; threshold + limit-hit events. [README](https://github.com/dev-formata-io/limitr)
- Preflight **authorization/reservation** before work executes; reserve predicted capacity, reconcile after.
- Mazes: policy-as-vcs-artifact; pre-execution budget checks; bounded policy overlays for negotiated exceptions.

## Hanzo Commerce — MIT OR Apache-2.0 (dual license verified; vendored terms in NOTICE not yet reviewed)

- Multi-tenant service: products/plans/orders/subscriptions/invoices/usage metering + claimed credit ledger; one SQLite file per org; payment cards split into a vault service. Details thin — secondary reference only. [README](https://github.com/hanzoai/commerce)

## Excluded (license traps)

- **Lago = AGPLv3** (concepts-only track; see ../lago-agpl/). Its separate SDK/MCP repos are MIT — that does not MIT license the platform. [getlago/lago](https://github.com/getlago/lago)
- **Flexprice** = open-core, core AGPLv3. [Flexprice](https://github.com/flexprice/flexprice)

## Cross-project synthesis → WS2/WS1/WS4

1. Separate legal hierarchy from billing mechanics (Kill Bill payer model is a reference, not enough — we need an explicit contract tree + allocation).
2. Persist the **exact price/rule version used per charge** (effective-dated catalogs everywhere; none proves a complete event→price-revision audit chain — our differentiator).
3. Funding = a **ledger** (committed/granted/consumed/adjustments/expiry/remaining) + thresholds + explicit enforcement mode (observe/notify/soft/hard). OpenMeter thresholds + Limitr preflight are ingredients, not the whole.
4. Corrections first-class with distinct financial outcomes (Kill Bill's four primitives), originals preserved, corrections linked.
5. Idempotency contracts documented per source (Kill Bill external keys; OpenMeter event IDs unverified — verify before adopting).
