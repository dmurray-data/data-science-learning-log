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

## October 6, 2026

### Worked On

Deep-Learning Tabular Prediction System — exploratory data analysis for the household power consumption dataset.

### What I Investigated

- Examined the long-term behavior of `Global_active_power`.
- Aggregated minute-level power readings into daily averages to make multi-year trends easier to interpret.
- Examined average household power consumption by:
  - hour of day
  - day of week
  - month
- Continued validating the dataset as a time-dependent forecasting problem rather than a conventional randomly split regression problem.

### What I Learned

- Minute-level time-series data often needs aggregation before long-term structure becomes visible.
- Daily resampling makes multi-year consumption patterns much easier to inspect than plotting millions of raw observations.
- Calendar variables such as hour, weekday, and month can reveal recurring consumption patterns and may become useful forecasting features.
- EDA should directly inform feature engineering rather than simply produce plots.
- Because the project will predict future power consumption, temporal order must be preserved when building features and evaluating models.

### Current Decision

Continue treating the project as a 60-minute-ahead household power forecasting problem.

Keep the raw timeline intact through feature engineering so lag variables and future targets preserve their true temporal meaning.

Potential calendar features now include:

- hour of day
- day of week
- month
- weekday/weekend indicator

These will later be evaluated alongside historical electrical measurements and lag features.

### Next Step

Continue EDA by studying:

- relationships among electrical measurements
- autocorrelation and lag behavior in `Global_active_power`
- which historical time offsets are likely to be useful predictors for the 60-minute-ahead targe
