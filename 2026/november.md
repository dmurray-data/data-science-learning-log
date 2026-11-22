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

## Nov. 13, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the final test-set evaluation of the selected model.

### Progress

- Trained the finalized Phase 2 Histogram Gradient Boosting model using the selected fixed hyperparameters.
- Generated predictions for the previously untouched test set.
- Evaluated final test performance.
- Compared final test RMSE directly against the Phase 2 validation benchmark.
- Confirmed that the selected model generalized well to unseen future data.

### Key Result

Final selected model:

**Phase 2 Histogram Gradient Boosting**

- Validation RMSE: `0.7867`
- Test RMSE: `0.6912`
- Test RMSE change vs validation: `-12.14%`

The test RMSE was substantially lower than the validation RMSE.

### Interpretation

The lower test error suggests that the final test period was easier to forecast than the validation period.

This should not be interpreted as the model itself improving after selection. Instead, the unseen test period likely had a more favorable error profile.

Most importantly, the final model maintained strong performance on completely unseen future data and showed no evidence of severe overfitting.

The test result supports the earlier validation-based decision to select Phase 2 Histogram Gradient Boosting as the preferred model.

### What I Learned

- The test set provides a final estimate of model performance after all tuning and model-selection decisions are complete.
- Validation and test performance do not need to be identical because different chronological periods can have different distributions and forecasting difficulty.
- A lower test error does not mean the model became better after selection; it can reflect an easier holdout period.
- The final test result should be interpreted together with the full validation history rather than in isolation.
- Preserving the test set until the end made the final evaluation meaningful and unbiased by the model-development process.

### Next Step

Complete the final project wrap-up by:

- recording final test MAE and R²
- creating final prediction diagnostics
- assembling the complete model-comparison table
- documenting conclusions and limitations
- summarizing the project in a portfolio-ready form

## Nov. 14, 2026

### Worked On

Deep-Learning Tabular Prediction System — continued the final project evaluation and diagnostic analysis.

### Progress

- Recorded the complete final test metrics for the selected Phase 2 Histogram Gradient Boosting model.
- Saved the final test results for use in the project summary.
- Created final prediction diagnostics for the untouched test set.
- Generated an actual-vs-predicted comparison for the final model.
- Calculated test residuals using:

  `residual = actual - predicted`

- Plotted the final test residual distribution.
- Plotted residuals against predicted power to inspect whether prediction errors were randomly distributed.
- Observed that residuals are concentrated near zero but have a longer positive tail.
- Observed visible structure and changing error variance in the residual-vs-predicted plot.

### Diagnostic Interpretation

The residual distribution is centered near zero, indicating that the final HGB model does not have a large overall prediction bias.

However, the positive tail shows that some high-consumption observations are underpredicted.

The residual-vs-predicted plot also contains structured bands and changing error variance rather than completely random scatter. This suggests that household power consumption contains multiple operating regimes and that forecasting difficulty changes depending on the underlying consumption state.

### What I Learned

- Final regression metrics describe overall accuracy, while residual diagnostics reveal where and how a model makes mistakes.
- Residuals centered near zero are evidence against a large systematic prediction bias.
- Positive residuals represent underprediction when residuals are defined as `actual - predicted`.
- Heteroscedastic residuals indicate that prediction uncertainty is not constant across the range of predictions.
- Structured residual patterns can reveal behavior that is hidden by aggregate metrics such as RMSE.
- Rare or rapidly changing high-power periods remain one of the most difficult parts of the forecasting problem.

### Next Step

Complete the final model-comparison table and project conclusion, document limitations and future work, then clean the repository and update the README with portfolio-ready project results.

## Nov. 15, 2026

### Worked On

Deep-Learning Tabular Prediction System — completed the final modeling notebook and project conclusions.

### Progress

- Finished the remaining sections of `11_final_model_selection_and_test_evaluation.ipynb`.
- Completed the final model-comparison table across the major project experiments.
- Documented the final selected model:
  - Phase 2 Histogram Gradient Boosting
- Summarized the final validation and test performance.
- Added the final project conclusion.
- Documented key findings from the full modeling process.
- Added project limitations.
- Added possible future-work directions.
- Completed the modeling and evaluation portion of the project.

### Final Project Findings

