# M&T Bank — Advanced Asset-Liability Management (ALM) & Interest Rate Risk Simulation

## Overview

This project is an independent Excel-based **Asset-Liability Management (ALM) and Interest Rate Risk in the Banking Book (IRRBB) simulation** built using publicly available information from **M&T Bank Corporation's FY2024 disclosures** and market data from the Federal Reserve Economic Data (FRED).

The project began as a relatively simple static balance-sheet sensitivity model and was subsequently expanded into a more dynamic framework incorporating:

* Scenario-dependent deposit behavior
* Mortgage prepayment and extension assumptions
* Non-parallel yield-curve scenarios
* Multi-period interest-rate paths
* Net Interest Income (NII) sensitivity
* Economic Value of Equity (EVE) sensitivity
* Automated risk-limit monitoring
* Model audit and validation checks

The objective was not to replicate M&T Bank's proprietary ALM or risk-management systems. Rather, the project explores how **balance-sheet structure, customer behavior, and interest-rate scenarios interact to influence a bank's earnings and economic value**.

---

## Key Questions

The model is designed to investigate several practical ALM questions:

1. How sensitive is a bank's short-term Net Interest Income to changes in interest rates?
2. How does the economic value of the balance sheet respond to the same shock?
3. How do deposit repricing assumptions affect the earnings impact of rate changes?
4. How does mortgage prepayment behavior change the duration of the asset portfolio?
5. Do non-parallel yield-curve movements produce materially different results from parallel shocks?
6. Does the sequence in which interest-rate changes occur affect the final outcome?
7. How might these exposures appear from an Asset-Liability Committee (ALCO) perspective?

---

# Model Architecture

The model follows the general structure:

```text
Public Financial Disclosures
          +
     FRED Market Data
          |
          v
   Balance Sheet Setup
          |
          v
 Behavioral Assumptions
          |
          v
  Interest Rate Scenarios
          |
          +------------------+
          |                  |
          v                  v
      NII Engine         EVE Engine
          |                  |
          +--------+---------+
                   |
                   v
             Risk Dashboard
                   |
                   v
            ALCO Monitoring
```

The workbook separates source data, assumptions, calculations, outputs, and audit checks to make the model easier to trace and review.

---

# Model Modules

## 1. Mortgage Cohort & Extension-Risk Module

Residential mortgages are separated into three illustrative coupon-vintage cohorts rather than being treated as a single homogeneous balance.

The model uses:

* 40% Low-Coupon cohort
* 35% Intermediate-Coupon cohort
* 25% High-Coupon cohort

Effective asset lives are then adjusted under different interest-rate environments to illustrate the effects of mortgage prepayment and extension behavior.

### Illustrative results

Under a **+200bp rate shock**, the low-coupon mortgage cohort extends from approximately 10 years to 13 years.

Under a **-200bp rate shock**, the high-coupon cohort contracts from approximately 4.0 years to 0.5 years.

The purpose of this module is to demonstrate the basic economic intuition behind mortgage convexity:

* Rising rates can reduce refinancing activity and extend asset duration.
* Falling rates can increase refinancing activity and accelerate principal repayment.

These cohort weights and behavioral lives are **model assumptions**, not estimates of M&T Bank's proprietary mortgage portfolio behavior.

---

# 2. Regime-Dependent Deposit Behavioral Module

Non-maturity deposits (NMDs) are modeled using different behavioral assumptions across interest-rate environments.

Rather than assuming that deposit pricing remains constant, the model allows deposit beta and behavioral decay assumptions to change as market rates move.

Illustrative assumptions include:

| Environment              | Deposit Beta | Approx. Deposit Life |
| ------------------------ | -----------: | -------------------: |
| Falling-rate environment |         0.25 |               Longer |
| Base case                |         0.45 |            2.5 years |
| Rising-rate environment  |         0.65 |            1.7 years |

The purpose is to capture an important ALM concept:

> **Deposit balances do not necessarily reprice one-for-one with market interest rates.**

This behavioral assumption can materially change the estimated sensitivity of both NII and EVE.

---

# 3. Non-Parallel Yield Curve Module

A traditional parallel-shock analysis moves the entire yield curve by the same number of basis points.

This project extends that framework by examining multiple curve shapes, including:

* Parallel shocks
* Steepeners
* Flatteners
* Bear flatteners

The model interpolates across tenor points to construct scenario-specific curves and then feeds those curves into the valuation framework.

One illustrative scenario produces a particularly important result:

> Under a bear-flattener scenario, 12-month NII increases by approximately **$459 million**, while EVE decreases by approximately **$2.1 billion**.

