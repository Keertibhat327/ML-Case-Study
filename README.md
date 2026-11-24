# Air Quality Prediction Using Environmental Sensors

This machine learning case study estimates hourly carbon monoxide (CO) concentration in mg/m³ from environmental sensor readings. It compares Linear Regression, Decision Tree, Random Forest, and Gradient Boosting on the UCI Air Quality dataset.

**Main result:** Linear Regression achieved the lowest validation RMSE. On 1,102 held-out test hours, its mean absolute error was **0.341 mg/m³**, compared with **1.051 mg/m³** for a baseline that predicted the historical mean.

## Explore the project

- [Executed Jupyter notebook](./Air_Quality.ipynb) — code, data checks, plots, and model outputs.
- [Validation results](./results/validation_comparison.csv) — comparison of all four models and the baseline.
- [Test results](./results/test_metrics.csv) — final evaluation of the selected model and baseline.
- [Hourly predictions](./results/test_predictions.csv) — measured CO, estimated CO, and residual for each test hour.

## Dataset and prediction task

The [UCI Air Quality dataset](https://doi.org/10.24432/C59K5F) contains hourly measurements from an air quality monitoring device in an Italian city. Each prediction uses eight inputs from the same hour: five gas sensor responses, temperature, relative humidity, and absolute humidity. The target, `CO(GT)`, is the CO concentration measured by a reference analyzer.

The CSV contained **9,357 populated rows**. The analysis treated `-200` as a missing value and excluded 1,683 hours without a reference CO measurement and another 330 hours without any of the eight inputs. This left **7,344 usable hours**.

## Method

The observations were sorted by timestamp and split chronologically. This allowed the models to be evaluated on readings collected later than their training data.

| Subset | Hours | Purpose |
|---|---:|---|
| Training | 5,140 | Fit preprocessing and train the models |
| Validation | 1,102 | Compare models and select settings using RMSE |
| Test | 1,102 | Evaluate the selected model on the latest readings |

Each model used a pipeline with median imputation. Linear Regression also used standardized inputs. After model selection, the chosen pipeline was refitted on the combined training and validation data before the final test.

## Results

| Model | Validation MAE (mg/m³) | Validation RMSE (mg/m³) |
|---|---:|---:|
| Linear Regression | 0.4523 | **0.6859** |
| Random Forest | **0.4429** | 0.6909 |
| Gradient Boosting | 0.4643 | 0.7102 |
| Decision Tree | 0.5204 | 0.7554 |
| Mean baseline | 1.1825 | 1.5136 |

Linear Regression narrowly led on RMSE, the selection metric. Random Forest had the lowest validation MAE. On the held-out test period, Linear Regression achieved **RMSE = 0.4874 mg/m³** and **R² = 0.8567**.

![Observed versus estimated CO, residual errors, and predictions during the first test week](./results/test_diagnostics.png)

The model underestimated the three highest observed CO readings in the test period. At the highest reading, the instrument measured **7.50 mg/m³** and the model estimated **6.17 mg/m³**. The results describe performance at one monitoring site.

## Run the notebook

The analysis was run with Python 3.14.4. Open a terminal in the repository folder and run:

```bash
python -m pip install -r requirements.txt
python -m pip install jupyterlab
python -m jupyterlab Air_Quality.ipynb
```

In JupyterLab, select **Kernel → Restart Kernel and Run All Cells**. The notebook reads [`data/AirQualityUCI.csv`](./data/AirQualityUCI.csv) and writes the figures, comparison tables, hourly predictions, and fitted model to [`results/`](./results/).

To verify one prediction, open `results/test_predictions.csv` after the run. For **16 February 2005 at 14:00**, the measured CO concentration was **1.50 mg/m³** and the model estimated **1.59 mg/m³**.

## Dataset citation

S. Vito (2008). *Air Quality* [Dataset]. UCI Machine Learning Repository. [https://doi.org/10.24432/C59K5F](https://doi.org/10.24432/C59K5F)
