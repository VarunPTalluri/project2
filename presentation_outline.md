# NFL Draft Predictor — recommended presentation outline

Aim for roughly 7–8 slides. Populate the bracketed figures **after running the R Markdown pipeline**.

1. **Decision and audience:** A football operations department has limited detailed film-review capacity. Question: which combine prospects are most likely to be drafted?
2. **Data and why it works:** nflreadr / PFR, 2000–2025, [N] non-specialist attendees; describe fields available before the draft.
3. **Cleaning choices:** Convert height, group positions, exclude post-draft fields, flag incomplete combine drills, impute training medians.
4. **Exploration:** Position-wise draft rates, number of attendees, missing physical drill tests; caution on small categories.
5. **Models and tuning:** Baseline Logistic Regression, KNN, SVM, ANN, Decision Tree, Random Forest; training-only cross-validation, 2022–25 holdout.
6. **Holdout results:** Accuracy, Kappa, sensitivity, specificity, precision; confusion matrix of chosen model; compare to majority baseline.
7. **Business case:** At 25% film-review capacity, number of drafted prospects found; compare to expected random shortlist, $195 illustrative review cost.
8. **Recommendation / limitations:** [Chosen model], how scouts would use it, why drafted is not successful, data and evaluation limitations, future data to collect.
