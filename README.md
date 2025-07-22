# 🔐 Aave V2 Wallet Credit Scoring 

This project builds a transparent credit scoring pipeline for DeFi wallets based solely on historical transaction behavior on the **Aave V2 protocol**.

Scores range from **0 (very risky)** to **1000 (very responsible)** — with no KYC or off-chain data.

---

## 📌 Objective

> Assign a credit score to each Aave wallet based **only on interaction history**, reflecting its reliability (repayments, usage patterns) and risk level (liquidations, short lifecycles, potential bot behavior).

---

## 🧠 Chosen Method

We use a **supervised machine learning approach** with soft pseudo-labels derived from behavioral rules, trained with an **explainable model (XGBoost)**:

- 🏷️ **Labels**: Auto-generated based on poor repayment and high liquidation rate.
- 💡 **Features**: Engineered from transaction types like deposits, repays, borrows, redemptions, and liquidations.
- 📊 **Model**: XGBoost (tree-based) trained to classify wallets as risky or not.
- 📈 **Score mapping**: Output risk probability → scaled to credit score using:
  `credit_score = int((1 - risk_probability) * 1000)`
- 🧠 **Explainability**: Each score is paired with SHAP (Shapley) values to show feature impacts per wallet.

---

## 🏗️ Architecture

```
              +---------------------+
              |  sample.json (Aave) |
              +---------------------+
                        |
                        v
 +-----------------------------------------------+
 | Feature Extractor: Wallet-level behavior stats|
 +-----------------------------------------------+
                        |
                        v
 +---------------------------------------------+
 | Pseudolabel Generator (based on heuristics) |
 +---------------------------------------------+
                        |
                        v
           +---------------------------+
           | Train XGBoost Classifier  |
           +---------------------------+
                        |
                +------------------+
                | Predict Risk (%) |
                +------------------+
                        |
                        v
+-----------------------------+     +---------------------+
| Map risk → credit score     |<----+ SHAP (Tree Explainer)|
+-----------------------------+     +---------------------+
                        |
                        v
        +-----------------------------------+
        | Output: wallet_scores.json        |
        | - credit_score (0–1000)           |
        | - top_features (SHAP explain)     |
        +-----------------------------------+
```

---

## 🔍 Feature Engineering

Wallet-level features derived from past transactions in Aave:

| Feature Name                | Description                                           |
|----------------------------|-------------------------------------------------------|
| `deposits`                 | # of `deposit` actions                                |
| `borrows`                  | # of `borrow` actions                                 |
| `repays`                   | # of `repay` actions                                  |
| `redeems`                  | # of `redeemUnderlying` actions                       |
| `liquidations`             | Times wallet got liquidated                           |
| `liquidations_as_liquidator` | Times wallet liquidated others                   |
| `repay_ratio`              | `repays / borrows` if borrows > 0                     |
| `liquidation_rate`         | `liquidations / borrows` if borrows > 0               |
| `duration_days`            | Active lifecycle of wallet (last – first interaction) |

---

## ⚙️ Processing Flow

1. **Load** raw Aave transactions from `sample.json`
2. **Group** by `userWallet`
3. **Extract** features for:
   - Deposits, Borrows, Repays, Liquidations, Duration, etc.
4. **Build labels**:
   - Risky if `repay_ratio < 0.5` and/or `liquidation_rate > 0.25`
5. **Train** XGBoost detector on labeled features
6. **Score** wallets using  `credit_score = (1 - risk) * 1000`
7. **Explain** results using SHAP
8. **Save** to `wallet_scores.json`

---

## 💾 Output Format

```json
[
  {
    "wallet": "0x123...",
    "credit_score": 912,
    "top_features": [
      { "feature": "repay_ratio", "impact": 0.138 },
      { "feature": "liquidation_rate", "impact": -0.122 }
    ]
  },
  ...
]
```

---

## 📊 Score Interpretation

| Score Range  | Meaning                   |
|--------------|----------------------------|
| **900–1000** | Very responsible DeFi user |
| 700–899      | Strong, regular user       |
| 500–699      | Moderate risk              |
| 300–499      | At-risk / inconsistent use |
| 0–299        | Likely bot or exploiter    |

---

## 🧩 Extensibility

- Swap model: Use LightGBM, Logistic Regression, Neural Nets
- Add features: token volume, asset types, flash loan traces
- Plug into: governance dashboards, on-chain lending limits, loan prequalification UIs
- Retrain on: real user loss/lending default data

---

## ✅ Requirements

- `xgboost`
- `shap`
- `pandas`
- `scikit-learn`

Install via Colab or `requirements.txt`

---

## 📁 Files in Repo

| File                   | Purpose                                 |
|------------------------|------------------------------------------|
| `aave_cre.....ipynb`   | Main script: trains, scores, explains    |
| `wallet_scores.json`   | Output: credit score + SHAP explanations |
| `analysis.md`          | Distribution of scores and wallet behavior|
| `README.md`            | This file                                |
| `score_distribution.png` | Histogram of wallet score bands        |

---

Built with ❤️ for transparency, DeFi security, and credit decentralization.
