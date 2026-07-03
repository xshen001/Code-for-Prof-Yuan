# Return Files

Generated result zips from running the packaged tasks on the evaluator machine.

## causal_interval_transfer_standard_20260702_171253_utc_return_to_authors.zip

- Task: evaluator-seeded causal interval-transfer standard evaluation.
- Run: completed cleanly (exit 0); seeds generated locally from OS entropy (`secrets.SystemRandom`), so the authors did not know them before this zip.
- Contents verified present: `summary.csv`, `contrasts.csv`, `manifest.json`, `evaluator_seed_manifest.json`, `hidden_records.csv`, `selected_rules.csv`.
- Environment: run as packaged, no code or requirement changes.
- SHA-256: `698b9820380391d141b8b9b3a54c961edabe005fb03e386385812e0c6e57fa03`

The Matbench-only return zip will be added in a later commit once that run completes.
