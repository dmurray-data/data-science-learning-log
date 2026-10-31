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

## October 7, 2026

### Worked On

Deep-Learning Tabular Prediction System — exploratory analysis of relationships among electrical measurements.

### What I Investigated

- Examined correlations among:
  - `Global_active_power`
  - `Global_reactive_power`
  - `Voltage`
  - `Global_intensity`
  - `Sub_metering_1`
  - `Sub_metering_2`
  - `Sub_metering_3`
- Created a correlation matrix and heatmap to compare the strength and direction of relationships among electrical variables.
- Created sampled scatter plots to examine the shape of relationships between `Global_active_power` and:
  - `Global_intensity`
  - `Voltage`
  - `Sub_metering_3`
- Used a 20,000-row random sample for scatter plots instead of plotting the full multi-million-row dataset.

### What I Learned

- Correlation coefficients are useful for quickly identifying strong linear relationships, but scatter plots are necessary to see whether the relationship is actually linear or contains clusters, thresholds, or other structure.
- Sampling is an effective way to visualize very large datasets without overwhelming the plot or wasting computation.
- Electrical measurements can be strongly related for physical reasons, so high correlation does not automatically mean a feature is independently informative.
- EDA should help determine which raw measurements are worth carrying into the forecasting model rather than blindly using every available variable.

### Current Direction

The project will continue to treat household power prediction as a 60-minute-ahead forecasting problem.

The next EDA step is to measure temporal persistence by comparing historical power values at different lags with future `Global_active_power`.

### Next Step

Investigate:

- 1-minute, 5-minute, 15-minute, 30-minute, 60-minute, 120-minute, and 24-hour lag relationships
- correlation between current power and power 60 minutes into the future
- whether daily persistence is strong enough to justify 24-hour lag features


## October 8, 2026

### Worked On

Deep-Learning Tabular Prediction System — lag analysis and temporal persistence for the 60-minute-ahead forecasting target.

### What I Investigated

- Sorted the household power dataset chronologically before creating lag features.
- Created temporary lag features for `Global_active_power` at:
  - 1 minute
  - 5 minutes
  - 15 minutes
  - 30 minutes
  - 60 minutes
  - 120 minutes
  - 1440 minutes (24 hours)
- Created a 60-minute-ahead forecasting target using future `Global_active_power`.
- Measured the correlation between historical power values and the 60-minute-ahead target.
- Compared current power consumption with future power consumption to evaluate short-term persistence.

### What I Learned

- Lag features let the model use past observations as predictors for future behavior.
- The meaning of a lag must always be interpreted relative to the target horizon.
- For a row representing the present time:
  - current `Global_active_power` is 60 minutes behind the target
  - `power_lag_60` is 120 minutes behind the target
  - `power_lag_1440` represents approximately the same time on the previous day
- Correlation with the future target provides evidence for which historical offsets may be useful during feature engineering.
- Time-series feature engineering requires preserving chronological order so each lag retains its correct temporal meaning.

### Current Direction

Use EDA evidence rather than arbitrary choices to select lag features for the forecasting model.

The next analysis will visually examine current power versus 60-minute-ahead power and evaluate daily persistence using the 24-hour lag.

### Next Step

Continue EDA by:

- plotting current `Global_active_power` against `power_target_60`
- examining the relationship between the 24-hour lag and the future target
- deciding which lag intervals should be carried into `03_feature_engineering.ipynb`


## October 9, 2026

### Worked On

Deep-Learning Tabular Prediction System — short-term and daily persistence analysis for the household power forecasting problem.

### What I Investigated

- Plotted current `Global_active_power` against power consumption 60 minutes into the future.
- Examined whether current household power consumption contains useful predictive information for the one-hour-ahead target.
- Plotted historical power consumption against the 60-minute-ahead target to investigate daily persistence.
- Identified an important timing distinction between:
  - `power_lag_1440`: power 24 hours before the current prediction time
  - `power_target_60`: power 60 minutes after the current prediction time
- Added `power_lag_1380` so the model can compare the forecast target with approximately the same clock time on the previous day.
- Added a direct correlation comparison between `power_lag_1380`, `power_lag_1440`, and `power_target_60`.

### What I Learned

