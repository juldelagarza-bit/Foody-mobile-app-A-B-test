# Mobile App A/A/B Test — Checkout Funnel Conversion Analysis

A/A/B experiment analysis for a food-service mobile app, evaluating whether a UI redesign (new fonts) changes checkout conversion. Built as a portfolio piece to demonstrate end-to-end analytics work: data cleaning, funnel analysis, and rigorous statistical hypothesis testing with multiple-comparisons correction.

## Business problem

Before rolling out a full app redesign, the product team ran a controlled experiment with three user groups:

- **246 — Control (A):** old design
- **247 — Control (A′):** old design (second control, to validate randomization)
- **248 — Test (B):** new design (new fonts)

The goal: determine whether the redesign changes checkout conversion, and whether it's worth shipping.

<img width="1017" height="812" alt="dc364890-5e6d-4f40-be72-d88dee6e5e0a" src="https://github.com/user-attachments/assets/e6247a76-bf81-43c7-95e8-25b988060d58" />

## What this project covers

- **Data cleaning:** deduplication, column standardization, timestamp parsing, and time-window filtering to isolate the reliable observation period.
- **Data quality validation:** confirming users were assigned to exactly one experiment group and that group sizes are balanced.
- **Funnel analysis:** unique-user conversion at each step of `MainScreenAppear → OffersScreenAppear → CartScreenAppear → PaymentScreenSuccessful`, identifying where users drop off.
- **A/A validation:** using the Mann-Whitney U test to confirm the two control groups behave the same before trusting any A-vs-B result.
- **A/B hypothesis testing:** four pairwise Mann-Whitney U tests (A vs A′, A vs B, A′ vs B, combined A+A′ vs B).
- **Multiple-comparisons correction:** Bonferroni adjustment and Family-Wise Error Rate (FWER) discussion, since 4 tests were run on the same data.
- **Business recommendation:** a clear go/no-go read on the redesign based on the statistical evidence.

## Key results

- No statistically significant difference in conversion between the old and new designs, in any of the 4 comparisons — even after a conservative Bonferroni correction (adjusted α ≈ 1.25%).
- The steepest funnel drop-off (≈37–38.5% of users) happens between `MainScreenAppear` and `OffersScreenAppear`, consistently across all groups.
- **Recommendation:** the evidence does not support that the redesign improves conversion, so shipping it purely for a conversion lift is not justified; other, non-conversion goals (accessibility, brand, etc.) would need separate justification.

## Tech stack

`pandas` · `numpy` · `scipy.stats` (Mann-Whitney U test) · `matplotlib` · `seaborn`

## Repository contents

| File | Description |
|---|---|
| `ab_test_funnel_analysis.ipynb` | Full analysis notebook, with business-English explanations and pre-run outputs/charts |
| `logs_exp_us.csv` | Raw event log dataset (244,126 events, 3 experiment groups) |

## How to run

```bash
pip install pandas numpy scipy matplotlib seaborn jupyter
jupyter notebook ab_test_funnel_analysis.ipynb
```

The notebook reads `logs_exp_us.csv` from the same folder, so no path changes are needed after cloning.

## Dataset

`logs_exp_us.csv` — one row per user event:

| Column | Description |
|---|---|
| `EventName` | User action (e.g. `MainScreenAppear`, `PaymentScreenSuccessful`) |
| `DeviceIDHash` | Anonymized device/user identifier |
| `EventTimestamp` | Unix timestamp of the event |
| `ExpId` | Experiment group (246 / 247 / 248) |
