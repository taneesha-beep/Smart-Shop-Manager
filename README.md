# 🛍️ SmartMLShop — AI-Powered Retail Management System

A full-stack retail management web application built with **Flask** and **Machine Learning**, featuring real-time sales forecasting, inventory optimisation, profit prediction, and role-based dashboards for managers and employees.

---

## 📌 Overview

Most ML-based retail forecasting systems predict demand, sales, and profit independently using the same input features. SmartMLShop instead implements a **multi-stage causal pipeline** that mirrors how retail actually works: demand drives sales, and sales drives profit. Each stage's prediction becomes an input feature for the next, producing significantly higher accuracy at the sales and profit stages than independent multi-output regression would achieve.

---

## 👩‍💻 My Contributions

This was a team project. My specific contributions:

- **Designed the multi-stage causal prediction pipeline** — the core architectural decision that differentiates this system. Rather than predicting demand, sales, and profit independently, I chained the three models so that demand predictions feed into the sales model and sales predictions feed into the profit model. This reflects the actual causal structure of retail operations and is why sales R²=0.9823 and profit R²=0.9948 despite demand only achieving R²=0.6273.
- **Conducted the full model comparison** — systematically trained and evaluated 6 regression algorithms (Gradient Boosting, Random Forest, XGBoost, LightGBM, Linear Regression, SVR) under identical preprocessing and evaluation conditions, then selected Gradient Boosting based on generalisation performance rather than training accuracy.
- **Ran GridSearchCV hyperparameter tuning** — exhaustive search across estimator count, tree depth, min samples split, and min leaf size with 5-fold cross-validation to arrive at the final configuration (150 estimators, depth 8, min split 5, min leaf 2).

---

## ✨ Features

- **Sales Forecasting** — Gradient Boosting models predict future revenue based on historical trends, seasonality, and demand patterns
- **Demand Forecasting** — Daily demand predictions with lag features, rolling averages, and event-driven spikes
- **Profit Optimisation** — ML-driven profit prediction using cost structure and margin context
- **Inventory Intelligence** — Reorder point calculations, safety stock recommendations, and stock status alerts
- **Role-Based Dashboards** — Separate views for managers (store-wide analytics) and employees (personal performance)
- **Demo Mode** — Fully functional without trained models using deterministic synthetic data

---

## 🤖 ML Pipeline

Three Gradient Boosting regressors trained in sequence:

**Stage 1 — Demand Model**
Input: time-based features, lag features (lag_1_day, lag_7_day, rolling_7_day_avg), trend index, event spikes
Output: daily unit demand prediction

**Stage 2 — Sales Model**
Input: all Stage 1 features + `demand_pred` (propagated from Stage 1)
Output: daily revenue prediction

**Stage 3 — Profit Model**
Input: all Stage 2 features + `sales_pred` + cost + margin_hint
Output: daily profit prediction

### Final Model Performance

| Stage  | Target          | R² Score |
| ------ | --------------- | -------- |
| Demand | Daily units     | 0.6273   |
| Sales  | Daily revenue   | 0.9823   |
| Profit | Daily profit    | 0.9948   |

### Model Comparison (Demand R² — hardest target)

| Model             | Demand R² | Notes                              |
| ----------------- | --------- | ---------------------------------- |
| Gradient Boosting | **0.6273**| Best generalisation — selected     |
| Linear Regression | 0.5765    | Limited nonlinear capacity         |
| Random Forest     | 0.5494    | Overfitting observed               |
| LightGBM          | 0.5365    | Strong but slightly weaker on demand |
| XGBoost           | 0.5320    | Competitive, requires careful tuning |
| SVR               | -1.5614   | Poor on structured retail data     |

---

## 🖥️ Tech Stack

| Layer    | Technology                                          |
| -------- | --------------------------------------------------- |
| Backend  | Python 3, Flask, Flask-SQLAlchemy, Flask-CORS       |
| ML       | scikit-learn (Gradient Boosting), pandas, NumPy, joblib |
| Database | SQLite                                              |
| Frontend | HTML5, Bootstrap 5, Chart.js, Font Awesome          |
| Auth     | Session-based with SHA-256 hashed passwords         |

---

## 📁 Project Structure

```
smartmlshop/
├── app/
│   ├── app.py                  # App factory
│   ├── auth.py                 # Authentication blueprint
│   ├── routes.py               # Route handlers & API endpoints
│   ├── models.py               # SQLAlchemy models
│   ├── demo_data.py            # Demo data seeding
│   ├── generate_data.py        # Training data CSV generator
│   └── templates/
│       ├── manager_dashboard.html
│       ├── employee_dashboard.html
│       └── product_management.html
├── src/
│   ├── preprocessing.py        # Feature engineering & data prep
│   ├── forecast_engine.py      # Multi-stage forecast logic
│   ├── train_model.py          # Model training orchestration
│   └── demo_synthetic.py       # Synthetic forecasting fallback
├── scripts/
│   └── train.py                # Training entry point
├── data/
│   └── raw/shop_data.csv
├── models/                     # Saved .pkl model files (git-ignored)
├── config.py
├── run.py
└── requirements.txt
```

---

## 🚀 Getting Started

```bash
git clone https://github.com/taneesha-beep/Smart-Shop-Manager.git
cd Smart-Shop-Manager

python3 -m venv venv
source venv/bin/activate

pip install -r requirements.txt

# Generate data and train models
python3 app/generate_data.py
mv shop_data.csv data/raw/shop_data.csv
PYTHONPATH=. python3 scripts/train.py

# Run the app
PYTHONPATH=. python3 run.py
```

Open **http://localhost:5001**

> Skip the training step to run in demo mode with synthetic data.

---

## 🔐 Demo Credentials

| Role     | Username  | Password     |
| -------- | --------- | ------------ |
| Manager  | `manager` | `manager123` |
| Employee | `alice`   | `emp123`     |