- Current household power contains useful information about consumption one hour later, but the relationship is highly variable rather than tightly deterministic.
- A wide scatter around the current-versus-future relationship suggests that current consumption alone will not be enough for accurate forecasting.
- This supports using additional lag features, calendar variables, and electrical measurements.
- Daily persistence exists as a possible source of information, but the visual relationship appears substantially weaker than short-term persistence.
- Lag definitions must always be interpreted relative to the forecast horizon.
- For a 60-minute-ahead forecast, a 1380-minute lag represents approximately the same target clock time on the previous day, while a 1440-minute lag represents power 24 hours before the current prediction time.
- Careful temporal reasoning is necessary to prevent subtly misaligned features in forecasting projects.

### Current Direction

Retain both short-term historical features and a daily-lag candidate for feature engineering, but allow validation performance to determine whether the daily feature actually improves forecasting.

The EDA results continue to support comparing linear and nonlinear models because the future-power relationship contains substantial spread and structure that may not be captured by a simple linear relationship.

### Next Step

Use the completed EDA to decide the initial feature set for `03_feature_engineering.ipynb`, including:

- short-term power lags
- same-time-yesterday lag
- rolling consumption statistics
- calendar features
- selected electrical measurements

Then construct the 60-minute-ahead target and prepare a leakage-safe chronological train/validation/test dataset.


## October 10, 2026

### Worked On

Deep-Learning Tabular Prediction System — began the feature engineering stage.

### Progress

- Created `03_feature_engineering.ipynb`.
- Loaded the validated household power Parquet dataset.
- Re-sorted the data chronologically and verified the time range.
- Prepared to convert EDA findings into a leakage-safe forecasting feature set.

### Next Step

Create:

- calendar features
- lag features
- rolling statistics
- the 60-minute-ahead target

Then remove unusable rows and save the engineered modeling dataset.


## October 11, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the first full feature-engineering pass for the household power forecasting dataset.

### What I Built

- Created calendar features:
  - hour
  - day of week
  - month
  - weekend indicator
- Created power lag features at multiple historical offsets.
- Added the corrected same-target-time-yesterday lag using 1380 minutes.
- Created trailing rolling statistics for recent and daily power behavior.
- Created the 60-minute-ahead forecasting target.
- Defined the final modeling feature list explicitly.
- Removed rows that could not support valid lag, rolling, or future-target values.
- Saved the engineered modeling dataset as:

`household_power_features.parquet`

### Final Dataset

- Original rows: 2,075,259
- Usable modeling rows: 1,950,244
- Rows removed: 125,015
- Final shape: 1,950,244 × 24

### What I Learned

- Feature engineering for forecasting requires strict temporal alignment.
- Lag and rolling features must use only information available at or before prediction time.
- Future targets naturally invalidate the final rows of the dataset.
- Long missing-data blocks also remove surrounding rows when historical windows or future targets depend on unavailable measurements.
- Explicitly defining the feature list makes the modeling pipeline easier to audit and reproduce.

### Next Step

Create a leakage-safe chronological train/validation/test split and fit preprocessing only on the training data before building baseline models.


## October 12, 2026

### Worked On

Deep-Learning Tabular Prediction System — chronological train/validation/test splitting and preprocessing.

### Progress

- Loaded the engineered household power feature dataset.
- Created a leakage-safe chronological split:
  - 70% training
  - 15% validation
  - 15% test
- Verified that the train, validation, and test periods are strictly ordered in time.
- Separated predictors from the 60-minute-ahead target.
- Fit a `StandardScaler` using only the training features.
- Applied the fitted scaler unchanged to training, validation, and test data.
- Verified that the scaled training features are centered near zero with standard deviation near one.

### What I Learned

- Forecasting datasets should be split chronologically rather than randomly.
- Preprocessing can leak future information if scaling parameters are estimated from validation or test data.
- The correct workflow is:

  `fit on training → transform training/validation/test`

- Validation and test data should not necessarily have mean 0 after scaling, because they are transformed using training-set statistics.
- Scaled and unscaled feature versions should both be preserved because neural networks and linear models benefit from scaling, while tree-based models generally do not require it.

### Next Step

Complete the remaining split diagnostics and metadata checks, then begin the baseline-model notebook with a naive persistence baseline followed by linear regression, random forest, and gradient boosting.


## October 13, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the chronological data-splitting and preprocessing stage and began the baseline-modeling stage.

