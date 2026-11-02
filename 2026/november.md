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
