# OGS workspace docs

The documents the Ledger application reads at runtime, and the research they
came from. Application code lives in the `ledger` repo (beside this one in a
pair checkout); this repo is what the product and its reviewers quote.

| Path | What it is |
|---|---|
| `research/FEATURE-SPECS.md` | The workstream panel's source. The Ledger Dashboard parses the WS3 row **live**, so the counts in it are read by the running product, not by a human only. |
| `research/*/` | The research packs behind the architecture decisions (variants, engines, adoption decisions). |
| `audit/` | Dated audits of the checkout, kept as records. |
| `handoffs/` | Handoffs that are still cited as a source of truth, plus a [retired table](handoffs/README.md) for the ones that were superseded. |

## The gate: `feature-specs-freshness`

[`.github/workflows/feature-specs-freshness.yml`](.github/workflows/feature-specs-freshness.yml)
fails when `research/FEATURE-SPECS.md` disagrees with the golden fixture in the
`ledger` repo (`fixtures/golden-contract-01/golden_contract.json`). The fixture
defines how many worked revrec examples exist; a sentence here that counts them
is a claim about that fixture, and a seventh section makes today's "six" false
without anyone touching this repo.

The job does not re-implement the check. It checks this repo out at `docs/`,
clones `ledger/` beside it (read-only deploy key, ref from the `LEDGER_REF`
repository variable, `erp-gl-posting` by default), and runs the ledger's own
`scripts/check_worked_example_counts.py` — the same CLI the Ledger repo's CI and
its nightly alarm run — so a green run here cannot mean something else than a
green run there. Findings name the file and line **in this repo**:

```
../docs/research/FEATURE-SPECS.md:24: says 'seven sections' but the golden fixture pins 6 worked example(s)
```

Run it locally from the pair checkout, where `docs/` and `ledger/` are siblings:

```bash
rm -f ledger/docs/workspace/FEATURE-SPECS.md   # judge the live file, not the ledger-side pin
python ledger/scripts/check_worked_example_counts.py --root ledger
```

Repair a red run by restating the count **from the fixture** — never by editing
the fixture to match the sentence, and never by silencing the lint. A line that
records what was true on a past date takes the marker
`worked-example-count: allow` with that date; live prose does not.

If the job fails at *"Missing deploy key"*, the check never ran: add the private
half of the key as the `LEDGER_DEPLOY_KEY` repository secret and the public half
to the `ledger` repo as a read-only deploy key. A job that cannot read the
fixture is red on purpose, so an unreadable fixture can never page as fresh.
