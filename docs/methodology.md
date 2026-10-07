# Methodology

A non-proprietary overview of the analytics in the dashboard — the *ideas*, not
the implementation, formulas, or data integration.

## Enterprise value yields (GP/EV, EBITDA/EV)

Rather than price multiples (P/E, EV/EBITDA) that get noisy or undefined for
loss-making or capital-structure-heavy names, the dashboard works in **yield
space**: gross profit and operating EBITDA as a yield on enterprise value.

- **GP/EV** — gross profit over enterprise value. Gross profit is a stable,
  hard-to-manipulate line, which makes it a robust valuation anchor.
- **EBITDA/EV** — operating EBITDA over EV. (The dashboard defines EBITDA on an
  *operating* basis, excluding non-operating other income so the figure reflects
  the business rather than investment gains.)

Working in yields keeps metrics comparable across names and lets you reason about
mean reversion the way you would with an interest rate.

## Trend regression & distance-to-trend

Each yield series is fit with a **log-linear trend** (growth in the underlying
dollar series, expressed back as a trend yield). Two useful readouts:

- **Trend** — the fitted yield today: a smoothed "fair" level.
- **Distance-to-trend** — current yield minus its trend. Positive means the
  current yield sits above its trend (cheaper than its own history); negative
  means richer.

## Percentile framing

For each metric, the current value is ranked within the stock's **own history**
(0–100). Higher percentile = cheaper relative to how the name has traded. Across
an ETF's holdings, the distribution of these percentiles (a histogram) shows
whether the fund as a whole is stretched or washed out.

## Historical as-of dates

Any view can be computed "as of" a past date: holdings, valuation stats, and
histograms are reconstructed as they stood then, so you can see how a metric
looked at a prior point — and, for a past date, the *actual* trailing return
since then rather than a forward estimate.

## Options analytics

Single-option Greeks and payoff, plus multi-leg **book** Greeks and P&L
distributions simulated under volatility scenarios (Monte Carlo), for
constructing and analyzing spreads.

## Architecture notes

Multi-holding analysis is made responsive by:

- a **Redis caching layer** over upstream data and computed results, and
- **async fan-out** across holdings, so an ETF's constituents are fetched and
  computed concurrently rather than one at a time.

Server-side computation keeps the UI a thin render layer: it makes one request
and the heavy lifting (fan-out, regressions, percentiles) happens behind a cached
endpoint.
