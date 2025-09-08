# Rossmann Sales Forecasting — Detailed Project Report

## 1. Introduction
This project explores **time series forecasting** using the **Rossmann Store Sales dataset** (Kaggle).  
We focus on:
- Cleaning and preparing the data  
- Exploring seasonal and calendar effects  
- Benchmarking three forecasting models: **ARIMA, SARIMA, and Prophet**  
- Evaluating accuracy on a validation period  
- Extracting **business insights** (promo uplift, seasonality, trends)

---

## 2. Data Overview
- **train.csv** → Daily sales, promo, holiday, and other store-specific info  
- **store.csv** → Metadata for each store (store type, assortment, competition info, etc)  
- We merged these into a single DataFrame `df`

### Shapes
- `train`: (1017209, 9)  
- `store`: (1115, 10)

---

## 3. Data Cleaning & Preparation

### 3.1 Sanity checks
We confirmed key columns exist in both datasets:
- assert {"Store","Date","Sales","Open"}.issubset(train.columns)
- assert {"Store","StoreType","Assortment"}.issubset(store.columns)

### 3.2 Filtering
- Removed closed days (`Open=0`) and days with `Sales=0`
- Resulting working dataset: `df_train`

### 3.3 Handling missing values
- `CompetitionDistance`: imputed with median per `StoreType`, fallback global median  
- “Since” fields (competition/promo start): filled with `0` → “not applicable”  
- `PromoInterval`: missing → `"None"`  
- Categorical columns (StoreType, Assortment, etc) cast to `category`

### 3.4 Outliers
We defined a function to cap extreme outliers using log + IQR, but Store 1 showed no suspicious spikes, so we skipped this step

---

## 4. Exploratory Data Analysis (EDA)

### 4.1 Trend, seasonality, decomposition
- Resampled Store 1’s daily sales into weekly averages 
- Applied **seasonal decomposition (period=52 weeks)**  
  - **Trend**: gradual decline across 2013–2015  
  - **Seasonality**: strong annual spikes at year-end (Christmas/New Year)  
  - **Residuals**: random shocks from promotions or events

### 4.2 Stationarity (ADF test)
- ADF test statistic = -3.76, p-value = 0.003 < 0.05 → Stationary  
- → No differencing required for ARIMA

### 4.3 Calendar features
Extracted Year, Month, Week, DayOfWeek, Weekend flag, Quarter  

### 4.4 Store summaries
- Top stores averaged ~18–21.5k daily sales, running promos ~45% of the time  
- Store 1 = ~5k weekly mean sales

### 4.5 Promotion uplift
- On average, promotions boosted sales by **~40%** (median ~36%)  
- Some cases >100% uplift

### 4.6 Holidays
- School holidays and state holidays showed measurable impact (store/weekday dependent)

### 4.7 Sales distribution
- Raw sales = right-skewed  
- After log transform, distribution became symmetric (better for modeling)  
- Correlation with customers = 0.824 → strong predictor

---

## 5. Modeling

### 5.1 Baseline ARIMA(1,0,1)
- Captured mean level (~4778) but forecasts were flat  
- Residuals had autocorrelation and fat tails  
- **AIC = 2201**

### 5.2 Seasonal ARIMA (SARIMA(1,0,1)(1,1,1,52))
- Added yearly seasonal terms  
- Forecast less flat, better match to cycles
- **AIC = 455 (much lower)**
- Residuals improved, though still fat-tailed

### 5.3 Prophet (with regressors)
- Daily → resampled to weekly for comparison 
- Added **Promo** and **SchoolHoliday** as regressors
- Component plots showed:
  - Trend decline over years  
  - Weekly cycle: peaks on weekends, trough midweek  
  - Yearly cycle: December spikes  
  - Promo uplift: +1000–1500 sales per week on average 

---

## 6. Model Evaluation

Validation = last 6 weeks of Store 1

| Model      | RMSE  | MAE   | MAPE   | RMSPE  |
|------------|-------|-------|--------|--------|
| ARIMA      | 714.5 | 616.9 | 15.36% | 18.57% |
| SARIMA     | 184.1 | 152.3 |  3.42% |  4.07% |
| Prophet    | 289.5 | 217.3 |  4.77% |  6.13% |

- **SARIMA** = most accurate  
- **Prophet** = interpretable, competitive accuracy, strong performance with regressors  
- **ARIMA** = weak baseline

---

## 7. Business Insights
- Sales show a **gradual decline** from 2013–2015  
- **Seasonality**: strong year-end spikes; weekend peaks vs midweek troughs  
- **Promotions** boost weekly sales by ~40%  
- **School holidays** also lift sales  
- Customers strongly predict sales (correlation 0.82)  

---

## 8. Conclusion
- SARIMA is the best classical forecasting model for accuracy  
- Prophet offers interpretable components and business insights, making it ideal for communication with stakeholders  
- This project demonstrates **end-to-end time series forecasting**: cleaning, EDA, stationarity checks, decomposition, ARIMA/SARIMA, Prophet, validation metrics, and insights