### Progress

- Preserved both scaled and unscaled feature sets for different model families.
- Saved train, validation, and test date ranges and row counts as split metadata.
- Checked target means across train, validation, and test periods to look for temporal distribution shift.
- Completed `04_train_validation_test_split.ipynb`.
- Created `05_baseline_models.ipynb` and documented the modeling goals.

### What I Learned

- Different model families have different preprocessing requirements:
  - linear models and neural networks generally benefit from standardized features
  - tree-based models generally do not require feature scaling
- Chronological split metadata makes the forecasting experiment easier to audit and reproduce.
- Differences between target distributions across time periods can help explain later differences in validation and test performance.
- Baseline models are necessary for determining whether added model complexity actually improves forecasting performance.

### Next Step

Begin model evaluation with:

1. naive persistence baseline
2. linear regression
3. random forest
4. gradient boosting

Use consistent regression metrics so the models can be compared fairly before training the PyTorch neural network.


## October 14, 2026

### Worked On

Deep-Learning Tabular Prediction System — began the baseline-model evaluation stage.

### Progress

- Loaded the engineered household power dataset into `05_baseline_models.ipynb`.
- Recreated the exact chronological 70/15/15 train, validation, and test split.
- Reused the finalized feature set and 60-minute-ahead target.
- Created a reusable regression-evaluation function using:
  - MAE
  - RMSE
  - R²
- Set up a shared results structure so all baseline models can be compared consistently.

### What I Learned

- Model comparisons are only meaningful when every model is evaluated using the same split and metrics.
- RMSE is useful as the primary metric because it penalizes large forecasting errors more heavily than MAE.
- MAE and R² provide complementary views of model performance.
- The held-out test set should remain untouched while model selection is performed on the validation set.

### Next Step

Evaluate the naive persistence baseline and linear regression model before moving to random forest and gradient boosting.


## 2026-10-15

### Worked On

Deep-Learning Tabular Prediction System — established the first forecasting baselines.

### Progress

- Evaluated a naive persistence baseline using current `Global_active_power` as the prediction for power 60 minutes later.
- Fit and evaluated a linear regression model using the engineered feature set.
- Standardized the linear-model inputs using a `StandardScaler` fit only on the training data.
- Evaluated both models on the same chronological validation set using:
  - MAE
  - RMSE
  - R²
- Added both results to a shared comparison table.

### What I Learned

- A persistence baseline provides an essential reference point for time-series forecasting.
- A more complex model should only be considered useful if it can beat the naive baseline on the same validation data.
- Linear regression provides a strong interpretable baseline for determining how much value the engineered features add before using nonlinear models.
- Scaling must continue to be fit on training data only to avoid leakage.
- Consistent metrics and validation data make model comparisons meaningful.

### Next Step

Train and evaluate:

- random forest
- histogram gradient boosting

Then compare all baseline models by RMSE, MAE, R², and improvement over persistence.


## October 16, 2026

### Worked On

Deep-Learning Tabular Prediction System — added the random forest baseline.

### Progress

- Trained a `RandomForestRegressor` for 60-minute-ahead household power forecasting.
- Used a reproducible 300,000-row sample from the chronological training set to keep training time and memory use reasonable.
- Evaluated the trained random forest on the full validation set.
- Added the random forest MAE, RMSE, and R² results to the shared model-comparison table.
- Preserved the held-out test set for final evaluation only.

### What I Learned

- Large tabular datasets can make full random-forest training unnecessarily expensive, so controlled training samples can be useful for establishing baselines.
- Training on a sample does not prevent evaluation on the full validation set.
- Tree-based models can capture nonlinear relationships and feature interactions that linear regression cannot.
- Fair model comparison still depends on using the same validation period and evaluation metrics across all models.

### Next Step

Train and evaluate histogram gradient boosting, then compare all classical baselines against the persistence forecast before moving to the PyTorch neural network.


## October 17, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the histogram gradient boosting baseline.

### Progress

- Trained a `HistGradientBoostingRegressor` on the full chronological training set.
- Used histogram-based gradient boosting because it is better suited to the large tabular dataset than traditional gradient boosting.
- Generated predictions for the full validation period.
- Evaluated the model using:
  - MAE
  - RMSE
  - R²
