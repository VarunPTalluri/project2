# NFL Draft Predictor — modular R Markdown project

## Deliverables

- **`main.Rmd`**: consolidated report for the final R Markdown/HTML submission. It **loads saved `.rds` objects** and does not re-run the models.
- **`00_run_all.Rmd`**: renders the 12 ordered data/model/evaluation/check phases and the final report.
- **`01_...` through `12_...`**: modular data cleaning, preprocessing, individual models, optional probability blend, comparison, and business analysis.
- **`presentation_outline.md`**: suggested slides for the class presentation.

## Run in RStudio

1. Unzip all files into one directory, then open that folder in RStudio and set it as your working directory.
2. Install required packages **once** if missing:
   ```r
   install.packages(c("rmarkdown", "knitr", "nflreadr", "caret", "class",
                      "kernlab", "neuralnet", "rpart", "rpart.plot", "randomForest"))
   ```
3. Knit **`00_run_all.Rmd`**. First run downloads the full nflverse combine file for seasons 2000–2025 and caches it as **`data/combine_2000_2025.csv`**. The download requires internet access.
4. Knit **`main.Rmd`** independently after the numbered files have completed, producing `main.html`.
5. Submit `main.Rmd` and `main.html` for the final written deliverable, with the modular files and data snapshot if the instructor wants reproducible code.

**Re-running after code changes:** Re-run the changed file **and every later dependent file**. In particular, if you modify `01_data_preparation.Rmd`, re-run Files 02–12 before re-knitting `main.Rmd`. All objects are saved and loaded by `saveRDS()` / `readRDS()`; do not depend on global RStudio state.

## How the R objects connect

| File | Key R objects saved |
|---|---|
| `01_data_preparation.Rmd` | Import and clean **and** split/preprocess: `raw_labeled.rds`, `cleaned.rds`, `measure_names.rds`, `removed_specialists.rds`, `train_data.rds`, `test_data.rds`, `train_x_raw.rds`, `test_x_raw.rds`, `train_x_scaled.rds`, `test_x_scaled.rds`, `train_y.rds`, `test_y.rds`, `feature_names.rds` |
| `02_baseline_logistic.Rmd` | `prelim_model.rds` (baseline-only reference) |
| `03_logistic.Rmd` | `logit_model`, `logit_cv`, `logit_prob`, `logit_pred` |
| `04_knn.Rmd` | `knn_model`, `knn_cv`, `knn_prob`, `knn_pred` |
| `05_svm.Rmd` | `svm_model`, `svm_cv`, `svm_prob`, `svm_pred` |
| `06_ann.Rmd` | `ann_model`, `ann_cv`, `ann_prob`, `ann_pred` |
| `07_decision_tree.Rmd` | `tree_model`, `tree_cv`, `tree_prob`, `tree_pred` |
| `08_random_forest.Rmd` | `rf_model`, `rf_cv`, `rf_prob`, `rf_pred` |
| `09_probability_blend.Rmd` | `base_probs.rds`, `blend_prob.rds`, `blend_pred.rds` |
| `10_evaluate_models.Rmd` | `all_confusions.rds`, `metrics.rds` |
| `11_scouting_business_case.Rmd` | `scouting_table.rds`, `capacity_table.rds`, cost and capacity objects |
| `12_quality_checks.Rmd` | Checks years, absence of post-draft predictors, probability validity, and result lengths; no fitted models |

`.rds` suffixes are omitted in some table entries for readability. The trained model is an R object; `*_prob` is a probability vector for **Drafted**, and `*_pred` is a factor prediction for confusion matrices.

## Why this design?

**Business question:** Can a team prioritize limited scouting effort using only information known before the draft?

**Outcome:** `draft_round` is used to construct `Drafted`; it is **not a predictor**. We never use other post-draft information such as `draft_team` or `draft_ovr` as model inputs. We also exclude names/IDs and high-cardinality school names. This is intentionally not a prediction of NFL career success.

**Cleaning and preprocessing (together in File 01):** Convert height (e.g. `6-2`) to inches; group playing positions; exclude specialist roles (`K`, `P`, `LS`); keep eight physical/drill variables; flag missing tests; impute values from historical training medians only. Both imputed and observed values retain the same unit. Keep the exact training snapshot in `data/` for replicability.

**Splitting:** Hold out **2022–2025** as unseen seasons; train/tune using **2000–2021**. This temporal split is more relevant to a future scouting decision than randomizing within each draft class.

**Model selection:** Logistic Regression (basic vs richer specification), KNN (`k`), SVM (linear vs radial), ANN (1 vs 2 hidden neurons), Decision Tree (`cp`) and Random Forest (`mtry`). `caret::train()` uses 5-fold cross-validation where applicable, and ANN uses three manually constructed folds. Kappa is the main metric; Accuracy helps interpret ties. The holdout years are not used for tuning.

**Combining models:** Optional equal-weight probability blend of all six base models. It is **not a second-level trained stacked learner**, since the assignment does not require a stack and doing that correctly would need a separate calibration/training procedure. All models and the blend are evaluated on the same holdout.

**Business evaluation:** Rank held-out players by predicted draft probability; with a fixed detailed-film-review budget, measure how many eventual drafted players are found relative to a random shortlist. Assumed 3 hours at $65/hour yields $195 per detailed review. Costs are hypothetical; being drafted is **not** career success.

## Limitations / cautions

- **The actual draft outcome** is an imperfect proxy for scout-worthy players; combine data do not include film, injuries, interviews, strategy, or team needs.
- **Selection bias:** combine participants are already invited prospects, not all college football players.
- **Missing combine drills** may be systematically related to prospect status; imputing them and flagging missingness helps preserve rows, but does not make absence random.
- **Cross-validation preprocessing:** medians and scales are estimated on historical training years before CV folds, so the CV figures can be slightly optimistic. The 2022–2025 holdout remains separate and is the primary evaluation.
- **Data snapshot:** nflverse data may be updated retrospectively; save the downloaded CSV to reproduce a particular result.
- **Source:** https://nflreadr.nflverse.com/reference/load_combine.html and https://nflreadr.nflverse.com/articles/dictionary_combine.html.

## Execution status for this ZIP

The R Markdown code has been constructed and statically reviewed, but the NFL dataset itself was **not uploaded in the chat**, external download was not available in the authoring environment, and **R is not installed here**. As a result, **no fitted model metrics or completed HTML report are included**. Run `00_run_all.Rmd` in RStudio with internet access to generate actual model results, `.rds` files, and finished HTML. Never present placeholder or unrun model results as measured findings.
