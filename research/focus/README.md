# R-2 Evidence Pack — FOCUS standard (public spec structure)

Target: focus.finops.org + official FinOps Foundation pages + github.com/FinOps-Open-Cost-and-Usage-Spec/FOCUS_Spec
Method: public pages read by a web research agent, Oct 18 2026. Every claim carries a source URL. No FOCUS text, branding, or name copied into Ledger deliverables — structural lessons only. Latest reviewed release: **v1.4** (June 2026).

## Column dictionary pattern

- Dataset-level tables list each column's display name, dimension/metric classification, feature level (Mandatory/Conditional/Recommended), and data type. The Cost and Usage dataset has **65 columns**. [Cost and Usage dataset](https://focus.finops.org/docs/specification/v1-4/datasets/cost-and-usage/) · [Billed Cost entry](https://focus.finops.org/docs/specification/v1-4/columns/cost-and-usage/billed-cost/)
- Per-column pages add: dataset, type, feature level, data type, introduction version, definition, requirements; nullability is specified **separately** from conditional presence. [Billed Cost entry](https://focus.finops.org/docs/specification/v1-4/columns/cost-and-usage/billed-cost/)
- `BilledCost`, `ContractedCost`, `EffectiveCost`, `ListCost` are mandatory decimal metrics denominated in Billing Currency. Unverified here: whether every entry carries a consistent "example" field. [Cost and Usage dataset](https://focus.finops.org/docs/specification/v1-4/datasets/cost-and-usage/)

## Schema highlights (grouped from the 65 columns; grouping is ours, not the spec's)

Account structure · billing period/invoice/currency · charge classification & timing (`ChargeCategory` ∈ Usage|Purchase|Tax|Credit|Adjustment) · cost/price/quantity · consumption · commitment/contract/reservation (incl. `ContractApplied` → `ContractCommitmentId` join to the Contract Commitment dataset) · provider/service · location/resource/SKU · allocation/tags. [Cost and Usage dataset](https://focus.finops.org/docs/specification/v1-4/datasets/cost-and-usage/) · [Charge Category entry](https://focus.finops.org/docs/specification/v1-4/columns/cost-and-usage/charge-category/)

## Versioning

"SemVer-inspired" per the spec repo — explicitly **not** a strict compatibility guarantee; additive-first releases with a formal deprecation cycle; implementers declare the version they align to. Ratified releases: 1.1 Nov 2024, 1.2 May 2025, 1.3 Dec 2025, 1.4 Jun 2026 — a history, not a guaranteed cadence. [FOCUS spec repo](https://github.com/FinOps-Open-Cost-and-Usage-Spec/FOCUS_Spec) · [What is FOCUS?](https://focus.finops.org/what-is-focus/)

## Conformance — the three-layer pattern (most valuable finding for WS5)

1. **Normative requirements** (RFC-2119 MUST/SHOULD; deviations formally assessed). [FOCUS spec repo](https://github.com/FinOps-Open-Cost-and-Usage-Spec/FOCUS_Spec)
2. **Automated data checks** — the hosted Validator reports pass/fail + row-level violations per spec version (1.2/1.3 supported on the pages reviewed; 1.4 planned). [FOCUS Validator](https://focus.finops.org/docs/tools-assets/tools/focus-validator/)
3. **Certification beyond automation** — Essential (one dataset) vs Full (two datasets + cross-integrity) tiers, 24-month window, and semantic/business checks that require **human attestation**, sample data, a public gap report, and a column-mapping reference. [v1.3 certification criteria](https://www.finops.org/certification-for-organizations/finops-certified-focus-v1-3-conformant/)

## Extensions/profiles

Custom columns are allowed under documented handling rules (extension without touching the standard column set); adjacent datasets (Contract Commitment in v1.3-era, two more in v1.4) extend coverage. **No formal profile mechanism verified.** [Cost and Usage dataset](https://focus.finops.org/docs/specification/v1-4/datasets/cost-and-usage/)

## Governance

Community-driven under Linux Foundation / Joint Development Foundation; Steering Committee ratifies releases; full amendment workflow unverified. [FOCUS spec repo](https://github.com/FinOps-Open-Cost-and-Usage-Spec/FOCUS_Spec) · [FOCUS 1.0 announcement](https://www.finops.org/insights/focus-1-0-available/)

## Transfer map → WS5 (Ledger Reconciliation Spec)

Adopt: three-layer conformance stack (schema-valid → check-passing → attested); required-presence vs nullability separated; version declaration + explicit breaking-change/deprecation definitions; public deviation/gap disclosure.
Avoid: inferring "structure-valid = reconciliation correct"; leaning on "SemVer-inspired" as a contract; assuming profiles exist; importing FOCUS's domain wholesale (it is cloud-cost billing, not contract reconciliation).

## Unverified items (carry forward)

Exact amendment/voting procedure; formal profile mechanism; guaranteed cadence; complete breaking-change definition; `ContractedCost` entry-level semantics; v1.4 Validator availability.
