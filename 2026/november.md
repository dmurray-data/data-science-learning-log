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


## November 5, 2026

### Worked On

Deep-Learning Tabular Prediction System — started the Phase 2 model-comparison stage.

### Progress

- Created `10_phase2_model_comparison.ipynb`.
- Added the goals, benchmarks, experimental question, and evaluation plan for Phase 2.
- Defined the main comparison as:
  - Phase 1 HGB using 22 features
  - Phase 2 HGB using 31 features
- Established the Phase 1 HGB benchmark to beat:
  - RMSE: `0.7885`
  - MAE: `0.5264`
  - R²: `0.434`
- Documented that the Phase 2 experiment will initially keep HGB hyperparameters unchanged so the effect of the expanded feature set can be measured cleanly.
- Reaffirmed that the chronological split and untouched test set will be preserved during model selection.

### What I Learned

- A new feature-engineering phase should be evaluated with the same model settings before changing model hyperparameters.
- Keeping the modeling procedure fixed makes it easier to attribute performance differences to the new features themselves.
- Validation metrics should continue to guide model selection while the final test set remains reserved for unbiased evaluation.
- Phase 2 is now structured as a direct test of whether better temporal information improves forecasting performance.

### Next Step

Load the Phase 2 feature dataset, recreate the chronological 70/15/15 split, and train Histogram Gradient Boosting using the expanded 31-feature set.

Then compare its validation MAE, RMSE, and R² directly against the Phase 1 HGB benchmark.

## Nov. 7, 2026

### Worked On

Deep-Learning Tabular Prediction System — evaluated Phase 2 Histogram Gradient Boosting using the expanded temporal feature set.

### Progress

- Loaded the Phase 2 modeling dataset with 31 features.
- Recreated the same chronological 70/15/15 train, validation, and test split used in Phase 1.
- Kept the test set untouched.
- Trained Histogram Gradient Boosting using the same hyperparameters as Phase 1.
- Evaluated the Phase 2 HGB model on the validation set using:
  - MAE
  - RMSE
  - R²
- Compared the Phase 2 model directly against the Phase 1 HGB benchmark.

### Key Result

The Phase 2 temporal features produced a small validation improvement.

- RMSE improvement vs Phase 1 HGB: `+0.23%`
- MAE change: `-0.0001`
- R² change: `+0.0029`

This indicates that the additional power-change, volatility, and recent-extreme features contain useful forecasting information, but the improvement is modest.

### What I Learned

- Feature engineering can improve model performance even when the modeling algorithm and hyperparameters remain unchanged.
- A small positive gain is still useful evidence when the experiment is controlled.
- The original Phase 1 feature set already captured most of the predictive signal available to HGB.
- The expanded temporal features add some new information, but they do not fundamentally change the difficulty of the 60-minute forecasting problem.
- Keeping the model configuration fixed made it possible to attribute the improvement primarily to the new feature set.

### Next Step

Retrain the baseline PyTorch MLP using the same 31 Phase 2 features and unchanged neural-network architecture and training settings.

Then compare whether the expanded temporal features help the neural network more than they helped Histogram Gradient Boosting.

## Nov. 8, 2026

### Worked On

Deep-Learning Tabular Prediction System — prepared the Phase 2 neural-network data pipeline.

### Progress

- Continued work in `10_phase2_model_comparison.ipynb`.
- Scaled the 31-feature Phase 2 input set using a `StandardScaler` fit only on the training data.
- Converted training and validation features and targets to `torch.float32` tensors.
- Created PyTorch `TensorDataset` objects for the Phase 2 training and validation sets.
- Created `DataLoader` objects using the same batch size and shuffle behavior as the Phase 1 neural-network experiment.
- Inspected a single training batch to verify that the expanded feature pipeline is working correctly.
- Confirmed that the neural-network input now uses 31 features rather than the original 22.

### What I Learned

- Expanding the feature set changes the neural network's input dimensionality but does not require changing the overall training pipeline.
- Keeping the preprocessing, batch size, and data-loading procedure unchanged makes the Phase 2 experiment more controlled.
- Scaling must still be fit on training data only to prevent information leakage.
- Inspecting one batch before training is a simple way to verify tensor shape and target alignment.
- The Phase 2 neural network will now test whether richer temporal information provides more value to the MLP than it provided to Histogram Gradient Boosting.

### Next Step

Define the same baseline MLP architecture using 31 input features, set reproducibility seeds, configure the same MSE/Adam/scheduler training setup, and train the Phase 2 neural network.

## 2026-11-09

### Worked On

Deep-Learning Tabular Prediction System — completed the Phase 2 neural-network setup and prepared the model for training.

### Progress

- Defined the Phase 2 tabular MLP using the expanded 31-feature input set.
- Kept the hidden-layer structure consistent with the Phase 1 baseline network.
- Added the same:
  - batch normalization
  - ReLU activations
  - dropout configuration
- Set reproducibility seeds for Python, NumPy, and PyTorch.
- Configured the training device to use CUDA when available.
- Instantiated the Phase 2 neural network with the correct 31-feature input dimension.
- Configured the same training setup used in Phase 1:
  - MSE loss
  - Adam optimizer
  - learning rate `1e-3`
  - weight decay `1e-5`
  - `ReduceLROnPlateau` scheduler
  - maximum epoch limit
  - early-stopping patience
