# TTF Pass-Through in the Italian Balancing Market

TTF→power pass-through in the Italian Balancing Market: regime-dependent and post-Hormuz analysis. part of MSc thesis, PoliMi.

Quantifying the gas-power coupling on MB prices across market regimes and around the
February 2026 Strait of Hormuz closure, using TTF settlement and TERNA data
(Jan 2024 – Apr 2026).

---

## Key finding

The TTF → power pass-through in the Italian Balancing Market is structurally stable
across thin and thick regimes (β ≈ 1.50 in both), but the macro context dominates its
explanatory power:

- **Baseline (585 days)**: β = 1.48, p < 0.001, R² = 6.1% — each €/MWh of TTF passes
  through to MB power +1.48 €/MWh, slightly above 1:1 and consistent with CCGT
  generation (~45% of the Italian mix) setting the marginal cost.
- **Thin vs thick**: pass-through nearly identical (β ≈ 1.50), but R² nearly doubles
  in the thin regime (9.1% vs 4.7%). With fewer active operators and less competing
  noise, the gas signal dominates price formation. Thin regimes are not broken
  markets — they are cleaner reflections of fundamentals.
- **Post-Hormuz (28 Feb 2026)**: β jumps from 1.51 to 2.43 (+60%), R² triples
  (4.9% → 13.4%, p = 0.017). In a stressed gas market, every TTF move is amplified
  through to power. With 42 post-event days the magnitude is preliminary, but the
  structural shift is statistically clear.

---

## Why it matters

The Italian Balancing Market is a pay-as-bid mechanism where the gas-power coupling
enters MB price formation through CCGT marginal cost. The strength of this coupling
is not constant — it depends on market liquidity and on macro-shocks to the gas
system. Any forecasting framework that treats TTF as a fixed-coefficient driver
mis-specifies its actual role.

This finding feeds directly into the two-layer thesis architecture: TTF is one of
the exogenous variables in the ARIMAX volume forecast, and the regime-dependent
behaviour quantified here justifies including a thin/thick interaction term rather
than a single TTF coefficient.

---

## Data

| Source        | Data                                          | Frequency | Period          |
|---------------|-----------------------------------------------|-----------|-----------------|
| Yahoo Finance | TTF settlement prices (`TTF=F`)               | Daily     | Jan 2022 – 2026 |
| TERNA         | Balancing volumes and prices, IT-North/Centre | 15-min    | Jan 2024 – 2026 |

> Intraday TTF data is not freely accessible; the daily Yahoo Finance series is used
> as the most reliable open source. Pre-2024 TTF data is downloaded but excluded
> from the regressions to prevent the 2022 energy crisis singularity from dominating
> the fit.

---

## Pipeline

### 1. Data preparation

- TTF series downloaded via `yfinance`, MB volumes and prices ingested from TERNA
  monthly quarter-hourly summaries
- 15-min MB data aggregated to daily granularity by **median** (robust to intraday
  outliers) to match TTF frequency

### 2. Structural break identification

- Empirical detection from rolling power volatility was attempted and discarded:
  intraday noise too high to isolate the event from price dynamics alone
- TTF returns used instead: the maximum `pct_change()` falls on **2 March 2026** —
  the first trading day after the closure announcement of 28 February — consistent
  with the exogenous geopolitical anchor

### 3. OLS specification

Five univariate regressions on daily data:

```
power = α + β · TTF + ε
```

estimated via `statsmodels.OLS`, fit on:

- full sample
- thin regime subset (|volume| < 50 MWh)
- thick regime subset (|volume| ≥ 50 MWh)
- pre-Hormuz (date ≤ 2 March 2026)
- post-Hormuz (date > 2 March 2026)

Regime subsetting is applied at quarter-hourly resolution **before** daily
aggregation — only quarter-hours classified as thin contribute to each daily
median in the thin regression, and likewise for thick.

---

## Methodological notes

- Quarter-hourly OLS was initially attempted and discarded. With daily TTF replicated
  across 96 quarters of each day, the 5M observations inflated t-statistics without
  adding independent variation. Daily aggregation is the methodologically clean
  approach for this gas-power frequency mismatch.
- The thin-market threshold [−50, +50] MWh is inherited from the prior project
  ([Thin-Market-Regime-MB](https://github.com/MatteoEPoliti/Thin-Market-Regime-MB)),
  where it was derived empirically as the zone of peak MB price volatility. The
  threshold will be subject to sensitivity analysis in the full thesis.
- The post-Hormuz sample (42 days) is small. The direction and statistical
  significance of the regime shift are clear, but the β = 2.43 magnitude should
  be treated as preliminary and revisited as more data accumulates.
- OLS measures statistical association, not causation. The economic case for TTF
  causing the MB power price is grounded in the CCGT generation mix, not in the
  regression itself.

---

## Stack

`Python` · `pandas` · `numpy` · `statsmodels` · `matplotlib` · `yfinance`

Jupyter Notebook, Git.

---

## Relation to the broader project

This repository complements
[Thin-Market-Regime-MB](https://github.com/MatteoEPoliti/Thin-Market-Regime-MB),
where the structural two-regime behaviour of the Italian Balancing Market was
identified empirically. Together the two projects form the empirical groundwork
for my Master's thesis in Energy Engineering at Politecnico di Milano:

- **Project 1 (Thin-Market-Regime-MB)** — identifies *where* the regime break
  occurs in the MB and *why* a global model is mis-specified
- **Project 2 (this repository)** — quantifies *how* a fundamental driver (TTF)
  propagates through the MB and *how that propagation changes* across regimes and
  macro-shocks

The full thesis extends both with a regime classification framework, a two-layer
ARIMAX + Random Forest forecasting architecture, and the backtesting of two
regime-conditional bidding strategies for a BESS operator in the IT-North macrozone.
