# Return Files

Generated result zips from running the packaged tasks on the evaluator machine.

Note: `final_hidden_resampling_matbench_with_expert_reference_...` (package 3) is the
authors' corrected Matbench rerun and supersedes the earlier
`matbench_only_hidden_resampling_...` result (package 2, kept here for the record).
Package 3 folds in the same `pymatgen==2023.12.18` fix we applied manually to package 2,
so it ran unmodified with no manual change needed.

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

## matbench_expert_reference_hidden_resampling_standard_standard_20260703_110547_utc_return_to_authors.zip

- Task: Matbench hidden-resampling **with expert-reference comparator** (package 3, the authors' corrected Matbench rerun). This is the current Matbench result; it supersedes the package-2 result above.
- Run: completed cleanly; master seed generated on the evaluator machine (`master_seed_source: machine_secret`), so the authors did not know it before this zip. Two phases: hidden-resampling evaluation (~16 h) then official-fold expert calibration (~2.5 h), CPU-only.
- Config as packaged: tasks `matbench_dielectric, matbench_jdft2d, matbench_log_gvrh, matbench_log_kvrh`; split ids `0,1,2,3,4`; target budget 24; CHGNet/foundation budget 15; validation repeats 3; held-out test fraction 0.20; expert-reference candidate `reg_extra_ll_structure_matminer_wide`; official calibration folds `0,1,2,3,4`. 100 arm-by-task-split records (five arms).
- Primary result (`matbench_hidden_resampling/summary.md`): source-informed self-improving utility -0.1930 (0.0072) vs target-only self-improving -0.1995 (0.0076), gain 0.0065 (0.0019), win rate 0.850; vs the declared expert-reference arm -0.3100 (0.0474), gain 0.1170 (0.0449), win rate 1.000.
- Calibration (`expert_fold_calibration_summary.csv`): the same expert rule scores mean MAE 23.26 on the secret resamples vs 24.92 on Matbench official folds (mean absolute relative difference ~5.3%), indicating the secret resamples are of comparable, slightly easier difficulty than the official folds.
- Contents include both `matbench_hidden_resampling/` and `matbench_official_fold_expert_reference/` result sets, `expert_fold_calibration.csv` (+ summary), `final_manifest.json` (+ sha256), and run logs.
- Environment: run **unmodified** as packaged. The package now pins `pymatgen==2023.12.18` itself and its setup runs a Magpie-featurizer preflight, so the package-2 fix was not needed manually this time. The official-fold logs contain benign `spglib` symmetry warnings (fallback for a few structures); they do not affect the run.
- SHA-256: `faa990ac2d6f2c372460645bca3e652deb4b0a4ff4d1d0228c1d1ab8362bf6e6`
