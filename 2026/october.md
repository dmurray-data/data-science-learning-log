# October 2026

## October 5, 2026

### Worked On

Deep Learning Tabular Prediction System.

### What I Investigated

Examined missing values in the `sub_metering_3` feature during exploratory
data analysis.

The feature contains approximately 1.2% missing observations.

I also investigated whether the missing values occurred randomly or in
contiguous blocks.

### What I Learned

Missing-data patterns can influence the appropriate imputation strategy.

For time-series data, contiguous missing blocks may require a different
approach than isolated missing observations.

### Current Decision

Before selecting an imputation method, I will investigate the temporal
structure of the missing observations and determine whether interpolation,
forward filling, or another approach is justified.

### Next Step

Continue exploratory data analysis and investigate relationships between the
household power-consumption variables.
