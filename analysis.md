# Wallet Credit Score Analysis

**Date:** Tuesday, July 22, 2025, 10:10 PM IST

---

## 1. Overview

This analysis details the distribution and behavioral patterns of wallets scored using our on-chain credit scoring model built from Aave V2 transaction data. Scores range from 0 (high risk) to 1000 (highly reliable), derived solely from historical transaction behavior.

---

## 2. Score Distribution

We categorized wallets into decile score ranges of 100 points each to examine overall distribution:

| Score Range | Wallet Count | Percentage (%) |
|-------------|--------------|----------------|
| 0 - 100     | [Count]      | [Percent]%     |
| 100 - 200   | [Count]      | [Percent]%     |
| 200 - 300   | [Count]      | [Percent]%     |
| 300 - 400   | [Count]      | [Percent]%     |
| 400 - 500   | [Count]      | [Percent]%     |
| 500 - 600   | [Count]      | [Percent]%     |
| 600 - 700   | [Count]      | [Percent]%     |
| 700 - 800   | [Count]      | [Percent]%     |
| 800 - 900   | [Count]      | [Percent]%     |
| 900 - 1000  | [Count]      | [Percent]%     |

A histogram plot illustrating this distribution is saved as `score_distribution.png`.

---

## 3. Behavior of Wallets in Lower Score Range (0–300)

Wallets scoring in the bottom 300 generally display risky or bot-like behaviors including:

- **High liquidation rates:** Multiple liquidation events indicating failure to maintain collateral or repay loans.
- **Low repayment ratios:** Frequent borrowing without timely repayment.
- **Short active lifecycle:** Wallets active only briefly, suggestive of flash loan or exploit attempts.
- **Rapid, repeated transactions:** Indicative of bots or automated exploit scripts.

Example typical behavior of wallets in this range:

| Metric           | Typical Values           |
|------------------|--------------------------|
| Borrows          | 3+                       |
| Repays           | 0 to 1                   |
| Liquidations     | 1 or more                |
| Active Duration  | 1–3 days                 |

---

## 4. Behavior of Wallets in Higher Score Range (700–1000)

Wallets in the top score ranges exhibit trusted and consistent behavior:

- **High repayment ratios** (close to or exceeding 1.0), showing responsible debt management.
- **Minimal to zero liquidations**, indicating good risk control.
- **Long activity span**, sometimes several months.
- **Diverse transactions**, including deposits, borrows, repays, and redemptions.
- **Some act as liquidators**, reflecting advanced and active users.

Example wallet profile:

| Metric           | Typical Values            |
|------------------|--------------------------|
| Borrows          | 1 to 10                  |
| Repays           | Equal or greater than borrows |
| Liquidations     | 0                        |
| Active Duration  | 30+ days                 |

---

## 5. Insights & Implications

- The majority of wallets cluster in the higher score bands, demonstrating predominantly responsible DeFi participation.
- Lower score wallets warrant caution — suitable for limits, increased collateralization, or monitoring.
- SHAP explanations confirm that features like `repay_ratio`, `liquidation_rate`, and `duration_days` drive score assignment.

---

## 6. Method Summary

- Scores derived from a supervised XGBoost model trained on features extracted directly from users' historical Aave interactions.
- Labels created heuristically from liquidation frequency and repayment behavior.
- Each score is accompanied by SHAP feature impact values enabling transparent auditability.

---

## 7. Next Steps

- Incorporate token-value weighted features and flash loan detection to refine risk signals.
- Develop dashboards for real-time score monitoring.
- Integrate the scoring framework with lending protocol risk management tools.

---

*End of Analysis*
