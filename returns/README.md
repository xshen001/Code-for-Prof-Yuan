# Return Files

Generated result zips from running the two packaged tasks on the evaluator machine.

## causal_interval_transfer_standard_20260702_171253_utc_return_to_authors.zip

- Task: evaluator-seeded causal interval-transfer standard evaluation.
- Run: completed cleanly (exit 0); seeds generated locally from OS entropy (`secrets.SystemRandom`), so the authors did not know them before this zip.
- Contents verified present: `summary.csv`, `contrasts.csv`, `manifest.json`, `evaluator_seed_manifest.json`, `hidden_records.csv`, `selected_rules.csv`.
- Environment: run as packaged, no code or requirement changes.
- SHA-256: `698b9820380391d141b8b9b3a54c961edabe005fb03e386385812e0c6e57fa03`

## matbench_only_hidden_resampling_standard_standard_20260702_194305_utc_return_to_authors.zip

- Task: Matbench-only hidden-resampling standard evaluation (curator/machine-seeded).
- Run: completed cleanly; master seed generated on the evaluator machine (`master_seed_source: machine_secret`), so the authors did not know it before this zip. Elapsed ~26,032 s (~7.2 h) of CPU-only compute.
- Config as packaged: tasks `matbench_dielectric, matbench_jdft2d, matbench_log_gvrh, matbench_log_kvrh`; split ids `0,1,2,3,4`; target budget 24; CHGNet/foundation budget 15; validation repeats 3; held-out test fraction 0.20. 80 arm-by-task-split records.
- Contents include `summary.csv`, `summary.md`, `by_task.csv`, `contrasts.csv`, `records.csv`, `archive.jsonl`, `hidden_split_manifest.csv`, `manifest.json`, `final_manifest.json` (+ sha256), and run logs.
- SHA-256: `b540b79c2dd3159430cc7c9a382df95acfee8bae5a0af807b6de7ce237267170`

### Environment note (Matbench only)

The packaged `SETUP_MATBENCH_ENV` resolves `pymatgen` to a 2024 build whose periodic
table adds deuterium and tritium as separate entries. That inflates the element
table to 120 rows, and matminer 0.7.4's Magpie featurizer then indexes past the
real element range and aborts with `ValueError: Unexpected atomic number Z=119`
before any model is fit. To let the run proceed we pinned `pymatgen==2023.12.18`
(the last release whose periodic table stops at the real elements); no experiment
code, scoring rule, candidate rules, seeds, or the requirements file were changed.
With that single pin the smoke check passed end to end and the standard run
completed as above.
