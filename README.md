# Electricity Consumption Forecasting

- **Author:** Abraham Ibitowa
- **Date:** 2025-09-01

---

## Project Overview

This project aims to **forecast electricity consumption** on the day of **2010-02-21** using statistical and machine learning models. The dataset contains 15-minute interval electricity consumption and outdoor temperature from **2010-01-01 01:15** to **2010-02-20 23:45**, with temperature also available for the forecast date.


---

## Data

**Snapshot of dataset:**  
![Elec_Train_Dataset](./images/Elec_Train_Dataset.png)  

**Index:**
- `Timestamp`: Date-time of measurement
- `Power (kW)`: Electricity consumption
- `Temp (C°)`: Outdoor temperature

---

## Workflow

### 1. Preprocessing & Feature Engineering
- Convert timestamps to `POSIXct`.
- Handle mixed timestamp formats.
- Filter training data up to `2010-02-20 23:45`.
- Create time series objects with frequency 96 (15-min intervals).
- Generate calendar features: hour of day, day of week.
- Compute average daily consumption profiles.

---

### 2. Exploration & Visualization

**Average Daily Consumption Profile:**  
![Average Daily Profile](./images/avg_daily_profile.png)  

**Interpretation:**
- Baseload overnight (~150 kW)
- Morning ramp-up (05:00 - 08:00)
- Daytime stable load (~250 kW)
- Evening peak (>300 kW)
- Evening gradually drops back to the baseload

**Full Historical Consumption:**  
![Full Historical](./images/full_history.png)  

**Interpretation:**
-The graph presents a daily seasonality with one unexpected drop on 18 february.
-Overall the time series exhibits a strong, predictable seasonal pattern.

---

### 3. Modeling

**Baseline Models:** Linear Regression + Fourier, SES, Holt, Holt-Winters additive  
**Statistical Models:** ARIMA (auto), SARIMA (manual), SARIMAX (temperature covariates)  
**Machine Learning Models:** NNETAR (temperature + quadratic), XGBoost (lagged consumption + temperature)  

---

### 4. Models Evaluation

**Evaluation Metrics:**

**RMSE:**
RMSE penalizes large errors more heavily than smaller ones, making it appropriate for electricity consumption where extreme deviations can be costly or critical.

**AIC:**
Quantifies model goodness-of-fit while penalizing complexity. Lower AIC indicates a model that balances accuracy with parsimony. Only applicable for certain models.

**BIC:**
Useful to compare models with different numbers of parameters, especially for large datasets. Like AIC, BIC is only defined for likelihood-based models.


| Model            | RMSE   | AIC      | BIC      |
| ---------------- | ------ | -------- | -------- |
| NNETAR + Temp    | 7.614  | NA       | NA       |
| SARIMA Manual    | 8.846  | 35475.10 | 35514.07 |
| HW Additive      | 9.821  | NA       | NA       |
| XGBoost + Temp   | 11.167 | NA       | NA       |
| SARIMAX + Temp   | 11.590 | 37218.57 | 37276.85 |
| ARIMA Auto       | 11.782 | 37368.01 | 37400.39 |
| SES              | 15.612 | NA       | NA       |
| Holt             | 15.613 | NA       | NA       |
| LM Trend+Fourier | 20.709 | 43543.33 | 43601.78 |


**Key Insights:**
- Baseline models have modest performance.
- Statistical models show improved accuracy.
- NNETAR with temperature has the best performance (lowest RMSE).
- XGBoost also outperforms most classical models (Baseline and Statistical).

---

### 5. Side-by-Side Forecast Visualization

**Comparison of the 4 Best Models:**  
![Forecast Comparison](./images/forecast_comparison.png)


---

### 6. Forecast Export

The forecast from the **best model (NNETAR + Temp)** is exported to Excel:   
`NNETAR_Forecast_2010_02_21.xlsx`
