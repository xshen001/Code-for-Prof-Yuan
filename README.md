# Code for Professor Yuan

This repository contains Codex-handoff packages for independent execution.

## Packages

1. `causal_interval_transfer_for_professor_yuan_20260702_evaluator_seeded_final_codex.zip`

   Corrected evaluator-seeded causal interval-transfer package. The package includes `plan.md`; Professor Yuan's Codex/Cursor agent should read it and run the standard command.

2. `final_hidden_resampling_matbench_only_for_professor_yuan_20260701_dependency_fix_codex.zip`

   Corrected Matbench-only hidden-resampling package. The package includes `plan.md`; Professor Yuan's Codex/Cursor agent should read it and run the standard command.

3. `final_hidden_resampling_matbench_with_expert_reference_for_professor_yuan_20260703.zip`

   Corrected Matbench hidden-resampling package with the missing same-split expert-reference comparator and a self-contained portable Python setup. This is the package to use for the Matbench rerun.

## SHA-256 Checksums

```text
990ccec880b443c623e3ee253ecb8709c98f238a8af2e19bb3c4cdf35ae0def1  causal_interval_transfer_for_professor_yuan_20260702_evaluator_seeded_final_codex.zip
b79ea73942e9ba1b660e63455ff363376699613a21b1cdc63b5c2b750e50da8f  final_hidden_resampling_matbench_only_for_professor_yuan_20260701_dependency_fix_codex.zip
0727b5d1a305747b59e5898f3c413d193221d7ae2aa069dbdc8eea752d906cac  final_hidden_resampling_matbench_with_expert_reference_for_professor_yuan_20260703.zip
```

## Return Files

After running each package, please return the generated nonzero-size result zip:

- Causal: `causal_interval_transfer_standard_*_return_to_authors.zip`
- Matbench expert-reference rerun: `matbench_expert_reference_hidden_resampling_standard_*_return_to_authors.zip`
