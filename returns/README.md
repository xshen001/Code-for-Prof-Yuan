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

## tabarena_prediction_level_type_aware_gated_20260708_032421_utc_return_to_authors.zip

- Task: TabArena prediction-level **type-aware gated-transfer** rerun (package 4, `..._20260707_gated`). This is the current TabArena result; it supersedes the earlier manually-patched `tabarena_prediction_level_strong_transfer_20260707_..._DIAGNOSTIC_PATCHED_return.zip` at the repo root, which was run on the previous (20260706) package.
- Run: completed cleanly (exit 0, status `success`), ~24 min wall on the evaluator machine (2026-07-08 03:24:21 → 03:48:23 UTC). The `math domain error` in `source_order_similarity_weighted` that stopped the previous package is fixed in this one: for a target with the `n_classes = -1` regression sentinel the term is now skipped (`if target_val < 0: continue`), so `math.log1p(-1)` is never reached. It ran end to end with no manual patch.
- Independence: the evaluation is deterministic given the frozen prediction artifacts, and the three analyses use the packaged fixed bootstrap seeds (`20260706`, `20260707`), not machine-secret seeds. Independence here is that the frozen pipeline was executed on the evaluator machine, not secret seeding.
- Config as packaged: TabArena context `neurips2025`; candidate families `AutoGluon_v130, CatBoost, LightGBM, XGBoost, RealMLP, TabPFNv2_GPU`; declared expert reference `AutoGluon_v130_bq_4h8c`; type-restricted, similarity-weighted source transfer with a larger regression source pool, prediction-level ensembling, and a target-validation acceptance gate; 51 datasets, 816 task-folds, seven arms. Frozen config hash `3ff1722b43852eec`.
- Primary result (`type_stratified_analysis/gated_source_final_summary.md`): type-balanced dataset-rank gain of the validation-gated source-informed arm vs target-only self-improving = 1.318 [95% CI 0.777, 1.879], one-sided 90% lower bound 0.950, paired sign-flip one-sided p = 0.00016; vs the declared expert reference = 0.999 [95% CI 0.157, 1.776], one-sided 90% lower bound 0.465, one-sided p = 0.0134. The gated arm beats the expert reference within every problem type (binary +0.55, multiclass +1.56, regression +0.88).
- Internal Elo (`internal_elo_analysis`, dataset unit, expert pinned to 1000): gated arm 1098.6 (+98.6 vs expert); dataset-level bootstrap contrast +99.5 Elo [−2.6, +200.7], win rate 0.588 [0.451, 0.725]. The dataset-level Elo interval still just spans zero at n = 51, while the prediction-level and primary rank contrasts clear zero.
- Contents (32 files, none empty): `prediction_level_summary.{csv,md}`, `prediction_level_selections.csv`, `prediction_level_weights.csv`, `prediction_level_bootstrap_ci.csv`, `prediction_level_dataset_bootstrap_ci.csv`, `internal_elo_analysis/`, `type_stratified_analysis/`, `config_frozen.json`, `manifest.json`, `task_summary.json`, `evaluator_run_status.json`, `run_terminal.log`, and the submitted config and scripts.
- Environment: run as packaged (Python 3.11, AutoGluon prerelease `1.5.1b20260626`). `TABARENA_CACHE`/`TABARENA_SRC` were pointed at the ~38 GB TabArena prediction cache and the pinned source (`6624d55`) already downloaded for the 20260706 run, so no 40 GB re-download; no code, config, candidate, seed, or requirement changes.
- SHA-256: `0b66d296a89e0d6b8afbe2b114b4eb7d3efc84a0e0cd74cdf55ad8b4a79d5951`
