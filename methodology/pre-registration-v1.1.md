# Undertow v1.1 Calibration: Pre-registered Regime Windows

**Author:** Ian Richter, Cursus Capital
**Date:** 2026-05-07
**Status:** LOCKED before v1.1 refactor execution
**Predecessor:** v1 pre-registration at commit bef0780 (FAILED v1 calibration on 2026-05-06)

This document defines pre-registered historical regime windows and validation criteria for v1.1 of the Undertow framework. The v1.1 design incorporates three structural modifications relative to v1, derived from the diagnostic-of-the-diagnostic analysis on 2026-05-06 and reviewed by external advisor:

1. Cell-family recall testing (replacing percentile-based recall testing) to remove tautology risk under percentile-rank component transformations
2. Independent operational labels for per-cell precision testing (replacing windows-based precision metric) to address the structural sparsity issue (windows cover only 6.8% of backfill)
3. Time-gated liquidity component (≥2021-01-01 only) with weight renormalization for pre-2021 days, due to upstream DefiLlama coverage limitations

Windows, expectations, and pass/fail rules are committed before the v1.1 refactor runs and before validation results are examined. Same five windows as v1 (no window changes).

---

## Pre-Registered Windows (unchanged from v1)

### Window 1: COVID Crash (Deep Capitulation)
**Date range:** 2020-03-09 to 2020-03-23

**Note:** Liquidity component unavailable for this window (pre-2021 time-gate). Risk score computed as 4-component composite for these days with weight renormalization.

### Window 2: November 2021 Cycle Top
**Date range:** 2021-11-05 to 2021-11-20

### Window 3: FTX Capitulation (Deep Capitulation)
**Date range:** 2022-11-08 to 2022-11-30

### Window 4: Grinding Bear Mid-Phase
**Date range:** 2022-08-01 to 2022-10-31

### Window 5: Grinding Bull Mid-Phase
**Date range:** 2024-01-15 to 2024-03-15

---

## Cell Families (Recall Testing Structure)

Cell families correspond to the three rows of the matrix, mapped along the risk dimension. Recall validation tests whether the framework correctly identifies the risk regime of each pre-registered window.

| Family | Cells | Risk Regime |
|---|---|---|
| **Defensive** | Fat Pitch, Reduce (Risk Off + Fair), Defend | Risk Off (R<35) |
| **Cautious** | DCA, Hold, Reduce (Cautious + Not Cheap) | Cautious (35≤R<65) |
| **Aggressive** | Accumulate, Ride, Trim | Risk On (R≥65) |

No overlapping memberships. Each cell belongs to exactly one family corresponding to its risk-row position.

**Note on Reduce disambiguation:** the v1 matrix contains two cells labeled "Reduce" with different semantic meanings — (Cautious + Not Cheap) and (Risk Off + Fair). For validation purposes throughout this document, these are referred to with row-suffix disambiguation. Methodology page revision in v1.1 release will address this naming overlap; the underlying cell semantics are unchanged.

**Rationale:** Cell families are a validation tool, not a market-condition taxonomy. They test whether the framework places each window in roughly the right risk regime. Value-dimension discrimination (which specific cell within a family) is tested separately via per-cell precision metrics against independent operational labels.

---

## Window Expectations (Recall, in Cell-Family Terms)

### Window 1: COVID Crash
- ≥70% of window days in **Defensive** family
- Asymmetric threshold rationale: acute event, externally well-defined, short duration (15 calendar days)

### Window 2: November 2021 Cycle Top
- ≥60% of window days in **Aggressive** family
- Note: Aggressive includes Trim, the cell expected to fire during late-cycle euphoria. The family-level test captures "framework recognizes this as a Risk On regime" without requiring the framework to land specifically on Trim every day.

### Window 3: FTX Capitulation
- ≥70% of window days in **Defensive** family