- Added gradient boosting to the shared baseline-results collection.
- Continued keeping the final test set untouched during model selection.

### What I Learned

- Gradient boosting builds an ensemble sequentially, with later trees correcting errors made by earlier trees.
- Histogram-based boosting can make gradient boosting practical on datasets with more than one million training observations.
- Nonlinear models can capture interactions among lag, rolling, calendar, and electrical features that linear regression cannot represent directly.
- Validation performance, rather than model complexity, should determine whether a more sophisticated model is worthwhile.

### Next Step

Compare persistence, linear regression, random forest, and histogram gradient boosting in one results table, calculate improvement over the persistence baseline, and save the validation results before beginning the PyTorch neural-network model.


## October 18, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the classical baseline comparison stage.

### Progress

- Combined the persistence, linear regression, random forest, and histogram gradient boosting results into one comparison table.
- Ranked models using validation RMSE as the primary metric.
- Calculated each model's percentage improvement over the naive persistence baseline.
- Saved the validation comparison results to:
  `results/metrics/baseline_validation_results.csv`
- Kept the held-out test set untouched for final model evaluation.

### What I Learned

- A shared results table makes model comparison much clearer than inspecting individual outputs separately.
- Improvement over a naive persistence forecast is an interpretable way to measure whether a model adds real forecasting value.
- More complex models should justify their complexity through measurable validation improvement.
- Model selection should continue to use validation performance rather than the final test set.

### Next Step

Begin the PyTorch neural-network model and compare its validation performance directly against the strongest classical baseline.


## October 19, 2026

### Worked On

Deep-Learning Tabular Prediction System — began the PyTorch neural-network modeling stage.

### Progress

- Created `06_pytorch_neural_network.ipynb`.
- Added the project goals and modeling plan for the neural-network phase.
- Defined the current benchmark to beat:
  - Histogram Gradient Boosting RMSE: 0.7885
  - MAE: 0.5264
  - R²: 0.434
  - 26.4% RMSE improvement over persistence
- Planned to use the same chronological train/validation/test split and evaluation metrics as the classical baselines.
- Established that the final test set will remain untouched until model selection is complete.

### Current Goal

Build a GPU-trained PyTorch MLP using the engineered household power features and determine whether deep learning provides enough validation improvement to justify its added complexity.

### Next Step

Load the engineered dataset, recreate the chronological split, scale features using training data only, and build PyTorch datasets and dataloaders.


## October 20, 2026

### Worked On

Deep-Learning Tabular Prediction System — began preparing the real household power dataset for PyTorch neural-network training.

### Progress

- Loaded the engineered household power feature dataset into `06_pytorch_neural_network.ipynb`.
- Recreated the same chronological 70/15/15 train, validation, and test split used for the classical baseline models.
- Reused the finalized 22-feature predictor set and 60-minute-ahead target.
- Fit a `StandardScaler` using only the training features.
- Applied the training-fitted scaler unchanged to validation and test features.
- Verified that the scaled training features have means near zero and standard deviations near one.

### What I Learned

- The neural network must use exactly the same chronological evaluation periods as the classical models for a fair comparison.
- Feature scaling is especially important for neural-network optimization because the input variables originally operate on very different numerical scales.
- Scaling parameters must be learned from training data only to prevent future information from leaking into model development.

### Next Step

Convert the scaled datasets to `float32` PyTorch tensors, create `TensorDataset` and `DataLoader` objects, and inspect the resulting mini-batch shapes before defining the MLP architecture.


## October 21, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the PyTorch data pipeline setup for neural-network training.

### Progress

- Converted scaled train, validation, and test features to `torch.float32` tensors.
- Converted the regression targets to `torch.float32` tensors with shape `[N, 1]`.
- Created `TensorDataset` objects for train, validation, and test data.
- Created `DataLoader` objects using mini-batches.
- Used a starting batch size of 2048 for GPU training on the DGX Spark.
- Enabled shuffled batches for training and deterministic ordering for validation and test sets.
- Inspected a single training batch to verify tensor shapes, dtypes, and expected structure.

### What I Learned

- PyTorch models expect numerical tensors with consistent dtypes and shapes.
- For this project, each input batch has the structure:

  `batch_size × 22 features`

- The target batch has shape:

  `batch_size × 1`

