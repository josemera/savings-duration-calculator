# Savings Duration Calculator

A single-file, client-side web app that models how long a portfolio will last under different return assumptions. No backend, no dependencies beyond Chart.js — just drop `index.html` anywhere and open it.

## Live demo

[https://https://josemera.github.io/savings-duration-calculator/](https://josemera.github.io/savings-duration-calculator/)

---

## What it does

You provide a starting capital, a monthly spending amount, an optional bridge fund, and optional Social Security income. The calculator simulates your portfolio balance year by year for up to 30 years and tells you either when it runs out or what it grows to.

Three return modes let you stress-test different scenarios:

### Fixed rate
Classic calculation using a single annual return rate (0–15%), applied monthly via compound growth. Good for a quick baseline.

### Historical sequence
Uses 14 real annual returns derived from the actual cumulative performance of a real portfolio, starting in January 2012. The portfolio composition at the time the returns were recorded was:

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

Returns are applied in order and cycle after year 14. The sequence captures the actual sequence-of-returns effect of this allocation — including a significant down year (~−20%) partway through the cycle.

The 14 derived annual returns are:

| Seq | Return | | Seq | Return |
|-----|--------|-|-----|--------|
| 1   | +13.0% | | 8   | +32.8% |
| 2   | +28.5% | | 9   | +23.1% |
| 3   | +18.1% | | 10  | +39.9% |
| 4   | −0.8%  | | 11  | −19.8% |
| 5   | +16.6% | | 12  | +28.9% |
| 6   | +21.2% | | 13  | +34.1% |
| 7   | −3.8%  | | 14  | +21.5% |

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

**Cash runway / bridge fund** is a separate up-front cash pool that grows at a fixed 3.0% annual rate and is spent before the portfolio is tapped. This lets you model a short-term bridge strategy without folding that cash into the invested starting capital.

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
| Starting capital | Your initial portfolio value in dollars |
| Monthly spend | Your spending in today's dollars (grown by inflation if enabled) |
| Annual rate | Fixed return rate (Fixed rate mode only) |
| Cash runway | Separate bridge fund available up front, spent before portfolio withdrawals and grown at 3.0% annually |
| SS payment | Monthly Social Security income in today's dollars |
| SS starts in | How many months until SS payments begin (slider, 0–120) |
| Inflation adjustment | Optional toggle to enable inflation. Slider sets annual rate (default 3.0%) |

---

## Output

**Stat cards** — balance at year 30 (or depletion year), total interest earned, total portfolio gain/loss %, bridge fund used if applicable, SS income received if applicable, and real value in today's dollars if inflation is enabled.

**Chart** — 30-year balance trajectory. Line color indicates mode: green (fixed), blue (historical), purple (Random Sequence). In Random Sequence mode after running the distribution analysis, the chart shows a percentile fan (p10/median/p90) instead of a single line. When inflation is enabled, hovering over any point shows both the nominal balance and its real value in today's dollars.

**Yearly breakdown table** — year-by-year detail including:
- Sequence year (historical and Random Sequence modes)
- Annual return applied
- Interest earned
- SS income received (inflation-adjusted when enabled)
- Bridge fund used (when applicable)
- Portfolio withdrawals (inflation-adjusted when enabled)
- Net change
- Ending balance
- Real value in today's dollars (when inflation is enabled)

**Sequence reference table** — visible in historical sequence mode, shows all 14 source returns with their original cumulative values for reference.

---

## How returns are applied

Each year's annual return is converted to a monthly compounding rate (`(1 + r)^(1/12) - 1`) and applied month by month. Portfolio withdrawals, bridge-fund usage, and SS income are processed each month against the current balances — so the timing of gains and losses within a year affects the result, rather than treating it as a year-end lump sum.

Social Security income offsets spending each month first. Any remaining spending need is then covered by the bridge fund before the portfolio is tapped. In other words, the portfolio only sees `max(0, monthly spend − SS payment − bridge available that month)` as a withdrawal. If SS and/or the bridge fund fully cover monthly expenses, the portfolio is not drawn down during those months.

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