- Histogram Gradient Boosting remained the strongest model across the project.
- Phase 2 temporal feature engineering produced a modest improvement.
- The PyTorch neural network remained competitive but did not outperform HGB.
- Huber loss and reduced-dropout experiments did not improve the baseline neural network.
- The selected Phase 2 HGB model generalized well to the untouched future test period.
- Residual diagnostics showed that rare or rapidly changing high-consumption periods remain the primary source of forecasting difficulty.

### What I Learned

- Strong data science work is not about forcing the most complex model to win.
- Validation evidence should determine model selection.
- Controlled experiments make it easier to understand which changes actually improve performance.
- Feature engineering can provide measurable gains even when the underlying model remains unchanged.
- Final test evaluation should occur only after all model-selection decisions are complete.
- Residual diagnostics are important for understanding model weaknesses beyond aggregate metrics.
- A complete project should document not only results, but also limitations, failed experiments, and future directions.

### Project Status

The modeling portion of the Deep-Learning Tabular Prediction System is now complete.

The next phase is portfolio preparation rather than additional model tuning.

### Next Step

Clean the repository, organize final outputs, update the README with the project methodology and final results, and prepare the repository for presentation as a portfolio project.

## Nov. 16, 2026

### Worked On

Deep-Learning Tabular Prediction System — began final portfolio and repository cleanup.

### Progress

- Reviewed the project repository structure for presentation quality.
- Identified the main numbered project notebooks as the core modeling workflow.
- Separated the main project sequence from earlier PyTorch learning/foundation notebooks.
- Began preparing the repository for portfolio presentation rather than additional modeling work.
- Drafted a README project summary covering:
  - the forecasting problem
  - the UCI household power dataset
  - the chronological and leakage-aware methodology
  - the model families evaluated
  - the Phase 1 and Phase 2 comparison
  - the selected final model
  - the final test result
- Documented the final conclusion that Histogram Gradient Boosting outperformed the neural-network alternatives on this structured tabular forecasting task.

### Final Project Result

Selected model:

**Phase 2 Histogram Gradient Boosting**

Key results:

- Validation RMSE: `0.7867`
- Test RMSE: `0.6912`
- Phase 2 Neural Network RMSE: `0.7973`
- Phase 1 Neural Network RMSE: `0.7983`

The final model maintained strong performance on unseen future data and remained the preferred model based on the full experimental evidence.

### What I Learned

- Completing a data science project includes communicating the work clearly, not just producing a strong model.
- A README should explain the problem, data, methodology, results, and model-selection reasoning without requiring someone to inspect every notebook.
- Failed and neutral experiments are still valuable when they support a clear evidence-based conclusion.
- A strong portfolio project should make the experimental progression easy for another person to follow.
- Model complexity should not be treated as a goal by itself; the final choice should be justified by performance and practical tradeoffs.

### Next Step

Finish repository cleanup, organize notebooks and outputs, add key figures and metrics to the README, and prepare the repository for final portfolio presentation.

##### November 17, 2026

**Project:** 02 — Deep Learning for Satellite Image Classification  
**Notebook:** `01_computer_vision_fundamentals.ipynb`  
**Phase:** 1 — Computer Vision Foundations  
**Status:** In Progress

## Today's Progress

Today marked the official beginning of Project 02 in my 12-month data science and machine-learning portfolio development plan.

After substantially completing Project 01, which focused on deep learning for tabular prediction, I began transitioning into computer vision and image classification using PyTorch.

### 1. Project Initialization

Established the foundational structure for the new project.

- Created the `computer-vision-satellite-classification` project directory.
- Organized directories for notebooks, source code, datasets, trained models, evaluation results, and learning logs.
- Set up the GitHub repository and version-control workflow.
- Created the initial project documentation, including `README.md`, `.gitignore`, and `requirements.txt`.

### 2. Python Environment Configuration

Configured a dedicated Python virtual environment for Project 02 on the NVIDIA DGX Spark.

- Created and activated the `.venv` environment.
- Installed the necessary Python packages.
- Configured Jupyter to recognize the virtual environment.
- Verified that the environment and required libraries were functioning correctly.

### 3. Notebook 01 Initialization

Created `01_computer_vision_fundamentals.ipynb` in the project's `notebooks/` directory.

Added the introductory Markdown documentation outlining the notebook's purpose, learning objectives, hardware environment, and relationship to the larger satellite image classification project.

The notebook will introduce digital image representation, RGB channels, NumPy arrays, PyTorch image tensors, normalization, batch dimensions, and GPU operations.

## Skills Practiced

- Git and GitHub repository organization
- Machine-learning project structuring
- Python virtual environment management
- Dependency installation and environment configuration
- Jupyter notebook initialization
- Technical documentation
- Reproducible project setup

