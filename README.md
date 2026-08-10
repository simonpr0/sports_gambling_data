# Sports Gambling Data

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/simonpr0/sports_gambling_data/blob/main/data.ipynb)
![Python 3.13](https://img.shields.io/badge/python-3.13-blue)
![License: MIT](https://img.shields.io/badge/license-MIT-green)

## Overview

With the rise of sports gambling, mostly online, I was intrigued about the data behind it —
specifically how much the betting giants profit off their customers, how much money states make
off betting taxes, and whether the benefits outweigh the costs. This project analyzes US
state-level legalization, revenue, and demographic data to answer three questions:

1. **Which states without legal online sports betting are most likely to legalize it next?**
2. **What predicts revenue per capita among states that have already legalized it?**
3. **Is the revenue states collect from legalization actually beneficial**, once weighed against
   what the data can say about cost?

Click the "Open in Colab" badge above to run the full analysis in your browser — no local setup
required. I want to eventually extend this into underage online gambling and the
volume/targeting of gambling advertising, both of which I think are headed for controversy.

## Key findings

**1. Predictor correlations (legalized states)** — checked for multicollinearity before fitting
the revenue models:

![Predictor correlation matrix](images/correlation_heatmap.png)

**2. The legalization model separates states reasonably well** (see the notebook for the current
LOO AUC and full holdout ranking) — South Carolina, Mississippi, and Georgia currently rank as
the most "legalization-ready" holdout states based on lottery revenue, unemployment, and
neighboring-state legalization patterns.

**3. The revenue model does not generalize.** With only 33–34 usable rows and 7+ predictors, LOO
cross-validated R² is negative for every regularization strength tried, including after cutting
down to the 3 most correlated predictors. The in-sample OLS R² (~0.82) is not meaningful here —
it reflects overfitting, not real explanatory power. This is a data volume problem, not a
modeling one; it should improve as more states legalize and the sample grows.

![Ridge regression predicted vs actual](images/predicted_vs_actual.png)

**4. Industry revenue isn't state revenue.** `revenue_per_capita` in the dataset is gross gaming
revenue (what sportsbooks take in), not what the state collects — that's
`revenue_per_capita * tax_rate`. Nevada leads every state in GGR per capita but taxes it at only
6.75%, so its actual state take per capita drops out of the top 8 once tax rate is accounted for,
while high-tax states like New York and New Hampshire (both 51%) move up. Any claim about "how
much states benefit" that uses raw revenue figures is measuring the wrong thing.

**5. On cost:** the dataset has no dollar-denominated cost data, only `problem_gambling_rate`.
State tax revenue per capita correlates weakly-to-modestly with problem gambling rate (r ≈ 0.38)
— consistent with more betting activity driving both, not evidence the tax revenue itself is
harmful. No sign of lottery revenue being cannibalized by sports betting revenue in this snapshot
(r ≈ 0.25, weakly positive, though this is cross-sectional and can't establish before/after
substitution).

![State tax revenue vs problem gambling rate](images/tax_revenue_vs_problem_gambling.png)

**Bottom line:** nothing here says the revenue isn't worth it, but nothing here can say it is,
either — a real verdict needs monetized social cost data this dataset doesn't have.

## Methodology

- **Data**: `betting_data.csv` — one row per US state (51, including DC), with legalization
  status/timing, revenue, demographics, and political/economic covariates.
- **Legalization model**: logistic regression, features standardized, evaluated with
  leave-one-out cross-validation (LOO-CV) given the small sample.
- **Revenue model**: OLS for an in-sample read, then Ridge + LOO-CV across a range of
  regularization strengths for an honest out-of-sample estimate.
- **Cost vs. benefit**: descriptive correlation only (no causal or monetized claims) between
  state tax revenue per capita, tax rate, lottery revenue, and problem gambling rate.

Full code and narrative are in [`data.ipynb`](data.ipynb).

## Limitations

- Small sample (33–51 states depending on model) relative to the number of predictors.
- Cross-sectional snapshot — no time-series data, so no before/after or causal claims are
  possible.
- No monetized cost data for problem gambling, only a prevalence rate.

## Setup

```bash
pip install -r requirements.txt
jupyter notebook data.ipynb
```

Or skip local setup entirely and use the [Colab link](https://colab.research.google.com/github/simonpr0/sports_gambling_data/blob/main/data.ipynb) above.
