# Savings Duration Calculator

A single-file, client-side web app that models how long a portfolio will last under different return assumptions. No backend, no dependencies beyond Chart.js — just drop `index.html` anywhere and open it.

## Live demo

[https://yourusername.github.io/savings-calculator](https://yourusername.github.io/savings-calculator)
*(replace with your GitHub Pages URL after deploying)*

---

## What it does

You provide a starting capital, a monthly spending amount, and an optional Social Security income. The calculator simulates your portfolio balance year by year for up to 30 years and tells you either when it runs out or what it grows to.

Three return modes let you stress-test different scenarios:

### Fixed rate
Classic calculation using a single annual return rate (0–15%), applied monthly via compound growth. Good for a quick baseline.

### Historical sequence
Uses 14 real annual returns derived from a cumulative portfolio return series, applied in order and cycling after year 14. Captures the actual sequence-of-returns effect — including a significant down year (~−20%) partway through the cycle.

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

### Monte Carlo
Takes the same 14 historical returns but draws them in a random order, refilling and reshuffling the pool every 14 years. Hit **Reshuffle ↺** to run a new scenario. This approximates a Monte Carlo simulation using empirically grounded return values rather than synthetic distributions — so the range of outcomes is realistic rather than theoretical.

---

## Inputs

| Input | Description |
|-------|-------------|
| Starting capital | Your initial portfolio value in dollars |
| Monthly spend | Fixed monthly withdrawal amount |
| Annual rate | Fixed return rate (Fixed rate mode only) |
| SS payment | Optional monthly Social Security income |
| SS starts in | How many months until SS payments begin (slider, 0–120) |

---

## Output

**Stat cards** — balance at year 30 (or depletion year), total interest earned, total portfolio gain/loss %, and SS income received if applicable.

**Chart** — 30-year balance trajectory. Line color indicates mode: green (fixed), blue (historical), purple (Monte Carlo).

**Yearly breakdown table** — year-by-year detail including:
- Sequence year (historical and Monte Carlo modes)
- Annual return applied
- Interest earned
- SS income received
- Total withdrawals
- Net change
- Ending balance

**Sequence reference table** — visible in historical sequence mode, shows all 14 source returns with their original cumulative values for reference.

---

## How returns are applied

Each year's annual return is converted to a monthly compounding rate (`(1 + r)^(1/12) - 1`) and applied month by month. Withdrawals and SS income are processed each month against the current balance — so the timing of gains and losses within a year affects the result, rather than treating it as a year-end lump sum.

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
