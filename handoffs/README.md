# Handoffs — what lives here, and what used to

The root of `OGS/` accumulated nine handoff and plan documents between July and
September 2026. This folder keeps the ones a current checkout still reads; the
rest were retired on 2026-10-21 because a live document had superseded each of
them. Nothing here is generated and nothing here is mirrored into `ledger/` or
`ledger-recon/` — the citations point back at this folder.

## Kept: still the source of truth

| Document | Why it is still authoritative |
|---|---|
| [LEDGER_HANDOFF_ingestion_reconciliation_traces.md](LEDGER_HANDOFF_ingestion_reconciliation_traces.md) (`6b966acb…`, 46,787 bytes) | The handoff for the evidence layer (event ingestion, decision layer, reconciliation package, agent traces). Three tracked documents cite it as the spec whose **invariants win**: `ledger/docs/handoff-build-spec.md` ("if this spec and the handoff disagree, the handoff's invariants win"), `ledger/docs/handoff-mapping.md`, and `ledger/docs/handoff-phase0-report.md`. Its §12 decisions are still LG-reserved and open (`ledger/docs/LG-MANUAL-TODO.md`), so it is not history yet. |

## Retired: superseded, deleted 2026-10-21

Recorded so a reader who finds a citation to one of these knows where the
knowledge went, and can prove what was removed (sha256 of the deleted file).
None was under version control, so these hashes are the record.

| Deleted root file | sha256 | Why it was retired | Where the live version is |
|---|---|---|---|
| `LEDGER-COPILOT-HANDOFF.md` | `f92c768cad4a6153ad8ac9fed4be65c75fc7cf544a6cc3a6d0d02e7ca9f3acb3` | First Copilot build spec; superseded twice. | `ledger/LEDGER-COPILOT-HANDOFF-LOOP.md`, `ledger/LEDGER-COPILOT-HANDOFF-v1.2.md` (both newer and larger, tracked) |
| `LEDGER-COPILOT-HANDOFF-LOOP.md` | `3d0e0592658321cf68ca9754b38b7393cc2c90b05486f38454e26f84415fc99b` | Earlier draft of the V0–V5 loop spec. | `ledger/LEDGER-COPILOT-HANDOFF-LOOP.md` — same headings plus the V0–V5 retrospective |
| `LEDGER-COPILOT-HANDOFF-v1.2.md` | `907e72965050200fac4215f3091cfa2527f309fb9de286a274bdc8bb7614a61d` | Earlier draft of the V1.2 delta. | `ledger/LEDGER-COPILOT-HANDOFF-v1.2.md` — same headings, with §5.10 pointing at the V6 design spec |
| `LEDGER-HANDOFF.md` | `5216707742d955cfd6d3876d8b6a104771f668093568397dbb68bac17d633e8f` | Architecture + R1–R8 roadmap handoff, status 2026-09-09 ("R8 in progress"). Every section it carries has a live home. | `ledger/README.md` (architecture, invariants, run-locally, roadmap) and `ledger/LEDGER-ROADMAP-REA-PLAN.md` (the dated decisions ledger) |
| `LEDGER-SF1408-READINESS-HANDOFF.md` | `d14cf6cf1fa0cea6783051e3dc634192b919eaf9ac773ee085d67bf86ba1aa0f` | Build spec for a surface that is built: the 10-criterion DCAA pre-award scorecard and its evidence-packet export exist in code. | `ledger/app/readiness/`, `ledger/README.md` ("…readiness scorecard are **built**") |
| `SESSION-HANDOFF-2026-07-19.md` | `929e2b3385f89c1420d95e0393435282c1b90f395643ee8ec258c0d75065173f` | One session's handoff from July; the journal is 145KB deep since. | `ledger/docs/SESSION-HANDOFF.md` (current journal), `ledger-recon/docs/SESSION-HANDOFF.md` (its mirror journal) |
| `ledger-build-plan.md` | `0f3cafe41fcbb792216c5192141881344a87e4b28ee61d95ec2acca7638f403c` | The July "weekend MVP" plan for a product that has been in production for months. | `ledger/README.md` |
| `ledger-build-plan 2.md` | `ed0d24814eead5a61b06755c80d8708a5718a329a2e0b4cb7cac19313f74ea85` | Same plan, taller revision (the ` 2` copy was never merged back). | `ledger/README.md` |

One dangling citation survives on purpose: the four tracked Copilot specs
(`ledger/LEDGER-COPILOT-HANDOFF-*.md` and their `ledger-recon/` mirrors) still
tell a reader to "read `LEDGER-HANDOFF.md` for style". Those specs are
themselves historical (V0–V5 shipped, and their own newer copies supersede
them), so the instruction was left alone rather than rewritten to point at a
document it never described — the live equivalent is `ledger/README.md`.

The root copies were compared with the tracked ones before deletion: the tracked
Copilot specs carry the same section list plus the later additions, so no unique
material was lost there. The rest were checked against `ledger/README.md`'s
architecture, roadmap and readiness sections.

## Left in place (not handoffs or plans)

`compass_artifact_wf-…_text_markdown.md` (open-source research; cited by
`ledger-recon/docs/plan-hierarchy-revrec.md` as "also at the OGS root"),
`LEDGER-PROGRESS-REPORT.html` (a report), `fresh-eyes-review-prompt.md` (a
process prompt) and `archive/` were out of scope for this pass and still sit at
the root.

## The rule for the next one

A handoff stays at the root only while a current, tracked document cites it as a
source of truth (a spec whose invariants win, a decisions ledger, a design spec
for work not yet built). Once every citation is historical and the shipped
equivalent lives in `ledger/` or `ledger-recon/`, the handoff is either moved
here — with its citations updated — or retired into the table above.