- `TensorDataset` pairs feature and target tensors together, while `DataLoader` controls how those examples are fed to the model in mini-batches.
- Shuffling is appropriate inside the training split because the chronological split has already been established, while validation and test ordering can remain fixed.
- The tensor and batch concepts from the PyTorch foundations work now directly apply to the real portfolio dataset.

### Next Step

Define the tabular MLP architecture, move it to the GPU, inspect its trainable parameter count, and prepare the training loop.


## 2026-10-22

### Worked On

Deep-Learning Tabular Prediction System — defined and configured the first real PyTorch MLP.

### Progress

- Defined a tabular multilayer perceptron with the architecture:

  `22 → 128 → 64 → 32 → 1`

- Added:
  - ReLU activation functions
  - Batch normalization
  - Dropout regularization
- Moved the model to the GPU when CUDA is available.
- Inspected the total and trainable parameter count.
- Reviewed parameter tensor shapes to connect the network architecture back to matrix multiplication concepts.
- Configured the regression loss using `MSELoss`.
- Configured the Adam optimizer with:
  - learning rate `1e-3`
  - mild weight decay
- Added a `ReduceLROnPlateau` scheduler to lower the learning rate when validation loss stops improving.
- Set initial limits for maximum epochs and early stopping patience.

### What I Learned

- The first linear layer maps each 22-feature observation into a 128-dimensional hidden representation.
- Each `Linear` layer performs a learned matrix transformation followed by nonlinear processing in the hidden layers.
- Batch normalization can help stabilize training, while dropout and weight decay provide regularization.
- The final regression layer uses one output neuron with no activation function because the target is a continuous value.
- Optimizer, scheduler, and early-stopping settings are all part of the training strategy, not just the model architecture itself.

### Next Step

Implement the reusable training loop, perform a forward-pass sanity check, and then begin the first full GPU training run.


## October 23, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the neural-network training pipeline and forward-pass validation.

### Progress

- Implemented a reusable PyTorch training function for the tabular MLP.
- Added separate training and validation phases for each epoch.
- Implemented:
  - forward propagation
  - MSE loss calculation
  - backpropagation
  - optimizer updates
  - validation with gradients disabled
- Added training-history tracking for:
  - training loss
  - validation loss
  - learning rate
- Integrated `ReduceLROnPlateau` using validation loss.
- Added early stopping based on validation performance.
- Added checkpoint logic that preserves and restores the model state with the best validation loss.
- Added per-epoch timing and progress reporting.
- Performed a single-batch forward-pass sanity check.
- Verified that the dimensional pipeline produces:

  `2048 × 22 → 2048 × 1`

  matching the target tensor shape.

### What I Learned

- Training and validation require different PyTorch model modes using `model.train()` and `model.eval()`.
- Gradients are required during training but can be disabled during validation with `torch.no_grad()`.
- Early stopping prevents continuing training after validation performance stops improving.
- Saving the best model state ensures the final model represents the strongest validation epoch rather than simply the last epoch.
- Learning-rate scheduling and early stopping can work together to give optimization a chance to improve before training is terminated.
- A successful forward-pass shape check confirms that the dataset, batches, and network architecture are dimensionally compatible before committing to a full training run.

### Next Step

Run the first full GPU training experiment, inspect the training and validation loss curves, and evaluate the restored best model against the histogram gradient boosting benchmark of RMSE 0.7885.


## October 24, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the first full GPU training run of the PyTorch neural network.

### Progress

- Trained the tabular MLP on the DGX Spark using the full training pipeline.
- Used mini-batch GPU training with:
  - MSE loss
  - Adam optimization
  - weight decay
  - learning-rate scheduling
  - early stopping
- Recorded training and validation loss across epochs.
- Plotted the training and validation loss curves.
- Observed rapid improvement during the first several epochs.
- Validation loss reached its strongest region early and then began to plateau and fluctuate.
- Training loss continued decreasing after validation performance stopped improving significantly.
- The training pipeline restored the model state associated with the best validation performance.

### Training Behavior

The loss curves showed a developing generalization gap:

- Training loss continued to decrease steadily.
- Validation loss initially decreased substantially.
- Validation loss later stabilized around approximately 0.64 MSE while training loss continued downward.
- This suggests the model began to mildly overfit the training data after the early epochs.

