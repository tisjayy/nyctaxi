# NYC Taxi Demand Forecasting & Anomaly Detection

This repo contains **two related but separate projects** on NYC taxi trips:  

1. [Demand Forecasting](#demand-forecasting) – Predicting pickup demand using the **NYC TLC Yellow Cab dataset (2015–2016)**.  
2. [Anomaly Detection](#anomaly-detection) – Detecting unusual demand periods using the **NYC Taxi dataset (2014–2015)** with LSTM confidence intervals.  

Both projects help understand urban mobility, optimize resource allocation, and detect unusual behavior (e.g., holidays, snowstorms, lockdowns).

---

## Table of Contents
- [Data Sources](#data-sources)
- [Demand Forecasting](#demand-forecasting)
- [Anomaly Detection](#anomaly-detection)
- [Tech Stack](#tech-stack)
- [How to Run](#how-to-run)
- [Results](#results)

---

## Data Sources
- **Demand Forecasting**:  
  NYC TLC Yellow Cab trip records (Jan 2015, Jan–Mar 2016).  
  Includes pickups, drop-offs, fares, passenger counts, and timestamps.  

- **Anomaly Detection**:  
  NYC Taxi Dataset (2014-2015).  
  Demand time series from **2014-07-01 to 2015-01-31**, one value every 30 minutes.  
  Known anomalies: NYC marathon, Thanksgiving, Christmas, New Year’s, Snowstorm.  

---

## Demand Forecasting
We forecast taxi pickup demand by clustering locations and predicting the next 10-minute bin’s demand.  

### Preprocessing
- Handled missing values and outliers (duration, speed, fares).  
- Clustered pickup locations with **MiniBatchKMeans (K=40)**.  
- Generated **time-lag features** (ft_1 to ft_5), **exp_avg**, cluster coordinates, and weekday.  

### Model
- **XGBoost Regressor** trained on 2016 cumulative time series.  
- Chronological 70–30 train/test split.  

### Evaluation
- Metric: **MAPE (Mean Absolute Percentage Error)**.  
- Results:  
  - Train MAPE: ~14%  
  - Test MAPE: ~13.4%  
- Shows strong generalization, predictions within ~13% error on average.
### PowerBI dashboard for Jan 2016 (currently in progress)
<img width="1321" height="745" alt="Screenshot 2025-07-28 143044" src="https://github.com/user-attachments/assets/cb419a40-948e-4106-b3ca-60cfccd1fbac" />

[⬆ Back to Top](#nyc-taxi-demand-forecasting--anomaly-detection)

---

## Anomaly Detection
We detect anomalies in NYC taxi demand using **LSTM quantile regression with confidence intervals**.  

### Approach
- Input: Past 48 half-hour observations (daily window).  
- Model: **Bidirectional LSTM with quantile outputs** (10th, 50th, 90th).  
- Trick: Used **Monte Carlo dropout bootstrapping** to stabilize quantile predictions & avoid crossover.  

### Key Idea
- If predicted **[90th–10th quantile interval]** is **narrow**, model is confident → normal behavior.  
- If the interval is **wide**, demand is unpredictable → anomaly.  

### Results
- **Great forecasting accuracy** (MSLE ≈ 0.055).  
- Correctly flagged anomalies:
  - NYC Marathon  
  - Thanksgiving  
  - Christmas  
  - New Year’s Eve/Day  
  - Snowstorm  

[⬆ Back to Top](#nyc-taxi-demand-forecasting--anomaly-detection)

---

## Tech Stack
- **Data Processing**: PySpark, Dask, Pandas, NumPy  
- **Forecasting**: scikit-learn, XGBoost  
- **Deep Learning**: TensorFlow / Keras (LSTM, quantile regression)  
- **Visualization**: Matplotlib, Seaborn, Plotly, Folium  

---
