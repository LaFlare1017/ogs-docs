# R-1 Evidence Pack — Ledger itself (owned software; full depth)

Method: read-only SQLite snapshot analysis + source-tree inventory + rea CLI probe results. Date: Oct 18 2026. No writes to any product code or live DB files (snapshot made via Python `backup()` API of `ledger/ledger.db` → `/tmp/ledger-snapshot.db`).

## Schema (from /tmp/ledger-snapshot.db — 82 tables)

Raw dump: `/tmp/r1-schema.json`. Highlights beyond the build-plan's 6-table MVP:

- **Roadmap features already have schema presence:** `clin_funding_config` (WS2), `contract_modifications` (WS2/WS3), `cost_pools` + `indirect_rates` + `unallowable_categories` (Layer-3/DCAA), `recon_runs` / `recon_packages` / `recon_tieouts` / `variances` / `exception_events` / `period_closes` (WS1), `erp_import_batches` / `erp_invoice_lines` / `erp_pushes` / `gl_entries` (parked ERP track), `wallet`-adjacent: none (no wallet table — prepaid/drawdown NOT present in ledger/), `payment_applications`, `remittance_rows`, `rating_runs` + `line_items` + `usage_events` (core), `agent_calls`/`agent_trace_steps` (audited model usage).
- Demo-data state: mostly empty except `agent_calls`(18), `agent_trace_steps`(1), `classification_questions`(6), `event_type_vocabulary`(10), `unallowable_categories`(8), `engine_projection_map`(3).

## Frontend routes (source inventory — rea JS analysis timed out, see limitations)

`ledger/frontend/src/routes/` includes: Dashboard, Billing, ClauseReview, ContractOverview, ContractWebhook (with dashboard evidence activities), EventsScreen, Evidence* (Package, Exceptions, ReviewQueue, Sources, Precedents, PacketExport), AuditTrail, AutoApprovalPolicy, Asc606Memo, CostPoolSetup, **ErpPosting** (ledger/ only), EventClassification, Reports* (Funding, Leakage, Revenue, Trust, Receivables, Credits), UserManagement, …

## Backend API modules

account, api_keys, audit, audit_pack, auth, auto_approval, billing, close, contracts, **erp**, events, evidence_* (decisions/ingest/lists/package/recon/traces), invoices, memos, notifications, payments, proposals, rehearsal, remittance, reports, rule_templates, users, vehicles, webhooks.

Engine: dsl, evidence, invoice, invoice_pdf, rating, recognition.

## Invariant checks (Phase-0-style, source-level — full sweep still pending in Stage I)

Floats-in-money-path sweep, UPDATE/DELETE triggers, wall-clock-in-output sweep: **not yet run** — queued as the first Stage I / Phase-0 task against `ledger/` (and later vs merged recon modules). No violations claimed found or clear at this point.

## rea runtime findings (limitations recorded)

- `doctor`: node 22.23.1 + host healthy; native engines (Hopper/Ghidra/IDA/JADX) absent — irrelevant to our targets (no binary RE).
- `inspect-sqlite-database`: **capability_unavailable** — rea requires Node ≥24.15 (native `DatabaseSync.limits` API); local Node is 22.23.1. Evidence produced via read-only Python snapshot instead; re-install Node 24+/26 later if rea-native SQLite evidence records are wanted.
- `analyze-javascript-application`: integrity mismatch on OneDrive-synced `app/static` ("directory entry changed during read"), then a 300s timeout on the huge hashed-asset tree (hundreds of overlapping build chunks). Mitigation options for later: analyze a fresh clean build output, or `--integrity-policy record-and-continue`, or point rea at a copied tree outside OneDrive.
- Not exercised: website/HAR/process-capture modes on targets (web targets were studied by the research agent at documentation level instead; process capture reserved for demo-runtime studies in Stage I).
