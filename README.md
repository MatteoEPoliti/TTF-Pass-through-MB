# TTF Pass-Through in the Italian Balancing Market

Quantifying the gas-power coupling on BM prices across market regimes
and around the March 2026 Strait of Hormuz closure, using TTF and
TERNA data for the Balancing Market.
<img width="1465" height="443" alt="image" src="https://github.com/user-attachments/assets/32cf2895-e486-4694-8d57-46eeaba58dcb" />

---

## Key finding

| Sample            | β     | R²    | p-value | N   |
|-------------------|-------|-------|---------|-----|
| Baseline          | 1.48  | 6.1%  | <0.001  | 585 |
| Thin regime       | 1.51  | 9.1%  | <0.001  | 584 |
| Thick regime      | 1.50  | 4.7%  | <0.001  | 585 |
| Pre-Hormuz        | 1.51  | 4.9%  | <0.001  | 542 |
| **Post-Hormuz**   | **2.43** | **13.4%** | **0.017** | **42** |

Pass-through is structurally stable across thin and thick regimes (β ≈ 1.50),
but **the macro context dominates**: after the Hormuz closure, β jumps +60%
and R² triples. The thin/thick split shows fewer active operators amplify
the gas signal (R² doubles) without changing pass-through magnitude — thin
regimes are cleaner reflections of fundamentals, not broken markets.

---

## Context

Companion to [Thin-Market-Regime-MB](https://github.com/MatteoEPoliti/Thin-Market-Regime-MB):
the first project identifies *where* the regime break occurs, this one quantifies
*how* TTF propagates and *how that propagation shifts* across regimes and macro-shocks.
Both feed my MSc thesis at Politecnico di Milano, which extends them with a regime
classification framework, two-layer ARIMAX + Random Forest forecasting, and
backtesting of regime-conditional bidding strategies for a BESS operator.

---

## Why it matters

Treating TTF as a fixed-coefficient driver in any MB forecasting framework
mis-specifies its role. This feeds the two-layer thesis architecture: TTF
enters the ARIMAX volume forecast with a regime-conditional treatment rather
than a single coefficient.

---

## Data and method

| Source        | Data                       | Frequency | Period          |
|---------------|----------------------------|-----------|-----------------|
| Yahoo Finance | TTF settlement (`TTF=F`)   | Daily     | Jan 2022 – 2026 |
| TERNA         | MB volumes and prices, IT-North/Centre | 15-min | Jan 2024 – 2026 |

15-min MB data aggregated to daily median (robust to intraday outliers).
Five univariate OLS `power = α + β·TTF + ε` via `statsmodels`, subsetting
at quarter-hourly resolution before daily aggregation. Structural break
identified via TTF `pct_change` (2 March 2026, first trading day after the
closure announced 28 February).

---

## Notes

- Pre-2024 TTF excluded to avoid the 2022 crisis singularity dominating the fit
- Post-Hormuz sample (42 days) is small — direction is clear, magnitude preliminary
- Thin threshold [−50, +50] MWh inherited from
  [Thin-Market-Regime-MB](https://github.com/MatteoEPoliti/Thin-Market-Regime-MB);
  sensitivity analysis in the full thesis
- OLS measures statistical association, not causation — the gas-power causal
  link is grounded in the ~45% CCGT share of Italian generation

---

## Stack

`Python` · `pandas` · `statsmodels` · `matplotlib` · `yfinance`
