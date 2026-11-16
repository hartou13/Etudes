# Shared arc

Each notebook in this folder is a short study of one table. The opening says what one row is, what the target means, and which models will be fit, in the order they appear later.

1. **Load.** UCI downloads start with the certificate cell, then `fetch_ucirepo`. A local file or a scikit-learn dataset skips that cell. Print the shape and the column names.
2. **Inspect the data.** Shape, dtypes, `describe`, and missing values.
3. **Investigate the target.** Counts for a class, or the range, center, and correlations for a number.
4. **Visualise relationships.** A seaborn pairplot.
5. **Train and test split.** Hold out 20% with `random_state=42`. Stratify when the target is a class.
6. **Baseline.** Logistic regression for a class, linear regression for a number, usually inside a pipeline. Score it on the held-out rows.
7. **One change at a time.** Another encoding, a dropped column, another model, or a transform. A markdown cell after each result says what the numbers mean.
8. **Conclusion.** Row count, split sizes, and the scores, in the same order as the experiments.

`random_state=42` is the seed. Classification cross-validation is stratified and uses 5 folds. `LogisticRegression` uses `max_iter=10000`.