This demonstrates why looking only at short-term earnings sensitivity can provide an incomplete picture of interest-rate risk.

A scenario can be favorable for **near-term earnings** while simultaneously damaging the **economic value of the balance sheet**.

---

# 4. Multi-Period Path-Dependency Module

The model also examines whether the sequence of interest-rate changes matters, rather than looking only at the final interest-rate level.

For example, the model compares a permanent +200bp scenario with a two-period path that temporarily moves rates lower before reaching the same endpoint.

In the modeled scenario:

* Permanent +200bp endpoint benefit: approximately **+$1.05 billion**
* Temporary -100bp path followed by the same endpoint: approximately **-$2 million**
* Illustrative path-dependency difference: approximately **$1.05 billion**

This demonstrates that behavioral assumptions can create different outcomes even when two scenarios ultimately arrive at the same market-rate level.

The result should be interpreted as a feature of the model's behavioral assumptions rather than a forecast of M&T's actual earnings.

---

# 5. NII Simulation Engine

The Net Interest Income module estimates the effect of interest-rate scenarios on the bank's projected interest income and interest expense.

Conceptually:

```text
Interest Income
       -
Interest Expense
       =
Net Interest Income
```

The model applies scenario-specific repricing and behavioral assumptions to the balance sheet and estimates the resulting change in NII.

The primary outputs include:

* 12-month NII sensitivity
* 24-month NII sensitivity
* Dollar change in NII
* Percentage change in NII
* Scenario-by-scenario comparison

---

# 6. EVE / Economic Value Module

Economic Value of Equity provides a longer-term perspective than NII.

Instead of focusing on the income generated during a particular period, the EVE framework estimates how the present value of future asset and liability cash flows changes under different interest-rate scenarios.

The model therefore evaluates both:

**Short-term earnings risk**

and

**Long-term economic-value risk**

This distinction is important because NII and EVE do not necessarily move in the same direction.

---

# 7. ALCO Monitoring Layer

The dashboard includes an illustrative risk-monitoring layer based on predefined policy thresholds.

Scenario results are classified as:

* `OK`
* `WATCH`
* `BREACH`

The purpose is to demonstrate how an ALCO-oriented reporting process could translate model outputs into actionable risk flags.

The thresholds used in this project are **illustrative assumptions** and should not be interpreted as M&T Bank's actual internal risk limits.

---

# Selected Results

The following table compares the original static framework with the expanded behavioral framework.

| Metric                 | Static Framework | Behavioral Framework |       Change |
| ---------------------- | ---------------: | -------------------: | -----------: |
| Δ NII 12-Month         |        $876.52mm |            $524.42mm |   -$352.10mm |
| Δ NII 12-Month (% NII) |           12.79% |                7.65% |       -5.14% |
| Δ NII @ -200bp         |       -$876.52mm |         -$1,228.61mm |   -$352.10mm |
| Δ EVE                  |        $893.63mm |         -$4,011.60mm | -$4,905.23mm |
| Δ EVE (% Equity)       |            3.08% |              -13.82% |      -16.90% |
| Δ EVE @ -200bp         |       -$642.91mm |           -$466.29mm |   +$176.62mm |
| Path-Dependency Cost   |      Not modeled |         -$1,050.70mm |   New metric |

The primary takeaway is that introducing behavioral assumptions can materially change the conclusions produced by a static sensitivity model.

---

# Model Audit & Technical Improvements

A structured internal audit was performed during development to identify formula dependencies, scenario-binding issues, and valuation inconsistencies.

Key redesigns included:

### A1 — NII Grid Decoupling

Corrected a scenario-grid issue in which individual shock rows could inadvertently reference the currently selected scenario.

The revised framework calculates each scenario independently of the active dashboard selection.

### A2 — Dynamic Life Valuation

Rebuilt the EVE valuation grid so that each scenario is discounted using the corresponding scenario-specific behavioral asset life rather than relying on a centralized selector.

### A3 — Cash-Flow-Based Valuation

Replaced a simple average-life / modified-duration approximation with a cash-flow-based revaluation framework.

This was done after identifying that the simplified duration approximation materially overstated structural asset-price changes in certain scenarios.

### A4 — Live Curve-Scenario Binding

Replaced static scenario outputs with linked calculations that feed directly into the NII and EVE engines.

### A13 — Asymmetric Deposit Beta Matrix

Updated the deposit sensitivity framework so that deposit beta assumptions change according to the individual scenario rather than applying one static beta assumption across all shocks.

---

# Data Sources

