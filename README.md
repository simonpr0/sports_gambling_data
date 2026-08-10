# Sports gambling data

With the rise of sports gambling, mostly online, I was intrigued about the data behind it —
specifically how much the betting giants profit off their customers, how much money states make
off betting taxes, and whether the benefits outweigh the costs. I want to eventually extend this
into underage online gambling and the volume/targeting of gambling advertising, both of which I
think are headed for controversy.

## Data

`betting_data.csv` — one row per US state (51, including DC), with legalization status/timing,
revenue, demographics, and political/economic covariates. See the column headers in the file for
the full field list (legalization dates, revenue per capita, problem gambling rate, population,
median household income, tax rate, tribal gaming presence, etc.).

## Analysis

`data.ipynb` fits two models:

1. **Legalization prediction** — logistic regression (leave-one-out cross-validated) predicting
   which of the 17 states without legal online betting are most likely to legalize it next, based
   on the 34 states that already have.
2. **Revenue prediction** — OLS and Ridge regression predicting revenue per capita among the 34
   states that have legalized online betting.

## Key findings

- The legalization model separates states reasonably well (see the notebook for the current LOO
  AUC and the full holdout ranking) — South Carolina, Mississippi, and Georgia currently rank as
  the most "legalization-ready" holdout states based on lottery revenue, unemployment, and
  neighboring-state legalization patterns.
- The revenue model does **not** generalize: with only 33-34 usable rows and 7+ predictors, LOO
  cross-validated R² is negative for every regularization strength tried, including after cutting
  down to the 3 most correlated predictors. The in-sample OLS R² (~0.82) is not meaningful here —
  it reflects overfitting, not real explanatory power. This is a data volume problem, not a
  modeling one; it should improve as more states legalize and the sample grows.

## Setup

```bash
pip install -r requirements.txt
jupyter notebook data.ipynb
```