### Window 4: Grinding Bear Mid-Phase
- ≥60% of window days in **Cautious** OR **Defensive** families combined
- Rationale: extended bear without acute crisis; framework may shift between Cautious and Defensive across the window without acute capitulation
- **Note on test design:** this test is designed to verify "not Aggressive" rather than "specifically Cautious." A degenerate case where the framework classifies W4 as 95% Defensive would pass the v1.1 test. This is acceptable for v1.1 and observed in the validation output. If the degenerate case occurs, criteria differentiation is a v1.2 design question.

### Window 5: Grinding Bull Mid-Phase
- ≥70% of window days in **Aggressive** family
- **Trajectory expectation** (two conditions, both must hold):
  (a) Trim fires on <25% of days in the first two-thirds of W5 (2024-01-15 through 2024-02-22)
  (b) Trim fires more frequently in the final third (2024-02-23 through 2024-03-15) than in the first two-thirds
- This trajectory test is an additional criterion alongside the family recall threshold and must also be satisfied for W5 to pass.
- Rationale: tests whether the framework tracks the trajectory of a bull market or just the endpoint

---

## Independent Operational Labels (Precision Testing)

Each label is computable purely from BTC daily price data, using only trailing windows. No dependency on Undertow scores or framework outputs.

| Cell | Operational Label |
|---|---|
| **Fat Pitch** | Day is in BTC drawdown >30% from trailing 90d high |
| **Trim** | Day is within 15% below trailing 365d high AND BTC has gained >50% over trailing 180d |
| **Defend** | Day is in 90d drawdown >20% AND drawdown is from a high that itself was within 15% of the trailing 365d high |
| **Accumulate** | Day is >40% below trailing 365d high AND BTC has gained 20-40% over trailing 90d |
| **Ride** | Day is within 30% of trailing 365d high AND BTC has gained >0% over trailing 90d AND is not in 90d drawdown >15% |
| **DCA** | Day is >30% below trailing 365d high AND not in 90d drawdown >15% AND no >20% gain in trailing 90d |

**Cells NOT precision-tested in v1.1:** Hold, Reduce (Cautious + Not Cheap), Reduce (Risk Off + Fair). These cells represent absence of clear directional signal or transitional regimes for which clean external operational labels cannot be constructed without circularity.

**Note on label overlap:** Multiple labels may apply to the same calendar day. This is expected — the precision metric tests "when the framework fires Cell X, does Label X apply", not "Label X is exclusive". Overlapping labels are acceptable.

---

## Precision Expectations

For each precision-tested cell: of all days where the framework fires that cell over the full backfill, the proportion of days where the corresponding operational label applies must be ≥60%.

| Cell | Required Precision |
|---|---|
| Fat Pitch | ≥60% |
| Trim | ≥60% |
| Defend | ≥60% |
| Accumulate | ≥60% |
| Ride | ≥60% |
| DCA | ≥60% |

---

## Liquidity Component Time-Gating

Pre-2021-01-01 stablecoin supply data from DefiLlama is structurally unreliable due to upstream coverage limitations (e.g., 2018-01-01 reported total: ~$110K, vs USDT alone ~$1.3B at the time). To avoid corrupting historical readings with coverage-growth artifacts, the liquidity component is time-gated.

**Rule:** For any date before 2021-01-01:
- Liquidity component output: NaN
- Risk score: computed as 4-component composite (Trend, Momentum, Volatility, Derivatives only)
- Component weights renormalized so remaining 4 components sum to 100%
- Score output flagged with "4-component" indicator

For Window 1 (COVID 2020-03), the framework operates on 4-component risk scores. Recall expectations for W1 are tested against this 4-component composite.

---

## Pass/Fail Rule

The framework passes v1.1 calibration if ALL THREE conditions are met:

1. AT LEAST 4 of 5 windows hit their stated cell-family recall threshold (and W5 additionally satisfies its trajectory expectation)
2. NO single window misses its threshold by more than 15 percentage points
3. ALL 6 precision-tested cells achieve ≥60% precision against their independent operational label

