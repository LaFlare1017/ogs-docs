# Audit — ledger-recon's 15 uncommitted files (pre-Stage-I disposition)

Date: Oct 18 2026 · HEAD `e060e08` (branch `phase-1-hierarchy`, local only, no remote)

## What the "15 files" actually are

### 6 modified files = noise, not work
Five are **permission-mode-only changes** (100644→100755; the git index picked up OneDrive's on-disk permission rewriting; zero content diff): `evals/golden/tier_cap_oos.json`, `CopilotAwarenessChip.tsx`, `auditSummary.test.ts`, `test_events_and_history.py`, `test_review_endpoint.py`. The sixth (`frontend/src/components/ExplainableAmount.tsx`) is **one word in a doc comment** ("brass" → "accent"); no behavior change.
Disposition: **do not port, do not commit as-is.** In `ledger/` the port target, mode churn is not a thing to replicate. Recon's index can be normalized later if it ever gets committed; harmless to ignore for porting.

### 9 untracked files = real, coherent, finished-looking work in two clusters

**Cluster 1 — OneDrive-git safety tooling (6 files).** Born from a real incident: a `git merge --ff-only` in `OGS/ledger` hung because OneDrive evicted `.git/ORIG_HEAD` (and separately evicted 33 loose objects while their directory entries still looked healthy to `stat()` and even `fsck --connectivity-only`).
- `scripts/preflight_merge.py` — pre-merge preflight that catches OneDrive eviction *before* a merge hangs (docstring documents the invisible-to-obvious-checks failure mode)
- `scripts/hydrate_object_store.py` — recovers/rehydrates a partly-evicted object store, gates repack
- `scripts/install_git_hook.sh` + `scripts/hooks/{pre-merge-commit,pre-push,post-merge,_common.sh}` — wire the preflight into git workflow
- `tests/test_preflight_merge.py` (895 lines) + `tests/test_hydrate_object_store.py` (293 lines)
Finished look: complete docstrings narrating why, defensive scripting, 1,188 lines of dedicated tests; **39+ tests observed green** in a bounded run (-suite was slow across OneDrive hydration; zero failure markers).
Disposition: **APPLICABLE TO `ledger/` AS-IS.** These guard `ledger/`'s own repository on this machine — arguably the most operationally valuable files in the whole recon tree. Recommend slotting them in as Stage I item 0-prep, directly from `archive/ledger-recon-oct18-2026.zip` or the live folder, with their tests.

**Cluster 2 — UI style guide (3 files).** `frontend/src/routes/StyleGuide.tsx` (554 lines) + `StyleGuide.css` (306) + `components/ThemeToggle.css` (27; relates to the one-word "accent" wording edit in `ExplainableAmount.tsx`).
Finished look: complete route-level component plus stylesheet. **Not yet wired:** no non-test import of `StyleGuide` exists in frontend source, and no `ThemeToggle.tsx` component file exists — so the route is dangling (probably pending a nav entry and the theme-toggle component it was built for).
Disposition: **do not port blind.** It's recon-side WIP-in-progress-looking-but-incomplete wiring. If `ledger/`'s UI redesign wants a styleguide route later, port as its own scoped task — not inside the module port.

## Verdict for the Stage I port

- Port blockers: none. The uncommitted state does **not** need to be resolved before porting, because the port takes **specific modules** (money/hierarchy/funding/revrec/journal), all of which are **committed** at `e060e08`.
- Do adopt as 0-prep: the OneDrive safety tooling (cluster 1) — protects the merges the port itself will need.
- Do ignore for now: the 5 mode-only changes + 1-word edit (recon-local churn).
- Park: the style-guide trio — incomplete wiring; optional future UI task.
- Housekeeping note for later: if recon is ever finalized (committed/tagged), add the mode-chaos to .gitattributes (`* -text` considerations aside, `core.fileMode=false` on cloud-synced checkouts) before any commit so this noise stops accumulating.

## Resolution note — mode churn silenced (applied 2026-10-18, same day)

The housekeeping note above was applied **while recon is still unfinalized** (it does not
need to wait for a commit, and waiting let the noise keep accruing). Root cause confirmed
before acting: OneDrive rewrote on-disk exec bits, so git's index kept flipping 100644→100755
→100644 on files with no content change — the five mode-only ` M` entries. `extensions.worktreeConfig`
was already enabled in this repo's `git config`, so the fix could be scoped to the recon worktree
alone without touching the `ledger/` checkout:

- `git config extensions.worktreeConfig true` — already enabled; no action needed
- `git -C ledger-recon config --worktree core.fileMode false` — recon worktree's local config only

Result, observed immediately after applying: recon's `git status` went from **9 M → 4 M** — the five
mode-only entries (`evals/golden/tier_cap_oos.json`, `CopilotAwarenessChip.tsx`, `auditSummary.test.ts`,
`test_events_and_history.py`, `test_review_endpoint.py`) disappeared, leaving only the real deltas
(`ci.yml`, `README.md`, `ExplainableAmount.tsx`, `test_alerts.py`). **`ledger/` untouched**: its
`core.fileMode` override is empty (defaults to `true`) and its status is unchanged (8 M / 11 ??).
Sticky caveat: the setting shrinks the noise signal, it does not repair an index that has already
absorbed bogus mode flips — those are moot now since the five files' entries already agree with the
new default and stopped reporting.

**Correction on final re-check (same day):** the post-apply status reads **5 M / 11 ??**, not
4 M — the fifth M is `docs/SESSION-HANDOFF.md`, which this very documentation step turned
content-modified (the rationale block added to recon's handoff doc is a real edit, not churn).
So the five mode-only entries are confirmed gone; the remaining M set is exactly
{`ci.yml`, `README.md`, `docs/SESSION-HANDOFF.md`, `ExplainableAmount.tsx`, `test_alerts.py`} —
all content deltas. The fix behaved precisely as intended: `core.fileMode=false` is live in
recon's worktree config, ledger's checkout kept the repo-wide default (`true`), and the
phantom-status noise source is extinguished.