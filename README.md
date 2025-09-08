# Rossmann Sales Forecasting

This project applies **time series forecasting** on the [Rossmann Store Sales dataset](https://www.kaggle.com/competitions/rossmann-store-sales) to predict sales and extract business insights.  
It demonstrates the data science workflow: **EDA → Modeling (ARIMA, SARIMA, Prophet) → Validation → Insights**

---

## 📊 Project Overview
- **Objective:** Forecast sales for Rossmann drugstores and analyze the impact of promotions and holidays 
- **Techniques:** ARIMA, SARIMA, Prophet (with regressors)  
- **Tools:** Python, Pandas, Statsmodels, Prophet, Matplotlib  
- **Granularity:** Store-level, weekly resampling (focus on Store 1 for demonstration)  
- **Validation:** Last 6 weeks held out as test set

---

## 🔑 Workflow
1. **Data Preparation**
   - Merged `train.csv` and `store.csv`  
   - Removed closed days (`Open=0`) and zero-sales days  
   - Imputed missing values (e.g., `CompetitionDistance` by median) 
   - Cast categorical columns appropriately  

2. **Exploratory Data Analysis**
   - Seasonal decomposition → downward trend, yearly Christmas spike, weekend peaks  
   - Augmented Dickey-Fuller test → stationary series  
   - Promotion uplift analysis → ~40% higher sales during promos  
   - Sales distribution → right-skewed, normalized by log transform  

3. **Modeling**
   - **ARIMA(1,0,1):** Flat forecasts, weak baseline  
   - **SARIMA(1,0,1)(1,1,1,52):** Best accuracy, captured yearly seasonality  
   - **Prophet:** Added regressors (Promo, SchoolHoliday); interpretable trend, weekly & yearly cycles  

4. **Validation**
   - Compared models on last 6 weeks (Store 1)  

---

## 📈 Results

| Model      | RMSE  | MAE   | MAPE   | RMSPE  |
|------------|-------|-------|--------|--------|
| ARIMA      | 714.5 | 616.9 | 15.36% | 18.57% |
| SARIMA     | 184.1 | 152.3 |  3.42% |  4.07% |
| Prophet    | 289.5 | 217.3 |  4.77% |  6.13% |

- **SARIMA** gave the best accuracy (lowest errors) 
- **Prophet** provided strong accuracy + interpretable components (trend, weekly cycle, promo uplift)  
- **ARIMA** served as a baseline

---

## 💡 Key Insights
- Sales trend is **declining** between 2013–2015  
- **Strong yearly seasonality**: sales spike in December/January  
- **Weekly pattern**: weekends much stronger than midweek  
- **Promotions boost sales by ~40% on average**  
- School holidays also increase demand  

---

## 🛠 Tech Stack
- **Language:** Python  
- **Libraries:** pandas, numpy, matplotlib, statsmodels, prophet, scikit-learn  
- **Environment:** Jupyter Notebook  
---

## 🚀 Next Steps
- Extend beyond Store 1 → multi-store modeling 
- Deploy forecasts with **Tableau dashboard** or a simple **Flask/Streamlit app**  
- Explore advanced models (XGBoost, DeepAR, LSTMs) 

---

## 📌 About
This project was built as part of my **Data Science portfolio** to demonstrate applied time series forecasting, model evaluation, and business insights communication