---

## Stop Rule (Critical)

If v1.1 validation fails:
- The framework does NOT go live with v1.1 thresholds
- The public methodology page is updated to reflect "v1.1 calibration in progress"
- Threshold tuning against failed validation results is PROHIBITED — this would be the seed-shopping trap
- The next iteration requires a NEW pre-registration with new windows or expectations, validated from scratch

This rule applies regardless of how close v1.1 was to passing. Iterative parameter-tuning against any pre-registered validation destroys the discipline.

---

## v1.1 Refactor Specifications (for reference, not part of locked pre-registration)

The component refactor that v1.1 validates includes:

1. **Bubble component:** replace linear cap-and-normalize with rolling-365d-percentile-rank
2. **Liquidity component:** rebalance mapping center to underlying-distribution median (or rolling-percentile-rank); time-gate to ≥2021
3. **MA200W component:** replace asymmetric cap [-0.5, +1.5] with empirically-derived bounds (or rolling-percentile-rank)
4. **Trend, Momentum:** unchanged (bimodality is real market signal); thresholds in coord_cell() recalibrated post-component-refactor
5. **Volatility:** unchanged (design template for percentile-rank approach)
6. **Derivatives:** unchanged (live Binance/CoinGlass divergence addressed as separate operational ticket)
7. **Composite thresholds:** respaced to empirical CDF tertiles after component refactor

These refactor specs are documented here for context but are not part of the locked pre-registration. Refactor implementation may discover that specific component changes need adjustment; only the pre-registration criteria (windows, families, expectations, labels, pass/fail, stop rules) are locked.

---

## Locked: 2026-05-07

No window definitions, expectations, family mappings, operational labels, precision thresholds, pass/fail rules, or stop rules will be modified after the commit hash of this file. Any deviations from these specifications during refactor or validation must be documented as explicit overrides in a v1.2 addendum, not silent changes.

---

## Appendix A: v1.2-vs-v2 Decision (locked 2026-05-11)

If v1.1 validation fails per the pass/fail rule defined above, the response is:

**Withdraw Undertow's public version and rebuild as v2 from the ground up.** 
Do NOT iterate via v1.2 with a third pre-registration cycle.

Rationale (per external advisor review, 2026-05-11):
Three pre-registrations in two weeks would read as a calibration crisis 
rather than discipline, regardless of whether each individual iteration 
was genuinely pre-registered. The discipline holds at the per-iteration 
level but degrades at the meta-level when iteration speed is high.

v2 rebuild specifics:
- Public methodology page replaced with "v1 retired pending v2 redesign" 
  statement
- Live dashboard either taken offline or marked as "historical illustration 
  only — v2 in development"
- v2 design begins from first principles, with a planning horizon of weeks 
  rather than days
- No artificial timeline pressure on v2; it ships when it's right, not when 
  v1.1's failure timer expires

This decision is committed before v1.1 validation runs, to remove the 
possibility of post-hoc rationalization toward v1.2 under time pressure 
if v1.1 fails.

---

## Appendix B: Refactor Execution Discipline (locked 2026-05-11)

Per external advisor instruction:

1. **Script-SHA logging:** The validation output must record both the 
   pre-registration commit hash AND the git SHA of the refactor/validation 
   script used. This enables exact reproducibility.

2. **No mid-run adjustments:** Refactor and validation run end-to-end. 
   Intermediate results are NOT inspected with the goal of adjusting code 
   mid-run.

3. **Code-bug protocol:** If a clear code bug is identified during refactor 
   execution, partial results are discarded, the bug is fixed, the script 
   is recommitted (new SHA), and the run restarts from scratch. No partial 
   carrying-forward of intermediate results.

4. **Validation output as artifact:** The validation output is a committed 
   artifact in the repository (under methodology/validations/v1.1/), not 
   just an email attachment. Both pass and fail outputs are committed.