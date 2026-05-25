# AI Contract Token Burndown Analysis

This project analyzes sample AI token usage for a hypothetical 12‑month enterprise contract. The goal is to answer practical questions such as:

- Are we on track to stay within the contracted token limit?
- Which cost centers and roles are driving most of the usage?
- How concentrated is usage across users?
- When might we run out of tokens if current trends continue?

All analysis is done in a single notebook (`ai_contract_burndown.ipynb`) using two CSV inputs:

- `sample_token_usage.csv` — daily token usage by user
- `sample_users_hr.csv` — HR metadata (cost center, manager, title, department, location)

---

## Data and contract setup

Key assumptions:

- Contract size: **1,000,000,000 tokens**
- Contract start: **2026‑01‑01**
- Contract length: **12 months**
- Forecast horizon: **180 days** beyond the last date of actual usage
- Rolling window for trend detection: **28 days**


The notebook joins the usage logs with HR data and derives calendar features such as month, week, and weekday for downstream analysis.

---

## Contract burndown and forecast

### Actual vs. ideal vs. forecast remaining tokens

The core of the project is a **burndown chart** with three lines:

- **Ideal Remaining**  
  A straight‑line consumption path from full contract balance to zero, assuming perfectly even daily usage over the entire contract period.

- **Actual Remaining**  
  The true remaining balance, computed as:

  - Aggregate daily usage  
  - Take a running cumulative sum of tokens used  
  - Subtract cumulative usage from the contract token cap to get remaining tokens per day

- **Forecast Remaining**  
  A forward‑looking projection based on recent behavior:
  - Start from the **recent average daily burn** (28‑day mean)
  - Estimate a **daily growth rate** by comparing the last 28 days vs. the 28 days before that
  - Build a **weekday profile** (average tokens per weekday 0–6) and adjust each future date by its typical weekday usage
  - Forecast daily usage as:

    “recent average” × “compound growth for i days” × “weekday adjustment ratio”

  - Convert predicted daily usage into projected cumulative totals and remaining tokens

Vertical reference lines highlight:

- **Last Actual date** — where real data ends and the forecast begins
- **Contract End date** — the contractual end of the period
- **Projected Runout date** — first day where projected remaining tokens reach zero or below


This view lets stakeholders visually compare:

- How actual burn compares to the ideal line
- Whether projected runout is **before** or **after** the contract end date
- How much uncertainty sits between current position and contract expiration

---

## Cost center usage and per‑capita behavior

### Total usage by cost center

The notebook aggregates token usage by cost center and computes several metrics:

- Total tokens used per cost center
- Number of unique users in each cost center
- Share of total tokens (`pct_all_tokens`)
- Average tokens per user (`tokens_per_capita`)

These metrics are summarized both numerically and as a colored bar chart. In the sample data:

- Engineering, Operations, Product, Finance, and Sales each account for a substantial share of total tokens.
- The **tokens per user** metric highlights where usage is especially intense on a per‑person basis, not just in raw volume.

All numeric columns are displayed with:

- Commas for thousands (e.g., `197,497,713`)
- Percentages with `XX.XX%`
- Per‑capita values with two decimals

### Per‑capita usage by cost center

A separate chart focuses explicitly on **per‑capita** usage:

- Sort cost centers by `tokens_per_capita`
- Plot a bar chart showing **tokens per user** by cost center

This makes it easy to see, for example, that a cost center with moderate total volume might still have very high per‑user usage, which can indicate a more intensive AI adoption pattern within that group.

---

## User‑level concentration

### Top users and their share of usage

The notebook computes user‑level totals and builds a **concentration table**:

- Aggregate total tokens per user
- Sort users by total usage
- Calculate:
  - `top_10_user_share` = % of total tokens used by the top 10 users
  - `top_20_user_share` = % of total tokens used by the top 20 users
  - `total_usage` = total tokens used across all users

In the sample data, the output looks like:

- `top_10_user_share` ≈ **30.58%**
- `top_20_user_share` ≈ **50.94%**
- `total_usage` ≈ **632,278,780 tokens**

This tells a compact story: roughly half of all usage is coming from the top 20 users. That level of concentration is useful for access management, cost controls, and identifying power users who may need extra support or governance.

### Top 20 users detail

A separate table lists the top 20 users with:

- User email
- Cost center, manager, title
- Total tokens used
- % of all tokens
- % of tokens within their cost center

This helps answer questions like:

- Which individuals are driving usage within each cost center?
- Are certain managers or teams showing unusually high activity per person?

---

## Weekly seasonality and recent dynamics

### Weekday usage profile

To capture weekly seasonality, the notebook builds a **weekday profile**:

- Derive the weekday from each usage date (0 = Monday, …, 6 = Sunday)
- Compute the average tokens used on each weekday across the dataset

The result is a simple table showing typical usage by weekday, for example:

- Higher usage on core weekdays (Tuesday–Thursday)
- Lower usage on weekends

These weekday averages and their overall mean are then used to adjust the forecast so that projected usage respects the observed weekday pattern.

### Recent trend and implied growth

To detect recent acceleration in usage:

- Take the last 56 days of usage
- Split into two halves:
  - First 28 days
  - Second 28 days
- Compute the mean for each half
- Estimate a daily growth rate from the ratio of the second half to the first half
- Clamp negative growth to zero to avoid forecasting declines by default

The notebook prints:

- **Recent average daily burn** (with commas and two decimals)
- **Implied daily growth rate** as a percentage

This growth estimate, combined with the weekday profile, feeds directly into the forward‑looking forecast.

---

## Forecast and projected runout

Using the recent burn rate, estimated growth, and weekday profile, the notebook:

1. Projects daily usage for the next 180 days.
2. Converts projected daily usage into:
   - Future cumulative usage
   - Projected cumulative total (historical + forecast)
   - Projected remaining tokens versus the contract cap
3. Identifies the earliest date where projected remaining tokens reach zero:
   - This is the **Projected Runout date**.

A small preview table shows:

- Forecast date
- Forecasted tokens used that day
- Cumulative forecast usage
- Projected cumulative total
- Projected remaining balance

All numbers are formatted with thousands separators to be presentation‑ready.

---

## Roles and titles

Finally, the notebook looks at token usage by **job title**:

- Monthly aggregation of tokens by (month, title)
- Identification of the top 10 titles by total token usage
- A multi‑line chart showing **monthly total tokens** for these top titles over time

A variant of this analysis computes **per‑capita tokens per title per month**, allowing comparisons such as:

- “Which roles have the highest average usage per person?”
- “How is per‑user usage evolving month‑over‑month for key roles?”

---

## How this fits in a portfolio

This project is designed to showcase:

- Joining usage data with HR metadata
- Aggregations and groupby logic across multiple dimensions (date, cost center, user, title)
- Time‑series analysis with rolling averages, simple growth estimates, and seasonality adjustments
- Forecasting a runout date using interpretable, business‑friendly logic
- Presentation-ready tables and charts (thousands separators, percentages, clear annotations)

The notebook can be adapted to real internal data by swapping in actual usage and HR files while keeping the analytical structure intact.
