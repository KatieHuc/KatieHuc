# AI Contract Token Burndown

This project simulates an enterprise AI usage analytics workflow for a 12‑month token‑based contract. It loads sample usage and HR data, computes token burndown over time, and produces rich visualizations and metrics that answer practical questions like:

- Are we on track to stay within the token limit?
- Which cost centers and roles are driving usage?
- How concentrated is usage across users?
- When are we likely to run out of tokens if current trends continue?

---

## Project structure

- `ai_contract_burndown.ipynb`  
  Main analysis notebook with all data transformations, charts, and metrics.

- `sample_token_usage.csv`  
  Synthetic daily token usage by user (date, user_email, tokens_used).

- `sample_users_hr.csv`  
  Synthetic HR data (user_email, cost_center, manager, title, department, location).

You can open the notebook in Jupyter, VS Code, or any notebook environment and run it end‑to‑end.

---

## Analysis overview

The notebook walks through the following steps:

1. **Load and join data**  
   - Load usage and HR CSVs.  
   - Join on `user_email` to enrich usage with cost center, manager, title, and other attributes.  
   - Derive time features such as month, week, and weekday.

2. **Contract setup**  
   - Contract size: **1,000,000,000 tokens**  
   - Start date: **2026‑01‑01**  
   - Duration: **12 months**  
   - Forecast horizon: **180 days** beyond the last observed usage date  
   - Rolling window: **28 days** for recent trend estimation

3. **Burndown computation**  
   - Aggregate usage to daily totals.  
   - Compute cumulative tokens used and remaining tokens versus the contract cap.  
   - Build an **ideal burndown line** assuming even daily usage across the contract.

4. **Forecasting**  
   - Compute a **recent average daily burn** using a 28‑day window.  
   - Estimate a **daily growth rate** by comparing the last 28 days to the previous 28 days.  
   - Build a **weekday usage profile** and adjust future days by typical weekday intensity.  
   - Forecast daily usage forward and convert into projected cumulative totals and remaining tokens.  
   - Identify the **projected runout date** when remaining tokens reach zero.

5. **Cost center analysis**  
   - Summarize total tokens by cost center.  
   - Compute:
     - Total tokens used
     - Number of unique users
     - Share of all tokens (`pct_all_tokens`)
     - Tokens per user (`tokens_per_capita`)  
   - Visualize with bar charts and formatted summary tables.

6. **User‑level concentration**  
   - Compute total tokens per user.  
   - Calculate concentration metrics:
     - `% of total usage from top 10 users`
     - `% of total usage from top 20 users`
   - List top users with their cost center, manager, title, and contribution to both global and cost‑center usage. [file:87]

7. **Weekday and role insights**  
   - Analyze average tokens by weekday (0–6) to reveal weekly usage patterns.  
   - Aggregate monthly usage by job title and visualize:
     - Monthly total tokens for top titles  
     - Optional per‑capita tokens per title per month

All numerical outputs used in tables are formatted with thousands separators and percentages to be presentation‑ready.

---

## Example questions this notebook answers

- Are we burning tokens faster than a straight‑line ideal path would suggest?
- How far is the projected runout date from the contract end date?
- Which cost centers are using the most tokens per person?
- How concentrated is usage among a small group of heavy users?
- On which weekdays is usage highest or lowest?
- Which job titles are driving the most AI activity over time?

---

## Skills demonstrated

This project is meant to showcase:

- **Data wrangling** with pandas (joins, groupby aggregations, pivot tables)
- **Time‑series style analysis** (rolling averages, simple trend estimation, weekday seasonality)
- **Forecasting logic** for contract runout under realistic business assumptions
- **Business‑oriented metrics** (cost center per‑capita usage, user concentration statistics)
- **Visualization and presentation** using Matplotlib, with attention to formatting and labels

Feel free to open issues or suggestions if you’d like to see additional analyses or visualizations built on top of this framework.
