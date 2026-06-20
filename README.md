# Energy Market Forecasting & Policy Impact Analysis

> **MS Capstone Project - Rutgers University, December 2025**
> Supervisor: Prof. Yaqing Chen · M.S. Data Science (Statistics Track)

Compositional time-series forecasting of U.S. electricity generation mix across **all 50 states + DC** using Centered Log-Ratio (CLR) transformation and Vector Autoregression (VAR) with AIC-based lag selection, benchmarked against a naive persistence baseline (1990–2023).

---

## 🔍 The Problem

Electricity generation shares - Natural Gas, Other Fossil Fuels (coal + petroleum), and Nuclear & Renewables - are **compositional**: they always sum to 100%. This violates the independence assumption of standard multivariate regression and forecasting models, which would produce nonsensical outputs (e.g. shares summing to 150%).

Standard approaches fail here. This project applies **compositional data analysis** methods - specifically the Centered Log-Ratio (CLR) transformation - to map shares to an unconstrained space where VAR can operate correctly.

---

## 📐 Methodology

```
EIA Annual Generation Data (1990–2023)
         │
         ▼
Aggregate into 3 fuel groups
(Natural Gas · Other Fossil · Nuclear & Renewables)
         │
         ▼
Zero Replacement (x = max(x, 1e-6))
         │
         ├─────────────────────────┐
         ▼                         ▼
CLR Transformation          Raw Shares
(theoretically correct)     (practical fallback)
         │                         │
         ▼                         ▼
  VAR Model (per state)    VAR Model (per state)
  AIC lag selection        AIC lag selection
         │                         │
         └──────────┬──────────────┘
                    ▼
    One-step-ahead forecast for 2023
                    │
                    ▼
         MAE vs Persistence Baseline
```

### Why CLR?
The CLR transform maps a composition (x₁, x₂, x₃) to an unconstrained space:
`CLR(xᵢ) = ln(xᵢ / geometric_mean(x₁, x₂, x₃))`

This allows VAR to model interdependencies between fuel types without violating the simplex constraint.

### Why VAR?
The three fuel categories are not independent - when natural gas share rises, fossil fuels typically decline. VAR captures these cross-series lagged dependencies jointly. AIC-based lag selection lets each state choose its own optimal model complexity.

---

## 📊 Key Results

| Model | Natural Gas MAE | Other Fossil MAE | Nuclear & Renewables MAE |
|-------|:--------------:|:----------------:|:------------------------:|
| Raw VAR | 19.3 | 21.8 | 42.1 |
| **Persistence Baseline** | **3.1** | **4.9** | **9.8** |

> **CLR-VAR** estimation succeeded for only a subset of states due to zero-heavy compositions causing numerical instability (rank-deficient matrices). Results shown for Raw VAR for full state coverage.

### Interpretation

The persistence baseline (last observation carried forward) substantially outperforms both VAR models across all fuel categories. This is a **meaningful finding, not a failure**: annual electricity shares change slowly for most states, making persistence a very strong one-step-ahead predictor with only 33 annual observations per state.

This implies that improving on persistence requires **exogenous variables** - Renewable Portfolio Standards, fuel price shocks, carbon regulations - rather than additional model complexity alone.

---

## 🗺️ Lag Order Distribution

AIC-selected lag orders varied meaningfully across states, reflecting heterogeneous energy transition dynamics:
- Most states: lag 0–1 (slow-changing, low-complexity systems)
- Active transition states (e.g. high RPS): lag 3–5 (more historical dependence)

---

## 📁 Repository Structure

```
├── energy_mix_forecasting_clr_var.ipynb   # Main pipeline
├── annual_generation_state.csv            # EIA source data (all states, 1990–2023)
├── Final_Project_Report.pdf               # Full research report with methodology
└── README.md
```

---

## 🚀 How to Run

```bash
# Clone the repo
git clone https://github.com/rohithdonthi/Energy-Market-Forecasting-and-Policy-Impact-Analysis.git
cd Energy-Market-Forecasting-and-Policy-Impact-Analysis

# Install dependencies
pip install statsmodels pandas numpy matplotlib scipy

# Run the notebook
jupyter notebook energy_mix_forecasting_clr_var.ipynb
```

---

## 🔭 Future Work

- **Exogenous covariates:** Incorporate state-level Renewable Portfolio Standard (RPS) policies, natural gas prices, and carbon regulations to capture policy-driven energy transitions
- **Alternative transforms:** ILR (Isometric Log-Ratio) and ALR (Additive Log-Ratio) as alternatives to CLR
- **Bayesian VAR:** Handle short time series more robustly with prior information
- **Multi-step forecasting:** Extend from 1-year to 5-year horizon

---

## 📚 References

- Aitchison, J. (1986). *The Statistical Analysis of Compositional Data*. Chapman & Hall.
- Lütkepohl, H. (2005). *New Introduction to Multiple Time Series Analysis*. Springer.
- EIA: U.S. Energy Information Administration, Annual Electric Power Industry Report

---

## 📄 License

MIT License - see [LICENSE](LICENSE) for details.

---

<div align="center">
<i>Part of MS Data Science capstone research — Rutgers University, December 2025</i>
</div>
