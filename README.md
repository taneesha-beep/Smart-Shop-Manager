# 🛍️ SmartMLShop — AI-Powered Retail Management System

A full-stack retail management web application built with **Flask** and **Machine Learning**, featuring real-time sales forecasting, inventory optimization, profit prediction, and role-based dashboards for managers and employees.

---

## ✨ Features

- **Sales Forecasting** — Random Forest models predict future revenue based on historical trends, seasonality, and demand patterns
- **Demand Forecasting** — Time-series style daily demand predictions with lag features and rolling averages
- **Profit Optimization** — ML-driven profit prediction using cost structure and margin context
- **Inventory Intelligence** — Reorder point calculations, safety stock recommendations, and stock status alerts
- **Role-Based Dashboards** — Separate views for managers (store-wide analytics) and employees (personal performance)
- **Product Management** — Per-product analysis with historical and forecast charts
- **Demo Mode** — Fully functional without trained models using deterministic synthetic data

---

## 🖥️ Tech Stack

| Layer    | Technology                                          |
| -------- | --------------------------------------------------- |
| Backend  | Python 3, Flask, Flask-SQLAlchemy, Flask-CORS       |
| ML       | scikit-learn (Random Forest), pandas, NumPy, joblib |
| Database | SQLite                                              |
| Frontend | HTML5, Bootstrap 5, Chart.js, Font Awesome          |
| Auth     | Session-based with SHA-256 hashed passwords         |

---

## 📁 Project Structure

```
smartmlshop/
├── app/                        # Flask application package
│   ├── app.py                  # App factory
│   ├── auth.py                 # Authentication blueprint
│   ├── routes.py               # Route handlers & API endpoints
│   ├── models.py               # SQLAlchemy models
│   ├── demo_data.py            # Demo data seeding
│   ├── generate_data.py        # Training data CSV generator
│   ├── static/
│   │   ├── css/styles.css
│   │   └── js/scripts.js
│   └── templates/
│       ├── base.html
│       ├── index.html
│       ├── login.html
│       ├── manager_dashboard.html
│       ├── employee_dashboard.html
│       └── product_management.html
├── src/                        # ML pipeline
│   ├── preprocessing.py        # Feature engineering & data prep
│   ├── forecast_engine.py      # Time-series forecast logic
│   ├── insights.py             # Demand trend & inventory intelligence
│   ├── predict.py              # Inference helpers
│   ├── train_model.py          # Model training orchestration
│   └── demo_synthetic.py       # Synthetic forecasting (no-model fallback)
├── scripts/
│   └── train.py                # Training entry point
├── data/
│   └── raw/shop_data.csv       # Generated training data
├── models/                     # Saved .pkl model files (git-ignored)
├── instance/                   # SQLite DB (git-ignored)
├── config.py                   # Central configuration
├── run.py                      # App entry point
└── requirements.txt
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.9+
- pip

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/smartmlshop.git
cd smartmlshop

# 2. Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install -r requirements.txt
```

### Generate Training Data & Train Models

```bash
# Generate the CSV dataset
python3 app/generate_data.py
mv shop_data.csv data/raw/shop_data.csv

# Train ML models (saves .pkl files to models/)
PYTHONPATH=. python3 scripts/train.py
```

> **Skip this step** if you just want to run the app — it works without trained models using the built-in synthetic demo engine.

### Run the App

```bash
PYTHONPATH=. python3 run.py
```

Open **http://localhost:5001** in your browser.

---

## 🔐 Demo Credentials

| Role     | Username  | Password     |
| -------- | --------- | ------------ |
| Manager  | `manager` | `manager123` |
| Employee | `alice`   | `emp123`     |
| Employee | `bob`     | `emp123`     |
| Employee | `carol`   | `emp123`     |
| Employee | `david`   | `emp123`     |
| Employee | `emma`    | `emp123`     |

---

## 🤖 ML Models

Three Random Forest regressors are trained in sequence:

1. **Demand Model** — Trained on daily store-level time-series with lag and rolling features; uses a time-based train/test split to avoid data leakage
2. **Sales Model** — Trained on `[unit_price, category, demand_pred, trend]`; demand predictions from model 1 are chained as input features
3. **Profit Model** — Trained on `[sales_pred, cost, margin_hint, ...]`; uses sales predictions from model 2 plus cost structure features

### Model Performance (typical)

| Model  | MAE       | R²    |
| ------ | --------- | ----- |
| Demand | ~15 units | ~0.91 |
| Sales  | ~$120     | ~0.88 |
| Profit | ~$45      | ~0.86 |

---

## 📊 Dashboards

**Manager Dashboard**

- Today's revenue, profit, transaction count, and average order value
- 30-day daily sales trend chart
- Category sales distribution (pie chart)
- Top 10 products by revenue

**Employee Dashboard**

- Personal sales performance and ranking
- Individual transaction history

**Product Management**

- Select any product to view 30-day historical demand, sales, and profit charts
- 30-day demand and sales forecast
- Inventory status with reorder recommendations

---

## ⚙️ Configuration

All settings are in `config.py`:

```python
PORT = 5001
DEBUG = True
SECRET_KEY = 'your-secret-key'   # Change this in production
```

---

## 🙈 .gitignore Recommendations

Add these to your `.gitignore`:

```
venv/
instance/
models/*.pkl
data/raw/
__pycache__/
*.pyc
users.json
```
