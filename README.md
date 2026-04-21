# Savings Duration Calculator

A single-file, client-side web app that models how long a portfolio will last under different return assumptions. No backend, no dependencies beyond Chart.js — just drop `index.html` anywhere and open it.

## Live demo

[https://https://josemera.github.io/savings-duration-calculator/](https://josemera.github.io/savings-duration-calculator/)

---

## What it does

You provide an invested/equity starting capital, an optional cash reserve, a monthly spending amount, and optional Social Security income. The calculator simulates your portfolio balance year by year for up to 30 years and tells you either when it runs out or what it grows to.

Three return modes let you stress-test different scenarios:

### Fixed rate
Classic calculation using a single annual return rate (0–15%), applied monthly via compound growth. Good for a quick baseline.

### Historical sequence
Uses 14 real annual returns from the historical portfolio's total-return column, starting in January 2012. The portfolio composition at the time the returns were recorded was:

| Ticker | Allocation | Description |
|--------|-----------|-------------|
| VPU    | 30%       | Vanguard Utilities ETF |
| IYF    | 25%       | iShares U.S. Financials ETF |
| SCHD   | 15%       | Schwab U.S. Dividend Equity ETF |
| IVV    | 10%       | iShares Core S&P 500 ETF |
| VTI    | 5%        | Vanguard Total Stock Market ETF |
| QQQ    | 5%        | Invesco Nasdaq-100 ETF |
| SMH    | 5%        | VanEck Semiconductor ETF |
| VGT    | 5%        | Vanguard Information Technology ETF |

Returns are applied in order and cycle after year 14. The sequence captures the actual sequence-of-returns effect of this allocation — including a significant down year (~−21%) partway through the cycle.

The 14 annual total returns are:

| Seq | Year | Return | | Seq | Year | Return |
|-----|------|--------|-|-----|------|--------|
| 1   | 2012 | +11.9% | | 8   | 2019 | +39.8% |
| 2   | 2013 | +32.9% | | 9   | 2020 | +30.9% |
| 3   | 2014 | +16.4% | | 10  | 2021 | +34.2% |
| 4   | 2015 | +0.3%  | | 11  | 2022 | −21.0% |
| 5   | 2016 | +19.0% | | 12  | 2023 | +36.1% |
| 6   | 2017 | +26.3% | | 13  | 2024 | +27.8% |
| 7   | 2018 | −5.9%  | | 14  | 2025 | +28.9% |

### Random Sequence
Takes the same 14 historical returns but draws them in a random order, refilling and reshuffling the pool every 14 years. Hit **Reshuffle ↺** to run a new scenario.

This is **bootstrapped resampling**, not a traditional Monte Carlo simulation. A true Monte Carlo would draw returns from a probability distribution (e.g., a normal distribution with a given mean and standard deviation), potentially generating values never seen in history. This approach only ever uses returns that actually occurred — so the outcome range is bounded by real historical experience rather than theoretical tails.

Click **Run Analysis ▶** to run 500 shuffles at once and see the full distribution of outcomes (see [Random Sequence Distribution Analysis](#random-sequence-distribution-analysis) below).

---

## Random Sequence Distribution Analysis

Available only in Random Sequence mode. Click **Run Analysis ▶** to run 500 independent shuffles against your current inputs and visualize the spread of outcomes.

### Summary cards

| Card | Description |
|------|-------------|
| Survived 30 yrs | Percentage of runs where the portfolio lasted the full 30 years. Green ≥ 80%, blue ≥ 50%, red below 50%. |
| Median final bal | 50th-percentile ending balance across all 500 runs |
| Earliest depletion | The earliest year any run depleted (worst case) |
| Best outcome | Highest final balance among runs that survived |

### Fan chart

The main balance chart switches from a single line to a **percentile band** showing the 10th, median, and 90th percentile balance at each year across all 500 runs. The shaded band between the dashed lines represents the middle 80% of outcomes.

### Scatter plot — two views

**Outcome tab**
- X-axis: final balance at year 30 (0 for depleted runs)
- Y-axis: total interest earned over 30 years
- Green dots = survived · Red dots = depleted
- Clusters near the origin are depleted runs; runs that survive and compound heavily appear in the upper right

**Timeline tab**
- Runs sorted by final balance ascending (rank on X-axis)
- Y-axis: year depleted (30 = survived)
- Shows the "cliff" where runs transition from survived to depleted as returns get worse
- Useful for seeing how many runs clustered near the depletion threshold

### Clicking a dot

Click any dot on the scatter plot to load that specific shuffle into the main chart, stat cards, and yearly breakdown table. A note at the bottom of the scatter identifies which run you're viewing and its outcome. Click **Reshuffle ↺** or **Run Analysis ▶** to return to the distribution view.

Changing any input while analysis is active automatically clears the analysis results.

---

## Inflation Adjustment

An optional toggle in the controls panel. When enabled, a slider sets the annual inflation rate (0–10%, default 3.0%).

### What it adjusts

**Monthly spend** grows each year by the inflation rate. A $4,000/month baseline becomes ~$4,120 in year 1, ~$5,418 in year 10, and ~$9,700 in year 30 at 3% — reflecting the real cost of maintaining the same lifestyle.

**Social Security income** also grows at the inflation rate, starting from year 0 in today's dollars. This matches how SS Cost of Living Adjustments (COLA) work by law: the benefit accrues real value from the day you enter it, even during the delay period before payments begin. A $2,000/month SS benefit entered today will be worth ~$2,312/month when it starts five years from now.

### Why this doesn't double-count returns

The 14 historical returns used by this calculator are **nominal** — inflation was already embedded in the market performance of those years. Applying a separate inflation rate to spending is therefore correct: you're modeling a portfolio that grows at historical nominal rates while expenses rise in nominal terms alongside them.

### Real value outputs

When inflation is active, two additional outputs appear:

- **Real value at yr 30 stat card** — the nominal final balance deflated back to today's purchasing power. At 3% inflation over 30 years, a nominal $800k is worth roughly $330k in today's dollars (× 0.41).
- **Real value column in the yearly breakdown table** — each year's ending balance expressed in today's dollars, shown alongside the nominal balance.
- **Chart tooltip** — hovering over any year on the single-run balance chart shows both the nominal balance and its real value in today's dollars.

---

## Inputs

| Input | Description |
|-------|-------------|
| Starting capital | Your invested/equity sleeve value in dollars |
| Cash reserve | Separate cash sleeve included in total portfolio balance |
| Cash interest | Annual cash-sleeve interest rate, selectable from 0% to 8% |
| Monthly spend | Your spending in today's dollars (grown by inflation if enabled) |
| Annual rate | Fixed return rate (Fixed rate mode only) |
| SS payment | Monthly Social Security income in today's dollars |
| SS starts in | How many months until SS payments begin (slider, 0–120) |
| Inflation adjustment | Optional toggle to enable inflation. Slider sets annual rate (default 3.0%) |

---

## Output

**Stat cards** — balance at year 30 (or depletion year), total interest earned across equity and cash sleeves, total portfolio gain/loss %, SS income received if applicable, and real value in today's dollars if inflation is enabled.

**Chart** — 30-year balance trajectory. Line color indicates mode: green (fixed), blue (historical), purple (Random Sequence). In Random Sequence mode after running the distribution analysis, the chart shows a percentile fan (p10/median/p90) instead of a single line. When inflation is enabled, hovering over any point shows both the nominal balance and its real value in today's dollars.

**Yearly breakdown table** — year-by-year detail including:
- Sequence year (historical and Random Sequence modes)
- Annual return applied
- Interest earned
- SS income received (inflation-adjusted when enabled)
- Portfolio withdrawals (inflation-adjusted when enabled)
- Net change
- Ending balance
- Real value in today's dollars (when inflation is enabled)

**Sequence reference table** — visible in historical sequence mode, shows all 14 source returns with their original cumulative values for reference.

---

## How returns are applied

Each year's annual return is converted to a monthly compounding rate (`(1 + r)^(1/12) - 1`) and applied month by month to the invested/equity sleeve. The optional cash reserve compounds monthly at the selected cash interest rate. Portfolio withdrawals and SS income are processed each month against the current balances — so the timing of gains and losses within a year affects the result, rather than treating it as a year-end lump sum.

Social Security income offsets spending each month first. Any remaining spending need is then covered by the cash reserve, then the invested/equity sleeve. In other words, the invested sleeve only sees withdrawals after SS and portfolio cash have been used. If SS and/or the cash reserve fully cover monthly expenses, the invested sleeve is not drawn down during those months.

---

## Deploying to GitHub Pages

1. Create a new GitHub repository
2. Add `index.html` to the root of the repo
3. Go to **Settings → Pages → Source → Deploy from branch**
4. Select **main** branch, **/ (root)** folder, and save
5. Your app will be live at `https://yourusername.github.io/your-repo-name`

---

## Tech

- Vanilla HTML, CSS, JavaScript — no build step, no framework
- [Chart.js 4.4.1](https://www.chartjs.org/) via CDN for the balance chart
- Fully client-side — no data leaves the browser
