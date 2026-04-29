# UK-Inflation-Forecasting-Retail-Pricing-Optimisation
## Overview

Retail businesses operate under constant pressure from macroeconomic volatility. This project addresses that challenge directly, combining **inflation forecasting** with **pricing optimisation** to give retailers a data-driven approach to protecting margins and stabilising revenue.

The pipeline ingests four macroeconomic datasets, engineers time-series features, trains and benchmarks three ML models, then feeds the best-performing forecasts into a pricing layer that simulates low, expected, and high inflation scenarios.



## Objectives

- Forecast UK inflation (CPI) using historical macroeconomic indicators
- Identify the key drivers of inflation through feature analysis
- Build a dynamic pricing model that adjusts to predicted CPI changes
- Simulate pricing scenarios to support retailer decision-making



## Dataset

Four publicly sourced macroeconomic time series, merged on date:

| Dataset | Description |
|---|---|
| `CPI_clean.csv` | Consumer Price Index (inflation baseline) |
| `interest_rates_clean.csv` | Bank of England base interest rates |
| `retail_sales_clean.csv` | UK retail sales volume index |
| `energy_prices_clean.csv` | UK wholesale energy price index |

All datasets were aligned to a common date index using an inner join, with forward-fill applied to handle sparse missing values.

---

##  Project Pipeline

```
Raw Data (4 sources)
      │
      ▼
Data Merging & Alignment
      │
      ▼
Feature Engineering
  ├── CPI lag (1-period, 3-period)
  ├── 3-period rolling mean (CPI, Retail Sales)
  └── Inflation Rate (% change in CPI)
      │
      ▼
Exploratory Data Analysis
  ├── Macroeconomic trend visualisation
  ├── Inflation volatility analysis
  └── Correlation heatmap
      │
      ▼
Model Training & Evaluation (80/20 time-series split)
  ├── Linear Regression
  ├── Random Forest Regressor
  └── XGBoost Regressor
      │
      ▼
Pricing Optimisation
  ├── CPI-linked dynamic price adjustment
  ├── Price elasticity of demand (ε = -0.5)
  └── Revenue simulation
      │
      ▼
Scenario Analysis
  ├── Low inflation  (-5% price band)
  ├── Expected       (baseline)
  └── High inflation (+5% price band)
```



##  Models

Three regression models were trained on an 80/20 chronological train/test split to preserve temporal order:

| Model | MAE | RMSE | Notes |
|---|---|---|---|
| **Linear Regression** | ✅ Best | ✅ Best | Captures the predominantly linear macro relationships |
| Random Forest | Higher | Higher | Overfits on the relatively small time series |
| XGBoost | Higher | Higher | Ensemble complexity not warranted by data structure |

**Key finding:** Linear Regression outperformed both ensemble methods, indicating that UK macroeconomic indicators have a strong linear relationship with CPI over the study period. Tree-based models were penalised by the limited dataset size and temporal structure.



## Pricing Optimisation

Using the best-performing model's CPI forecasts, prices are adjusted dynamically:

```python
# CPI-linked price adjustment
price = base_price * (1 + (predicted_CPI - mean_CPI) / mean_CPI)

# Demand via price elasticity
demand = 1000 * (price / base_price) ** elasticity  # elasticity = -0.5

# Revenue simulation
revenue = price * demand
```

**Findings from the pricing simulation:**
- Revenue remained stable and showed mild growth despite rising prices
- Incremental CPI-linked price changes avoided demand collapse
- The elasticity value of -0.5 confirmed that modest price increases are sustainable



##  Key Insights

1. **CPI and retail sales are positively correlated** — higher prices coincide with sustained consumer demand, not contraction
2. **Interest rates show a lagged inverse effect** on retail sales, consistent with monetary policy transmission
3. **Inflation is inherently volatile** — averages and linear trends alone are insufficient; dynamic modelling is required
4. **Lag features (CPI lag-1, lag-3) are strong predictors** of future inflation movement
5. **Scenario analysis confirms** that proactive, data-driven pricing outperforms reactive adjustments



## Repository Structure

```
uk-inflation-forecasting/
│
├── Inflation_Forecasting.ipynb   # Main project notebook
├── CPI_clean.csv                 # Consumer Price Index data
├── interest_rates_clean.csv      # Interest rate data
├── retail_sales_clean.csv        # Retail sales data
├── energy_prices_clean.csv       # Energy price data
├── requirements.txt              # Python dependencies
└── README.md                     # This file
```



##  Installation & Usage

**1. Clone the repository**
```bash
git clone https://github.com/your-username/uk-inflation-forecasting.git
cd uk-inflation-forecasting
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Launch the notebook**
```bash
jupyter notebook Inflation_Forecasting.ipynb
```



## 📦 Requirements

```txt
pandas>=2.0
numpy>=1.24
matplotlib>=3.7
seaborn>=0.12
scikit-learn>=1.3
xgboost>=1.7
jupyter>=1.0
```



## 📐 Methodology Notes

- **Train/test split** is chronological (80% train, 20% test) — random splitting would cause data leakage in time series
- **Forward-fill** is used for missing values to propagate last known observations, appropriate for monthly economic data
- **Rolling and lag features** are computed before the split to avoid leakage from test data into training features
- **Price elasticity** of -0.5 is a standard short-run estimate for UK retail; this can be adjusted per category



## 🔮 Future Work

- [ ] Incorporate ARIMA / SARIMA models for pure time-series forecasting
- [ ] Add Prophet (Facebook) for trend + seasonality decomposition
- [ ] Extend to category-level elasticity (food vs. electronics vs. clothing)
- [ ] Build an interactive Streamlit dashboard for scenario planning
- [ ] Integrate real-time ONS (Office for National Statistics) data feed



##  Author

**Nwabuisi Chizaram**  
Data Scientist · AI Automation Specialist · 




##  License

This project is licensed under the MIT License




