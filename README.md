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

`data.ipynb` covers three questions:

1. **Legalization prediction** — logistic regression (leave-one-out cross-validated) predicting
   which of the 17 states without legal online betting are most likely to legalize it next, based
   on the 34 states that already have.
2. **Revenue prediction** — OLS and Ridge regression predicting revenue per capita among the 34
   states that have legalized online betting.
3. **State cost vs. benefit** — distinguishes industry gross gaming revenue from what the state
   actually collects in tax, then checks that against problem gambling rate as a (data-limited)
   proxy for cost.

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
- **Industry revenue isn't state revenue.** `revenue_per_capita` in the dataset is gross gaming
  revenue (what sportsbooks take in), not what the state collects — that's
  `revenue_per_capita * tax_rate`. Nevada leads every state in GGR per capita but taxes it at only
  6.75%, so its actual state take per capita drops out of the top 8 once tax rate is accounted
  for, while high-tax states like New York and New Hampshire (both 51%) move up. Any claim about
  "how much states benefit" that uses raw revenue figures is measuring the wrong thing.
- **On cost:** the dataset has no dollar-denominated cost data, only `problem_gambling_rate`. State
  tax revenue per capita correlates weakly-to-modestly with problem gambling rate (r ≈ 0.38) —
  consistent with more betting activity driving both, not evidence the tax revenue itself is
  harmful. No sign of lottery revenue being cannibalized by sports betting revenue in this
  snapshot (r ≈ 0.25, weakly positive, though this is cross-sectional and can't establish
  before/after substitution). Net: nothing here says the revenue isn't worth it, but nothing here
  can say it is, either — a real verdict needs monetized social cost data this dataset doesn't have.

## Setup

```bash
pip install -r requirements.txt
jupyter notebook data.ipynb
```
