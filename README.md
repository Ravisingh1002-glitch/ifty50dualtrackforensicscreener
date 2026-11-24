
# Nifty 50 Dual-Track Forensic Accounting Screener & Statistical Peer Clustering Engine

An automated, institutional-grade Python pipeline that screens all 50 Nifty 50 constituents for earnings manipulation, balance-sheet distress, and cash-flow quality—and groups companies by their actual financial DNA using unsupervised machine learning (`RobustScaler` + `K-Means` + `PCA`).

## Why a Dual-Track Architecture?
Financial Services represent ~37% of Nifty 50 weight, yet standard corporate forensic models (**Beneish M-Score**, **Altman Z-Score**, **Sloan Accruals**) are mathematically invalid for banks, NBFCs, and insurers because they lack traditional COGS, inventory, or current assets/liabilities, and leverage is an operating input rather than a distress signal.

This engine programmatically segments the Nifty 50 into three tracks:
- **Track A — Non-Financial Corporates (39 stocks):** Beneish M-Score (8 sub-ratios), Altman Z-Score, Sloan Accrual Ratio, CFO/PAT Cash Conversion, and K-Means/PCA clustering.
- **Track B — BFSI Lenders (9 Banks & NBFCs):** ROA, ROE, Equity Multiplier (Leverage), Provisioning Intensity, Non-Interest Income Share, Cost-to-Income, P/B, and K-Means/PCA clustering.
- **Programmatically Excluded — Life Insurers (2 stocks: `HDFCLIFE`, `SBILIFE`):** Flagged as actuarial models requiring VNB Margin, Persistency, and Solvency Ratio.

---

## Key Empirical Findings

### Track A: Non-Financial Corporates (39 Stocks)
| Institutional Peer Group | Count | Median Beneish M | Median Altman Z | Median CFO/PAT | Median Sloan Accrual | Median Red Flags |
| **High-Accrual Watchlist** | 6 | -1.83 | 2.04 (Grey) | 0.25x | +0.07 | 2.0 |
| **Fortress Compounders** | 13 | -2.60 | 10.98 (Safe) | 1.28x | -0.03 | 0.0 |
| **Capex-Heavy Cash Generators** | 7 | -2.83 | 1.72 (Distress*) | 2.57x | -0.08 | 1.0 |
| **Core Steady Cash Cows** | 13 | -2.34 | 5.75 (Safe) | 1.13x | -0.01 | 0.0 |

- **Separating Structural Leverage from True Distress:** *Capex-Heavy Cash Generators* (`RELIANCE`, `BHARTIARTL`, `NTPC`, `POWERGRID`, `ONGC`, `TATASTEEL`, `ULTRACEMCO`) trigger an Altman Z false positive (`1.72 < 1.81`) due to infrastructure leverage, yet exhibit the cleanest earnings quality (`Beneish M = -2.83`) and strongest cash conversion (`CFO/PAT = 2.57x`) in the index.
- **True Forensic Shortlist:** The *High-Accrual Watchlist* (`ADANIENT`, `BAJAJ-AUTO`, `BEL`, `GRASIM`, `HINDALCO`, `TMPV`) combines weak cash conversion (`0.25x`) with positive accruals (`+0.07`), identifying where an analyst should open cash-flow footnotes first.

### Track B: BFSI Lenders (9 Stocks)
| Institutional Peer Group | Count | Median ROA | Median ROE | Median Leverage | Median Cost-to-Inc | Median P/B |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **High-Yield Retail NBFCs** (`BAJFINANCE`, `SHRIRAMFIN`) | 2 | 3.48% | 17.22% | 4.89x | 28.98% | 4.82x |
| **Core Universal Banks & Conglomerates** (`HDFCBANK`, `ICICIBANK`, `SBIN`, `AXISBANK`, `KOTAKBANK`, `BAJAJFINSV`) | 6 | 1.43% | 13.08% | 8.54x | 49.16% | 2.39x |
| **Unlevered Financial Holding Outlier** (`JIOFIN`) | 1 | 1.05% | 1.21% | 1.22x | 17.41% | 1.12x |

- Without sub-sector labels, K-Means cleanly separated high-margin retail NBFCs from deposit-funded universal banks and automatically isolated `JIOFIN` due to its unlevered equity/treasury balance sheet (`1.22x` leverage).

---

## Real-World Data-Quality Guardrails Engineered
1. **ADR Currency Mismatch (`INFY.NS`):** Dual-listed ADRs on Yahoo Finance can return Market Cap in INR against USD Total Liabilities, inflating Altman Z's $X_4$ term by ~85x (`Altman_Z = 409.60`). Fixed via upper-bound capping (`25.0`) and `np.log1p` compression.
2. **Hyper-Growth Singleton Distortion (`ETERNAL.NS`):** High Sales Growth Index (`SGI`) pushed `ETERNAL` into a 1-stock cluster under standard Euclidean scaling. Fixed by replacing `StandardScaler` with `RobustScaler` and applying 5th–95th percentile Winsorization.
=
## Tech Stack & Deliverable
- **Python:** `yfinance`, `pandas`, `numpy`, `scikit-learn`, `plotly`, `openpyxl`
- **Output:** 5-tab Excel workbook (`outputs/Nifty50_DualTrack_Forensic_Screener.xlsx`) + 3 interactive Plotly dashboards.