## Key Takeaways

This session focused primarily on establishing the infrastructure necessary for a well-organized and reproducible machine-learning project.

I now have a working development environment for exploring computer vision using PyTorch on the NVIDIA DGX Spark.

Project 02 will build upon the neural-network fundamentals developed during Project 01 while introducing image processing, convolutional neural networks, transfer learning, and computer vision model evaluation.

## Next Steps

1. Begin the technical exercises in Notebook 01.
2. Explore grayscale and RGB image representation using NumPy.
3. Visualize pixel values and individual color channels.
4. Convert image arrays into PyTorch tensors.
5. Practice normalization and tensor dimension manipulation.
6. Investigate image tensor operations using GPU acceleration.

## Session Summary

Successfully initialized Project 02, configured the development environment, and created the first notebook with its introductory documentation.

**Current milestone:** Project infrastructure completed; computer vision fundamentals ready to begin.

# November 18, 2026

**Project:** 02 — Deep Learning for Satellite Image Classification  
**Notebook:** `01_computer_vision_fundamentals.ipynb`  
**Phase:** 1 — Computer Vision Foundations  
**Status:** In Progress

## Today's Progress

Continued working on Project 02 of my 12-month data science and machine-learning portfolio development plan, focusing on the fundamentals of digital image representation and manipulation using NumPy and Matplotlib.

Completed Exercises 1 and 2 of Notebook 01, establishing a foundational understanding of how computers represent grayscale and RGB images as numerical arrays.

### 1. Grayscale Image Representation

Explored how grayscale images are represented as two-dimensional arrays containing pixel intensity values.

- Created a 3 × 3 grayscale image using NumPy.
- Examined pixel values ranging from 0 (black) to 255 (white).
- Investigated the `uint8` data type and its ability to represent 256 distinct intensity levels.
- Visualized grayscale pixel intensities using Matplotlib.
- Reinforced the relationship between array dimensions and image resolution.

### 2. RGB Image Representation

Introduced three-dimensional arrays for representing color images using red, green, and blue channels.

- Created a 100 × 100 RGB image using NumPy.
- Examined the `(height, width, channels)` array structure.
- Manipulated individual color channels through NumPy indexing.
- Experimented with RGB values to produce different colors.
- Visualized the resulting images using Matplotlib.
- Developed an understanding of how combinations of RGB intensity values determine pixel colors.

## Skills Practiced

- NumPy array creation and manipulation
- Multidimensional array indexing
- Understanding `uint8` image data
- Grayscale and RGB image representation
- Matplotlib image visualization
- RGB channel manipulation
- Interpreting image dimensions and shapes

## Key Takeaways

Digital images are numerical arrays in which individual pixel values determine the visual appearance of an image.

Grayscale images typically use two-dimensional arrays, while RGB images use three-dimensional arrays containing separate red, green, and blue channels.

The `uint8` data type commonly represents pixel intensities between 0 and 255.

NumPy provides a straightforward way to manipulate individual pixels and color channels, while Matplotlib allows these numerical representations to be visualized.

Understanding these concepts provides the foundation for working with image tensors and convolutional neural networks in PyTorch.

## Next Steps

1. Continue with Exercise 3: Understanding Image Channels.
2. Create an RGB image containing multiple colored regions.
3. Extract and visualize individual red, green, and blue channels.
4. Begin converting NumPy image arrays into PyTorch tensors.
5. Explore channel-first tensor formatting and pixel normalization.

## Session Summary

Completed the first two exercises of Notebook 01, developing foundational skills in grayscale and RGB image representation, NumPy indexing, and image visualization.

These concepts establish the groundwork for understanding how deep-learning models process visual information.

**Current milestone:** Notebook 01 — Exercises 1–2 completed; Exercise 3 next.

### November 20, 2026

**Project:** 02 — Deep Learning for Satellite Image Classification  
**Notebook:** `01_computer_vision_fundamentals.ipynb`  
**Phase:** 1 — Computer Vision Foundations  
**Status:** In Progress

## Today's Progress

Continued working on Project 02 of my 12-month data science and machine-learning portfolio development plan, focusing on understanding how RGB images store and represent color information.

Completed Exercise 3 of Notebook 01, exploring how individual color channels contribute to the appearance of a digital image.

### 1. Creating a Multicolor RGB Image

Used NumPy to create a synthetic RGB image containing four distinct colored regions.