- Reused the existing training-function design so the Phase 2 neural-network experiment remains directly comparable with Phase 1.

### What I Learned

- Expanding the feature set changes the size of the first neural-network weight matrix while the rest of the architecture can remain unchanged.
- Keeping architecture and training settings fixed makes it easier to attribute any performance difference to the new temporal features.
- Reproducibility seeds are important when comparing neural-network experiments because initialization and minibatch ordering can otherwise introduce additional variation.
- The Phase 2 experiment is now set up as a controlled test of feature quality rather than a simultaneous architecture and hyperparameter experiment.

### Next Step

Run the full Phase 2 neural-network training process using early stopping and learning-rate scheduling.

Then evaluate validation MAE, RMSE, and R² and compare the Phase 2 MLP against both the Phase 1 neural network and the Phase 2 Histogram Gradient Boosting model.


## Nov. 10, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the Phase 2 neural-network comparison.

### Progress

- Trained the Phase 2 baseline MLP using the expanded 31-feature temporal dataset.
- Kept the same architecture and training setup used in Phase 1 so the comparison remained controlled.
- Evaluated the Phase 2 neural network on the validation set using:
  - MAE
  - RMSE
  - R²
- Compared the Phase 2 neural network directly against:
  - the Phase 1 neural network
  - the Phase 2 Histogram Gradient Boosting model
- Confirmed that the Phase 2 neural network improved slightly over the Phase 1 neural network.
- Confirmed that Histogram Gradient Boosting still remained the strongest validation model.

### Key Results

Phase 2 neural network:

- MAE: `0.5218`
- RMSE: `0.7973`
- R²: `0.4215`

Comparison:

- Phase 1 NN RMSE: `0.7983`
- Phase 2 NN improvement vs Phase 1 NN: `+0.13%`
- Phase 2 HGB RMSE: `0.7867`
- Phase 2 NN RMSE: `0.7973`

### What I Learned

- The expanded temporal feature set helped the neural network, but only slightly.
- Histogram Gradient Boosting benefited more from the Phase 2 features than the neural network did.
- Better features can improve multiple model families without necessarily changing the model ranking.
- The neural network remains competitive, but HGB continues to provide stronger validation performance with less modeling complexity.
- At this point, the evidence supports selecting Phase 2 HGB as the leading model rather than continuing to tune the neural network.

### Next Step

Finalize model selection and evaluate the selected Phase 2 model on the previously untouched test set.

Use the test-set results as the final estimate of generalization performance and complete the project comparison.

## Nov. 11, 2026

### Worked On

Deep-Learning Tabular Prediction System — began the final model-selection and test-evaluation stage.

### Progress

- Created `11_final_model_selection_and_test_evaluation.ipynb`.
- Defined the final project goal as evaluating the selected model on the previously untouched test set.
- Selected Phase 2 Histogram Gradient Boosting as the final model based on validation evidence.
- Documented the Phase 2 HGB validation benchmark:
  - RMSE: `0.7867`
- Recorded the Phase 2 neural-network comparison for context:
  - MAE: `0.5218`
  - RMSE: `0.7973`
  - R²: `0.4215`
- Defined the final evaluation plan and success criteria.
- Reaffirmed that the final notebook will focus on measurement and conclusion rather than additional tuning.

### What I Learned

- Final model selection should be based on validation evidence, not test-set performance.
- The test set should remain untouched until all modeling and tuning decisions are complete.
- A final test evaluation is meant to estimate generalization on genuinely unseen future data.
- The selected model does not need to exactly reproduce validation performance, but test metrics should remain reasonably close if the model generalizes well.
- At this stage, the project is transitioning from experimentation to final evidence and conclusions.

### Next Step

Recreate the finalized Phase 2 HGB setup, generate predictions on the untouched test set, calculate MAE, RMSE, and R², and compare test performance against the validation benchmark.

## Nov. 12, 2026

### Worked On

Deep-Learning Tabular Prediction System — began the final test-set evaluation workflow.

### Progress

- Loaded the finalized Phase 2 feature dataset.
- Recreated the same chronological 70/15/15 train, validation, and test split used during model development.
- Verified the date ranges for each split.
- Recreated the finalized 31-feature Phase 2 predictor list.
- Confirmed the target remains `power_target_60`.
- Defined the final training and untouched test matrices:
  - `X_train`
  - `y_train`
  - `X_test`
  - `y_test`
- Preserved the test set for the first true final evaluation of the selected model.

### What I Learned

- Final evaluation should reuse the exact data split and feature definition that were used during model selection.
- The test set should only be accessed after all modeling and tuning decisions are complete.
- Keeping the final evaluation pipeline simple reduces the chance of accidentally introducing a new modeling decision after seeing test performance.
- The selected model will now be judged on genuinely unseen future data rather than on the validation period used for development.

### Next Step

Train the finalized Phase 2 Histogram Gradient Boosting model using the fixed selected hyperparameters, generate test-set predictions, and calculate final MAE, RMSE, and R².

Then compare test RMSE against the Phase 2 validation benchmark of `0.7867`.
