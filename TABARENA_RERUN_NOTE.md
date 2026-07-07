# TabArena strong-transfer rerun — independent run notes

Xiaotong,

I ran the final safeguarded strong-transfer package
(`tabarena_prediction_level_for_professor_yuan_20260706`) on my machine. Setup
was clean and the full ~40 GB of processed TabArena prediction artifacts
downloaded and cached without trouble. The run then stopped the moment it
entered the experiment, on what looks like a deterministic bug rather than
anything environmental.

## The crash

It fails in `source_order_similarity_weighted`
(`scripts/run_tabarena_prediction_level_experiment.py`) at the size-feature
distance:

```
term.loc[valid] = np.abs(np.log1p(source_val.loc[valid]) - math.log1p(target_val))
ValueError: math domain error
```

The loop runs over `("max_train_rows", "n_features", "n_classes")`. For a
regression target, `n_classes` carries the −1 sentinel, so `math.log1p(-1)` =
log(0) raises the domain error. The tell is the asymmetry between the two sides:
the source vector uses `np.log1p` (which just returns −inf, no exception), while
the target scalar uses the strict `math.log1p`. So the failure surfaces only once
the candidate loop reaches a regression target — which, in the neurips2025
context, it always eventually does. It reproduces on every run until the sentinel
is handled.

## The fix I used to validate

To confirm nothing else downstream was broken, I applied the smallest change that
keeps `log1p` in-domain — floor both sides at 0, so the sentinel contributes zero
distance, and make the two sides symmetric on `np.log1p`:

```python
term.loc[valid] = np.abs(
    np.log1p(np.clip(source_val.loc[valid], 0, None))
    - np.log1p(max(target_val, 0.0))
)
```

With that one change the package runs end to end and writes all of the expected
outputs. The original file is preserved beside it as `...experiment.py.orig`, and
the exact submitted script is inside the return archive. You may well prefer a
different handling — skipping the `n_classes` term when the task is regression,
or mapping the sentinel to NaN upstream so your existing `pd.notna` guard drops
it. The numbers below depend on which fix you pick, so please read them as
confirmation that the pipeline completes, not as the official independent result;
that should come from your corrected package. The ~40 GB is already cached here,
so a corrected rerun will finish in a few minutes without re-downloading.

## What it produced (with the fix above)

51 datasets, 816 task-folds, five arms. On the internal TabArena-style Elo
(dataset unit, the declared expert reference pinned to 1000), the source-informed
self-improving arms rank above the expert reference:

| arm | internal Elo (expert = 1000) | vs expert |
| --- | --- | --- |
| source_informed_self_improving | 1045.1 | +45.1 |
| source_informed_gated_self_improving | 1041.2 | +41.2 |
| source_informed_safeguarded_self_improving | 1037.3 | +37.3 |
| expert_reference | 1000 | 0 |
| target_only_self_improving | 884.7 | −115.3 |

The safeguarded arm carries a lower mean native error than the expert reference
(1616.5 vs 1656.7) and wins on 51% of the datasets. On the prediction-level
normalized-loss bootstrap the safeguarded-minus-expert gap is 0.0019 with a 95%
interval of [0.0004, 0.0037], so it clears zero at the task-fold level. At the
dataset level the same contrast is +37 Elo with an interval of [−38, +116] and a
win rate of 0.51 [0.37, 0.65] — the point estimates favor the method throughout,
but on n = 51 datasets the dataset-level interval still spans zero. The
safeguard barely moves the result relative to the plain source-informed arm
(their contrast interval straddles zero), so most of the gain over the expert is
coming from the source-informed selection itself.

The full return archive
(`tabarena_prediction_level_strong_transfer_20260707_DIAGNOSTIC_PATCHED_return.zip`,
status success) is in this repo next to this note, with all the summaries,
selections, weights, both bootstrap tables, the internal-Elo set, config,
manifest, submitted scripts, and terminal log.

Yuan