This behavior demonstrates why validation monitoring and early stopping are important even when training loss continues to improve.

### What I Learned

- Lower training loss does not automatically mean better performance on unseen data.
- Validation loss is the more important signal for deciding when model training has stopped producing useful generalization improvements.
- A growing difference between training and validation loss is evidence that the model is beginning to specialize to the training data.
- Early stopping allows training to terminate while retaining the model from the strongest validation epoch rather than the final epoch.
- Plotting learning curves provides information about model behavior that cannot be seen from a final metric alone.

### Next Step

Generate predictions from the restored best model on the validation set and calculate MAE, RMSE, and R².

Then compare the neural network directly against the current best classical model, Histogram Gradient Boosting, which achieved an RMSE of 0.7885.


## October 25, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed validation evaluation of the first PyTorch neural network.

### Progress

- Generated validation predictions using the restored best MLP checkpoint.
- Calculated the same regression metrics used for the classical models:
  - MAE
  - RMSE
  - R²
- Compared the neural network directly against Histogram Gradient Boosting, the strongest classical baseline.
- Found that the neural network RMSE was approximately 1.24% worse than Histogram Gradient Boosting.
- Created an actual-vs-predicted scatter plot to inspect prediction behavior across the validation set.
- Observed that the neural network predicts the common power-consumption range reasonably well but tends to underpredict large consumption spikes.
- Documented the interpretation of the neural network results.

### Key Result

The first PyTorch MLP performed competitively with Histogram Gradient Boosting, but did not outperform it.

`NN RMSE improvement vs HGB: -1.24%`

This indicates that the additional complexity of the neural network did not provide better validation performance with the current architecture, features, and training configuration.

### What I Learned

- Similar overall RMSE values can hide important differences in prediction behavior.
- Actual-vs-predicted plots help reveal systematic errors that summary metrics alone cannot show.
- The neural network tends to regress extreme power values toward the center of the target distribution.
- Deep learning is not automatically superior to classical machine-learning methods on structured tabular data.
- Model selection should be based on validation evidence rather than model complexity or technique.
- A model losing narrowly to a simpler method is still a useful experimental result.

### Next Step

Preserve this model as the baseline neural-network experiment and decide whether to run one controlled improvement experiment before final model selection.

Potential improvements include changes to the loss function, architecture, regularization, or handling of rare high-power observations while continuing to leave the test set untouched.


## October 26, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed a controlled Huber-loss neural-network experiment.

### Progress

- Created a separate notebook for the Huber-loss experiment.
- Recreated the data loading, chronological split, scaling, tensor conversion, dataloaders, model architecture, and training function so the notebook is fully self-contained.
- Kept the MLP architecture and training configuration unchanged from the baseline neural network.
- Changed only the loss function from `MSELoss` to `HuberLoss`.
- Trained the Huber model with:
  - Adam optimizer
  - weight decay
  - learning-rate scheduling
  - early stopping
- Observed the scheduler reduce the learning rate after validation performance stalled.
- Early stopping triggered after the model failed to improve further.
- Evaluated the restored best Huber model on the validation set.
- Compared its RMSE directly against the baseline MSE-trained neural network.

### Key Result

The Huber-loss experiment did not improve the neural network.

- Baseline NN RMSE: approximately `0.7983`
- Huber NN improvement vs baseline NN: `-1.51%`

The negative improvement means the Huber model performed worse than the original MSE-trained neural network.

### What I Learned

- Controlled experiments make it possible to attribute performance changes to a specific modeling decision.
- A lower training loss value cannot be compared directly across different loss functions.
- Model comparisons should use the same external evaluation metrics such as RMSE, MAE, and R².
- Huber loss did not improve this forecasting problem, even though it was motivated by the neural network's difficulty with high-power observations.
- Not every theoretically reasonable model change leads to better validation performance.
- The original MSE-trained MLP remains the strongest neural-network configuration tested so far.

### Next Step

Run one final controlled neural-network experiment focused on architecture or regularization, then proceed to final model selection and test-set evaluation.


## October 27, 2026

### Worked On

Deep-Learning Tabular Prediction System — began the final controlled neural-network experiment.

### Progress