## M&T Bank Corporation

Primary balance-sheet and financial information is sourced from M&T Bank Corporation's FY2024 public filings.

The project uses publicly disclosed information including:

* Balance-sheet balances
* Interest-earning assets
* Deposits
* Borrowings
* Debt
* Mortgage-related information
* Other relevant financial disclosures

## Federal Reserve Economic Data (FRED)

Treasury yield and market-rate information is sourced from FRED.

FRED is maintained by the Federal Reserve Bank of St. Louis.

---

# Important Methodology & Scope Disclosures

This project is an **independent educational and analytical model**. It is not an M&T Bank model and does not represent M&T Bank's internal risk-management methodology, forecasts, assumptions, or proprietary data.

### 1. Pre-Hedge Exposure

The model primarily analyzes the underlying on-balance-sheet exposure before incorporating M&T's full interest-rate hedging program.

M&T's reported interest-rate sensitivity includes the effect of its hedging activities.

Therefore, the model's sensitivity results should **not** be directly interpreted as forecasts of M&T's reported NII or EVE sensitivity.

### 2. Behavioral Assumptions

Mortgage prepayment speeds, mortgage cohort weights, deposit betas, deposit lives, and other behavioral parameters are illustrative assumptions used to explore sensitivity.

They are not intended to replicate M&T's proprietary behavioral models.

### 3. Borrowing Classification

Fixed- and floating-rate allocations for certain borrowing categories are modeled using available contractual information. Detailed swap documentation and proprietary hedge information were not available.

### 4. Balance-Sheet / Market-Data Timing

Balance-sheet data are anchored to **December 31, 2024**, based on M&T's FY2024 disclosures.

Certain market-rate and discount-curve inputs are based on **July 30, 2026 FRED data**.

This means the model combines a historical balance-sheet snapshot with a later illustrative market-rate environment.

It should therefore be interpreted as a **scenario simulation**, not as a contemporaneous measurement of M&T's actual 2026 IRRBB exposure.

### 5. Regulatory Interpretation

The project is designed primarily as an analytical modeling exercise and is not intended to reproduce a bank's regulatory IRRBB reporting methodology.

Any regulatory thresholds or ALCO limits shown in the workbook are illustrative unless explicitly identified as publicly disclosed requirements.

---

# What I Learned

Building the model reinforced several concepts that are difficult to appreciate from financial statements alone.

### 1. Interest-rate risk is not simply "rates up = good" or "rates down = bad."

The effect depends on the relative repricing behavior of assets and liabilities.

### 2. NII and EVE answer different questions.

NII focuses on the effect of rate changes on near-term earnings, while EVE provides a longer-term perspective on the economic value of the balance sheet.

### 3. Behavioral assumptions can matter as much as contractual terms.

Deposits and mortgages contain customer behavior that can materially change the effective duration and repricing characteristics of the balance sheet.

### 4. Curve shape matters.

A parallel +100bp shock does not tell the complete story. Steepeners, flatteners, and other non-parallel movements can produce very different results across NII and EVE.

### 5. Model validation matters.

A sophisticated-looking output is not useful if the underlying scenario grid, assumptions, or cell dependencies are incorrect. Building audit checks into the workbook was therefore an important part of the project.

---

# Project Structure

The Excel workbook is organized into separate sections for:

```text
README
│
├── Raw Data
│   ├── M&T Bank FY2024 disclosures
│   ├── FRED market data
│   └── Supporting inputs
│
├── Balance Sheet
│
├── Assumptions
│
├── Regime Assumptions
│
├── Repricing Gap
│
├── NII Simulation
│
├── Cash Flows
│
├── EVE / Duration Analysis
│
├── Dashboard
│
└── Audit / Validation
```

The exact worksheet structure may evolve as the model is refined.

---

# Intended Use

This project is intended for:

* Financial modeling practice
* Banking and ALM education
* Interest-rate risk analysis
* Excel modeling demonstration
* Portfolio development
* Discussion in finance interviews

It should **not** be used as an investment recommendation, regulatory submission, valuation of M&T Bank, or representation of M&T Bank's internal risk systems.

---

# Next Step

This project represents the **ALM / balance-sheet-risk side** of my financial modeling work.

The next project will build on this foundation from a different perspective:

> **FIG Capital Advisory + DCM Financing**

The objective will be to examine how a financial institution evaluates its capital stack, financing alternatives, regulatory capital position, and debt issuance economics.

Together, the projects are intended to demonstrate both sides of financial-institution analysis:

**How a bank manages its balance sheet**

and

**How a bank accesses capital markets.**