- Initialized a 100 × 100 RGB image using `np.zeros()`.
- Used NumPy array slicing to divide the image into four quadrants.
- Assigned RGB intensity values to produce red, green, blue, and yellow regions.
- Visualized the resulting image using Matplotlib.
- Reinforced understanding of multidimensional array indexing and pixel manipulation.

### 2. Extracting Individual RGB Channels

Investigated how color information is distributed across the three channels of an RGB image.

- Extracted the red, green, and blue channels using NumPy indexing.
- Examined the structure of individual two-dimensional channel arrays.
- Visualized each channel separately using grayscale color maps.
- Observed how brighter regions correspond to higher channel intensity values.
- Explored how yellow results from combining high red and green intensities.

### 3. Visualizing Channel Contributions

Used Matplotlib subplots to compare the original RGB image with its individual channels.

This exercise demonstrated how three separate numerical arrays combine to produce a full-color image and how examining individual channels can reveal the underlying pixel information.

## Skills Practiced

- NumPy multidimensional array slicing
- RGB channel extraction and manipulation
- Working with three-dimensional image arrays
- Matplotlib subplot creation
- Grayscale visualization of individual color channels
- Understanding additive RGB color representation
- Interpreting pixel intensity values

## Key Takeaways

RGB images are represented by three channels containing red, green, and blue intensity values.

Each channel can be extracted and analyzed independently as a two-dimensional numerical array.

When displayed in grayscale, brighter areas indicate higher intensity values for the selected channel, while darker areas indicate lower intensity values.

Combining the information from all three channels produces the original full-color image.

These concepts are foundational to computer vision because convolutional neural networks process image data through numerical operations involving channels and spatial relationships.

## Next Steps

1. Begin Exercise 4: Converting NumPy Images to PyTorch Tensors.
2. Explore the differences between channel-last `(H, W, C)` and channel-first `(C, H, W)` representations.
3. Practice using `torch.from_numpy()` and `permute()`.
4. Investigate how image tensors are structured for convolutional neural networks.
5. Continue developing familiarity with PyTorch image-processing workflows.

## Session Summary

Completed Exercise 3 of Notebook 01, strengthening my understanding of RGB image representation, multidimensional array slicing, and individual color-channel visualization.

This session provided an important foundation for understanding how deep-learning models process visual information.

**Current milestone:** Notebook 01 — Exercises 1–3 completed; Exercise 4 next.

### November 21, 2026

**Project:** 02 — Deep Learning for Satellite Image Classification  
**Notebook:** `01_computer_vision_fundamentals.ipynb`  
**Phase:** 1 — Computer Vision Foundations  
**Status:** In Progress

## Today's Progress

Continued working on Project 02 of my 12-month data science and machine-learning portfolio development plan.

Completed Exercises 4 and 5 of Notebook 01, focusing on converting NumPy image arrays into PyTorch tensors and understanding pixel normalization.

These exercises built upon previous work with grayscale images, RGB channels, and multidimensional NumPy arrays, introducing important preprocessing techniques used in computer vision and deep learning.

### 1. Converting NumPy Images to PyTorch Tensors

Explored how image data can be converted from NumPy arrays into PyTorch tensors for use in deep-learning models.

- Used `torch.from_numpy()` to convert an RGB image into a PyTorch tensor.
- Examined the differences between NumPy's common channel-last `(H, W, C)` format and PyTorch's channel-first `(C, H, W)` format.
- Used `permute(2, 0, 1)` to rearrange tensor dimensions.
- Verified tensor shapes before and after permutation.
- Reinforced understanding of how tensor dimensions influence compatibility with neural-network architectures.

This exercise demonstrated an important distinction between conventional image processing and the input formats expected by PyTorch convolutional neural networks.

### 2. Pixel Normalization

Investigated how raw pixel intensity values are converted into floating-point values suitable for neural-network training.

- Examined the original `torch.uint8` image representation.
- Converted image tensors to `torch.float32` using `.float()`.
- Normalized pixel values by dividing by 255.
- Observed the transformation from the original `[0, 255]` intensity range to `[0, 1]`.
- Used `.min()`, `.max()`, and `.item()` to inspect tensor values.
- Reviewed how normalization helps establish consistent input scales for neural-network optimization.

Also introduced the concept of standardization using channel-specific means and standard deviations:

\[
x_{\text{normalized}} = \frac{x-\mu}{\sigma}
\]

This technique will become particularly relevant when working with pretrained computer vision models.

