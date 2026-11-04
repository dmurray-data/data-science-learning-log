## November 1, 2026

### Worked On

Deep-Learning Tabular Prediction System — continued Phase 2 temporal feature expansion.

### Progress

- Added features describing recent changes in household power consumption:
  - 1-minute power change
  - 5-minute power change
  - 15-minute power change
  - 60-minute power change
- Added a short-term 15-minute rolling standard deviation to represent recent volatility.
- Added rolling maximum features for:
  - 15 minutes
  - 60 minutes
- Added a 60-minute rolling minimum feature.
- Created `rolling_range_60` as the difference between the recent 60-minute maximum and minimum power values.
- Continued building all temporal features using current and historical observations only to avoid future-data leakage.

### What I Learned

- Lag features describe past power levels, while change features explicitly describe the direction and magnitude of recent movement.
- Rolling standard deviation provides information about how stable or volatile recent consumption has been.
- Rolling maximum and minimum features provide information about recent local extremes.
- `rolling_range_60` summarizes how widely power consumption has moved during the previous hour.
- These features may help models distinguish between stable consumption and periods where household load is changing rapidly.
- Better temporal information may be more valuable than simply increasing neural-network complexity.

### Next Step

Inspect the distributions of the new temporal features and measure their relationships with the 60-minute-ahead target.

Then assemble the complete Phase 2 feature set, remove rows made unusable by lag/rolling calculations, and create the expanded modeling dataset.


## November 2, 2026

### Worked On

Deep-Learning Tabular Prediction System — analyzed the new Phase 2 temporal features.

### Progress

- Inspected descriptive statistics for the newly engineered temporal features.
- Reviewed the distributions and numerical ranges of:
  - recent power-change features
  - short-term volatility
  - recent rolling maximums
  - recent rolling minimums
  - 60-minute rolling range
- Calculated correlations between the new temporal features and the 60-minute-ahead power target.
- Used the correlation analysis as an initial diagnostic for whether the new features contain useful forecasting signal.
- Kept features with potentially useful nonlinear information even when their simple linear correlation with the target was modest.

### What I Learned

- Correlation is useful for understanding direct linear relationships, but it does not determine whether a feature will help nonlinear models.
- Features such as rolling range, recent maximums, and power changes may contribute through interactions with other predictors rather than through strong standalone correlation.
- Inspecting feature distributions helps identify unusual scales, extreme values, and behavior that may affect model training.
- Phase 2 is focused on improving the information available to the models rather than increasing model complexity.

### Next Step

Assemble the complete expanded Phase 2 feature list, create the final modeling dataframe, remove rows made unusable by lag and rolling calculations, verify chronological integrity and missing values, and save the expanded feature dataset for modeling.


## November 3, 2026

### Worked On

Deep-Learning Tabular Prediction System — assembled and validated the expanded Phase 2 modeling dataset.

### Progress

- Defined the complete Phase 2 feature list.
- Combined:
  - original electrical measurements
  - original lag features
  - calendar features
  - original rolling features
  - new power-change features
  - new volatility and recent-extreme features
- Expanded the modeling feature set from 22 features to 31 features.
- Built the Phase 2 modeling dataframe using:
  - datetime
  - 31 predictor features
  - the 60-minute-ahead target
- Removed rows that could not be used because of lag, rolling-window, or target-related missing values.
- Checked the resulting dataset for:
  - missing values
  - chronological ordering
  - duplicate timestamps
- Confirmed the expected feature count and dataframe column structure.

### What I Learned

- A clean modeling dataset should be validated before any new model is trained.
- Feature engineering can increase model complexity even when the underlying model architecture stays unchanged.
- Chronological integrity remains especially important in forecasting problems because incorrect ordering can introduce leakage.
- Verifying feature counts and dataset structure helps catch mistakes before they propagate into modeling experiments.
- Phase 2 now provides both HGB and the neural network with the same expanded 31-feature input space for a fair comparison.

### Next Step

Inspect a few rows for sanity, verify that the new range features behave as expected, save the Phase 2 feature dataset to Parquet, and then begin retraining Histogram Gradient Boosting using the expanded feature set.

## October 4, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the Phase 2 temporal feature dataset.

### Progress

- Inspected selected Phase 2 rows to confirm the new engineered features behaved as expected.
- Verified that recent change, volatility, maximum, minimum, and range features were numerically sensible.
- Confirmed that `rolling_range_60` remained nonnegative as expected.
- Completed the final Phase 2 dataset validation.
- Saved the expanded modeling dataset to:

  `data/processed/household_power_features_phase2.parquet`

- Reloaded the saved Parquet file to verify that it was written correctly.
- Confirmed that the saved dataset retained the expected shape and structure.
- Finalized the expanded 31-feature dataset for Phase 2 model experiments.

### What I Learned

- Feature engineering should include both statistical validation and simple sanity checks based on what each feature is supposed to represent.
- Saving a clean intermediate modeling dataset makes later experiments more reproducible and prevents feature-generation logic from being duplicated across modeling notebooks.
- Reloading a saved artifact is a useful final verification step before treating it as the source of truth for future modeling.
- Phase 2 is now ready to test whether better temporal information improves model performance without changing the forecasting target.

### Next Step

Create the Phase 2 model-comparison notebook and retrain Histogram Gradient Boosting first using the expanded 31-feature dataset.

Compare its validation RMSE against the Phase 1 HGB benchmark of `0.7885` before retraining the neural network.
