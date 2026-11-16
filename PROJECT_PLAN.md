# Project plan and implementation decisions

## Stage 1 — Dataset

Use the exact `nflreadr::load_combine(seasons = 2000:2025)` source from the class-provided NFL Draft Predictor code. Cache the downloaded CSV in `data/` so every file references the same records. Check the outcome, counts, positions, and missing drills.

## Stage 2 — Business question

**Can an NFL scouting department use pre-draft combine information to prioritize detailed film review among combine invitees?** Predict whether the player is *drafted*, not NFL career value or whether the player should be drafted.

## Baseline model used in the final analysis

File 02 retains a simple Logistic Regression using position, height, and weight as a reference for the expanded models. The already-submitted Intermediate Deliverable #1 is not included in this package.

## Stage 3 — Modeling

Split and preprocess in File 01 by time: 2000–2021 for training/tuning, 2022–2025 for a future-year holdout. Avoid leaking drafted outcome fields into X. Use positions and physical/drill measurements, plus indicators for missing drills, with training-median imputation. Dummy-code factors where required and scale distance-based inputs from historical training statistics.

Compare these six class-taught models: Logistic Regression, KNN, SVM, ANN, Decision Tree, Random Forest. For each, identify the starting version and one modest tuning attempt using historical-only cross-validation. Evaluate all models with Accuracy, Kappa, confusion matrix, sensitivity and specificity. An optional equal-weight probability blend offers a simple seventh predictive approach. No complex second-level stack is required here.

## Business evaluation

With a hypothetical 25% film-review capacity, compare the number of eventual drafted players captured by each model against the expected number from a random shortlist of the same size. Show cost per review and a 10/25/40% capacity sensitivity table. Do not confuse future draft selection with actual career success.

## Stage 4 — Final deliverables

The `main.Rmd` report reads previously saved objects and compiles visualizations, model summaries, confusion matrices, business analysis, and limitations. Knit it to create `main.html`. Use `presentation_outline.md` as the 8-slide talk structure. All files can be run in order through `00_run_all.Rmd`.

## Validation status

All R Markdown chunks and `.rds` dependencies have been statically checked. The NFL dataset and R runtime were unavailable in the authoring environment, so the zipped package contains no measured model scores and no final knitted HTML. Run the pipeline in RStudio to generate those results.