## Skills Practiced

- NumPy-to-PyTorch tensor conversion
- PyTorch tensor dimension manipulation
- Understanding channel-first and channel-last image formats
- Using `torch.from_numpy()` and `permute()`
- Converting tensor data types
- Pixel intensity normalization
- Inspecting tensor statistics
- Preparing image data for deep-learning models

## Key Takeaways

Digital image arrays must be organized into the appropriate tensor format before they can be processed by convolutional neural networks.

NumPy commonly represents RGB images using `(H, W, C)`, while PyTorch convolutional layers expect individual images in `(C, H, W)` format.

The `permute()` operation rearranges tensor dimensions without changing the underlying pixel values.

Raw image data is commonly stored using unsigned 8-bit integers, but neural networks generally operate on floating-point tensors.

Dividing pixel values by 255 converts standard 8-bit RGB intensity values into the `[0, 1]` range, establishing a consistent numerical scale for model inputs.

These preprocessing operations will form an essential part of the image classification pipeline used later in the project.

## Connection to Project 01

These exercises reinforced concepts previously explored during Project 01, particularly PyTorch tensor manipulation, data types, and preprocessing.

The primary difference is the structure of the input data.

In Project 01, neural networks processed tabular data consisting of numerical features. In Project 02, neural networks will process multidimensional image tensors containing both spatial and color-channel information.

This represents an important step toward understanding convolutional neural networks and learning features directly from images.

## Next Steps

1. Begin Exercise 6: Adding the Batch Dimension.
2. Understand the `(N, C, H, W)` tensor format.
3. Practice using `unsqueeze()` to add dimensions.
4. Complete Exercise 7: GPU Tensor Operations.
5. Transfer image tensors to the NVIDIA DGX Spark GPU.
6. Practice computing image statistics using PyTorch.
7. Continue toward completing Notebook 01.

## Session Summary

Completed Exercises 4 and 5 of Notebook 01, developing practical experience converting NumPy image arrays into PyTorch tensors, rearranging image dimensions, and normalizing pixel intensities.

These exercises strengthened the connection between fundamental image processing and the PyTorch workflows previously developed during Project 01.

**Current milestone:** Notebook 01 — Exercises 1–5 completed; Exercise 6 next.


### November 22, 2026

**Project:** 02 — Deep Learning for Satellite Image Classification  
**Notebook:** `01_computer_vision_fundamentals.ipynb`  
**Phase:** 1 — Computer Vision Foundations  
**Status:** Notebook 01 Completed

## Today's Progress

Continued working on Project 02 of my 12-month data science and machine-learning portfolio development plan.

Completed the remaining exercises in Notebook 01, focusing on image batching, GPU-accelerated tensor operations, and the representation of satellite-sized images in PyTorch.

This session built upon earlier exercises involving NumPy arrays, RGB image channels, tensor dimension manipulation, and pixel normalization.

### 1. Understanding Batch Dimensions

Explored how individual image tensors are organized into batches for processing by deep-learning models.

- Reviewed PyTorch's channel-first image representation `(C, H, W)`.
- Used `unsqueeze(0)` to introduce a batch dimension.
- Examined the resulting `(N, C, H, W)` tensor structure.
- Investigated how batch size, channel count, image height, and image width are represented.
- Connected image batching to the DataLoader workflows previously explored during Project 01.

This exercise established an understanding of the four-dimensional tensor format commonly expected by convolutional neural networks.

### 2. GPU Tensor Operations

Practiced using PyTorch to perform image tensor operations with GPU acceleration on the NVIDIA DGX Spark.

- Used `torch.cuda.is_available()` to verify CUDA availability.
- Selected a computation device using `torch.device()`.
- Transferred an image tensor to the selected device using `.to(device)`.
- Inspected tensor properties, including shape, data type, and device.
- Computed the mean pixel intensity of an image tensor.
- Used `.item()` to retrieve scalar tensor results.
- Calculated average intensity values for individual RGB channels.
- Practiced transferring computed results to the CPU for NumPy-based inspection.

This reinforced previous experience using GPU acceleration with PyTorch while applying those concepts to multidimensional image data.

### 3. Working with Satellite-Sized Image Tensors

Completed the final technical exercise by generating and inspecting synthetic image data with dimensions matching the RGB imagery used in the EuroSAT dataset.

- Set a random seed using `torch.manual_seed()` for reproducibility.
- Generated a batch of 16 synthetic RGB images
