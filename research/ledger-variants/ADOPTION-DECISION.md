# Decision Memo — Adopt ledger-recon's hierarchy/funding/revrec modules as Stage I item 0

Date: Oct 18 2026 · Status: RECOMMENDED-ADOPT (LG to confirm before Stage I code moves) · Evidence: ../ledger-variants/README.md, ../ledger-app/README.md, this memo's audit below.

## Verdict

**Adopt — port `ledger-recon`'s hierarchy, funding, revrec, and journal modules into `ledger/` as Stage I item 0**, behind feature flags, module-by-module with their tests, after an invariant sweep confirms each imported module meets the seven invariants in its new home. Rationale: recon already implements the Oct-17 roadmap's WS2/WS3 core (contract tree roll-ups, CLIN funding holds/gates/status, revrec engine with modifications/allocate/schedule/loss/trace methods, frozen journal) with real test coverage — 28 dedicated test files — and its most recent commits encode LG's Oct-15 decisions (right-to-invoice, prospective modification treatment). Rebuilding this from scratch (WS2/WS3 as fresh Stage I work) would re-earn months of decisions recon already settled.

## Audit evidence justifying the verdict

- **Money kernel** (`app/engine/money.py`): dedicated Decimal kernel — rejects anything not Decimal at the boundary, one rounding rule (`half_up_cents`), one remainder rule (`largest_remainder`, exact Fractions). Invariant 2 honored by construction.
- **Roll-ups computed, never stored** (`app/hierarchy.py` docstring): recursive contract tree + item tree in SQL, sums in Python Decimal — "computed, never stored" matches invariant 3/5 discipline.
- **Immutability triggers** (`app/db.py`): `BEFORE UPDATE/DELETE` triggers RAISE on `usage_events` and `time_entries`; committed cash history protected in the journal layer. Invariant 3 native.
- **Funding holds** (`funding_holds.py`): own short transaction, versioned, resolved only on re-evaluated facts; three reason codes (funded amount / estimated cost / ceiling price).
- **revrec engine** (`app/engine/revrec/`): allocate, methods, modifications, price, schedule, loss, journal, trace, errors — a real module structure, not a stub.
- **Tests**: test_hierarchy (incl. acceptance + properties), test_funding_gate/status, test_revrec_* (engine, modifications, proposals, store, properties, loader), test_ledger_journal (+frozen), test_clin(_extraction) — property tests present, matching the plan's testing standard.

## Open risks to resolve during the port (name-checked, not blockers)

1. `float()` appears in 8 recon non-test sites, but all in evidence-decision confidence/calibration and copilot display paths — **none in the money path** by inspection; confirm during port that none feed amounts.
2. 62 shared files differ between recon and ledger (config/CI/Docker/api modules) — porting is per-module with tests, not a wholesale replace; ledger/'s `erp/` work (recon has none) must be preserved.
3. Recon working tree has uncommitted modifications (5 files) and 3 local commits (no remote). **Do not assume theworking tree state; reconcile those changes with LG before porting from a clean commit.** The archive zip (checksum in archive/MANIFEST.txt) preserves the exact as-found state either way.
4. Module import graph reaches `authoritative.py`, `display.py`, `funding_gate.py` — port dependencies together or stub first; gold contract fixtures (Phase 0 item 5) should gate the equivalence claim.

## Sequencing (Stage I item 0 once LG confirms)

0a. Baseline suite + invariant sweep on `ledger/`.
0b. Per-module port order: `engine/money.py` → `hierarchy_store`/`hierarchy*` → `funding_*`/`line_funding`/`clin_funding_config` → `engine/revrec/*` + `revrec_*` → `ledger_journal`. Each module: flag + tests green in `ledger/` + ADR in `ledger/docs/adr/`.
0c. Golden dataset (`fixtures/golden-contract-01/`) built against the merged app; hand-computed expectations independent of code.
Remaining Stage I order unchanged (WS0 first, then WS2/WS3/WS4 fat with the ported code, WS1 last in Phase 1).
