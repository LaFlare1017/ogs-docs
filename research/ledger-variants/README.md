# R-1b Evidence Pack — Variant folders (decision evidence)

Date: Oct 18 2026. Sources: full `diff -rq` of ledger-recon vs ledger (1310 differing/only-in entries; 62 files differ; unique-module lists extracted), plus direct inspection of ledger-defense-flow.

## What each variant contains

### ledger-recon — a DIVERGENT, richer branch (not a duplicate)

- 156 test files (vs 117 in ledger/); ~56,216 LOC under `app/`.
- **Unique to recon (absent from ledger/):** `app/hierarchy*.py` (hierarchy/store/proposals/backfill), `funding_holds.py`, `funding_gate.py`, `funding_status.py`, `line_funding.py`, `app/engine/revrec/` + `revrec_store/loader/proposals/extract` — i.e. **WS2 (CLIN hierarchy + funding) and WS3 (ASC 606) work already exists in code**, plus `ledger_journal`, `journal.py` API, `clause_library.py`, `pds_import.py`, `docling_ingest.py`, `langextract_agent.py`, `selector.py`, `standards/`, `decisions_zen/`, `golden_cases.py`, `engine/money.py`, revrec/hierarchy funding sections in the evidence package, and dedicated famework (`funding_approval.py`, `hierarchy_synth.py`, `revrec_synth.py` test helpers); routes `ContractRevenue`, `EvidenceExceptions`, `CitationQuote`.
- **Unique to ledger/ (absent from recon):** `app/erp/` module, `api/erp.py`, `ErpPosting` route + `test_erp_posting.py`.
- 62 shared files differ (config, ci.yml, Dockerfile, README, alerts, several api modules…).
- **No `.git`** — deleting it is unrecoverable.

### ledger-defense-flow — a different project, not a ledger fork

Lovable-connected **TanStack Start/React + Supabase** frontend prototype (bun, Playwright visual tests, Radix UI stack). `AGENTS.md` warns to avoid rewriting published git history (history lives on Lovable's side). Its git-ness is external to OGS.

## Decision required (LG)

| Option | ledger-recon | ledger-defense-flow | Implication |
|---|---|---|---|
| **A. Archive only (RECOMMENDED)** | zip → `archive/ledger-recon-…zip` + keep folder until Stage I merge decision; do NOT delete | zip after repôle capture; mark "Lovable-connected prototype" | Zero data loss; workspace decluttered; recon stays readable for Stage I porting |
| **B. Merge-then-archive** | Port recon's hierarchy/funding/revrec/journal modules into `ledger/` behind flags with tests, then archive del | n/a | Highest value but violates the "no implementation before Stage R consolidation approved" gate — becomes **Stage I item 0** after LG approval |
| **C. Delete** | ✗ destroys plausibly real WS2/WS3 work with no git history — worst option | ✗ unrecoverable locally | Not recommended |

## Standing recommendation

Treat **ledger-recon as an internal study target feeding Stage R/I** (its modules are part of the "reverse-engineered work" this project wanted): R-1 evidence = the diff above; final disposition after LG picks A or B.