- Created `08_neural_network_reduced_dropout.ipynb`.
- Defined the experiment goal and hypothesis.
- Established that the new experiment will keep the existing:
  - dataset
  - chronological train/validation/test split
  - preprocessing
  - hidden-layer sizes
  - optimizer
  - learning rate
  - weight decay
  - scheduler
  - batch size
  - early stopping procedure
- Defined the controlled change as reduced dropout:
  - baseline dropout: `0.20` and `0.10`
  - experimental dropout: `0.10` and `0.05`
- Framed the experiment around the possibility that the baseline MLP may be slightly over-regularized.

### What I Learned

- Controlled model experiments are most useful when only one meaningful factor is changed at a time.
- Reducing dropout tests whether the network can benefit from retaining more hidden-unit activity during training.
- A small performance difference should be interpreted cautiously because neural-network training includes randomness from initialization and minibatch ordering.
- The purpose of this experiment is not to force the neural network to win, but to test one final reasonable hypothesis before final model selection.

### Next Step

Recreate the self-contained data and training pipeline, set reproducibility seeds, define the reduced-dropout architecture, and run the final neural-network validation experiment.


## October 30, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the final controlled neural-network experiment.

### Progress

- Created and completed the reduced-dropout neural-network experiment.
- Recreated the self-contained data, preprocessing, tensor, dataloader, model, and training pipeline.
- Added reproducibility seeds for Python, NumPy, and PyTorch.
- Kept the baseline architecture and training setup unchanged except for dropout probabilities.
- Reduced dropout from:
  - `0.20 → 0.10`
  - `0.10 → 0.05`
- Trained the model with the same:
  - MSE loss
  - Adam optimizer
  - weight decay
  - learning-rate scheduler
  - early stopping
- Generated validation predictions and evaluated MAE, RMSE, and R².
- Compared the reduced-dropout model directly against the baseline neural network and Histogram Gradient Boosting.

### Key Result

Reducing dropout did not improve validation performance.

- Improvement vs baseline NN: `-0.09%`
- Improvement vs HGB: `-1.32%`

The reduced-dropout model performed essentially the same as the baseline neural network, but slightly worse.

### What I Learned

- Small architecture changes do not necessarily produce meaningful performance improvements.
- A difference of only a fraction of a percent should be interpreted cautiously.
- The baseline MSE-trained MLP remains the strongest neural-network configuration tested.
- Histogram Gradient Boosting remains the strongest overall validation model.
- Controlled experiments are more valuable than endless hyperparameter tuning because they provide clear evidence about whether a specific change helps.
- At this point, further neural-network tuning is unlikely to be justified without a new modeling hypothesis.

### Next Step

Stop neural-network tuning and move to final model selection.

Evaluate the selected models on the previously untouched test set, compare final generalization performance, and determine the preferred model for the project.


## October 31, 2026

### Worked On

Deep-Learning Tabular Prediction System — began Phase 2 temporal feature expansion.

### Progress

- Created `09_temporal_feature_expansion.ipynb`.
- Defined the Phase 2 goal of improving 60-minute-ahead forecasting through better temporal features rather than additional neural-network tuning.
- Loaded the validated minute-level household power dataset.
- Sorted the data chronologically to preserve correct time alignment.
- Recreated the original Phase 1 calendar features:
  - hour
  - day of week
  - month
  - weekend indicator
- Recreated the original lag features:
  - 1 minute
  - 5 minutes
  - 15 minutes
  - 30 minutes
  - 60 minutes
  - 120 minutes
  - 1380 minutes
- Recreated the original rolling features:
  - 15-minute rolling mean
  - 60-minute rolling mean
  - 60-minute rolling standard deviation
  - 1440-minute rolling mean
- Recreated the 60-minute-ahead prediction target.

### What I Learned

- Phase 2 should improve the information available to the models before increasing model complexity.
- Rebuilding features from the validated minute-level timeline ensures that all lag and rolling calculations remain correctly aligned.
- The existing feature set mainly describes recent power levels and averages, but does not explicitly describe direction, volatility, or recent extremes.
- Preserving the same forecasting target and chronological structure makes Phase 2 directly comparable with the Phase 1 benchmarks.

### Next Step

Add new temporal features that describe:

- recent changes in power
- short-term volatility
- recent maximum and minimum power
- recent power range

Then inspect their distributions and relationships with the 60-minute-ahead target before rebuilding the Phase 2 modeling dataset.
