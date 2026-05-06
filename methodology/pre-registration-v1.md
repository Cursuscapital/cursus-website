# Undertow v1 Calibration: Pre-registered Regime Windows

**Author:** Ian Richter, Cursus Capital
**Date:** 2026-05-06
**Status:** LOCKED before backfill execution

This document defines pre-registered historical regime windows for validating Undertow v1 thresholds against backfilled data. Windows, expectations, and pass/fail rules are committed before the backfill is run, to prevent post-hoc rationalization.

---

## Window 1: COVID Crash (Deep Capitulation)

**Date range:** 2020-03-09 to 2020-03-23

**Rationale:** BTC crashed from $7,935 to $3,860 over 2 days starting March 12, 2020 — single-day drop >39%. Window covers approach, crash, and immediate aftermath before recovery began. Classic liquidity-event capitulation, externally well-documented.

**Falsifiable expectations:**
- value_score >= 75th percentile on >=70% of days in window
- risk_score <= 25th percentile on >=70% of days in window
- At least 5 of 11 trading days should map to "Fat Pitch" (Risk Off + Cheap)

---

## Window 2: November 2021 Cycle Top

**Date range:** 2021-11-05 to 2021-11-20

**Rationale:** BTC ATH of $69,045 on November 10, 2021. Window tightened to 15 days bracketing the peak (per Viktor review) to avoid dilution from October run-up phase. Funding rates extreme, retail euphoria documented in sentiment indices.

**Falsifiable expectations:**
- risk_score >= 75th percentile on >=60% of days
- value_score <= 25th percentile on >=60% of days
- At least 5 of 15 days should map to "Trim" (Risk On + Not Cheap)

---

## Window 3: FTX Capitulation (Deep Capitulation)

**Date range:** 2022-11-08 to 2022-11-30

**Rationale:** FTX bankruptcy filing on November 11, 2022. BTC fell to two-year low of $15,480 on November 21. Industry-wide contagion event. Glassnode realized-loss spike documented externally as canonical capitulation reference point.

**Falsifiable expectations:**
- value_score >= 75th percentile on >=70% of days
- risk_score <= 25th percentile on >=70% of days
- At least 8 days should map to "Fat Pitch"

---

## Window 4: Grinding Bear Mid-Phase

**Date range:** 2022-08-01 to 2022-10-31

**Rationale:** Quiet bear-market grinding period between Luna/3AC deleveraging and FTX collapse. BTC traded $19k-$23k range without extreme stress events. Tests whether framework distinguishes "acute capitulation" from "extended bear market normalcy".

**Falsifiable expectations:**
- risk_score in 25th-50th percentile range on >=60% of days
- value_score >= 50th percentile on >=70% of days
- Should NOT predominantly trigger "Fat Pitch" (reserved for acute events)
- Predominant cell expected: "DCA" (Cautious + Cheap)

---

## Window 5: Grinding Bull Mid-Phase

**Date range:** 2024-01-15 to 2024-03-15

**Rationale:** Spot Bitcoin ETF approval period, BTC rising from $45k to $73k. Sustained healthy uptrend without late-cycle euphoria. Tests whether framework correctly identifies "early-to-mid bull" without false-positive Trim signals.

**Falsifiable expectations:**
- risk_score >= 50th percentile on >=70% of days
- value_score declining over the window from >50th percentile toward <50th percentile
- Transition from "Accumulate"/"DCA" early in window toward "Ride" late in window
- Should NOT predominantly trigger "Trim" until late in window

---

## Methodological Notes

### Asymmetric hit-rate thresholds (70% capitulation, 60% other)

Acute events (Windows 1, 3) are externally well-defined and short-duration (11-23 days), giving cleaner signals. Extended regimes (Windows 4, 5) include inherent boundary days where regime classification is genuinely ambiguous. The asymmetry reflects this structural difference, not a design preference.

### Overall pass/fail rule

The framework passes v1 calibration if AT LEAST 4 of 5 windows hit all stated expectations, with NO single window missing any threshold by more than 15 percentage points.

Failure to meet this triggers a calibration revision before any public methodology update.

### Precision-side metric (complement to recall)

Computed only for cells with directly corresponding pre-registered windows in v1:
- **Fat Pitch:** Windows 1 (COVID) + 3 (FTX)
- **Trim:** Window 2 (Nov 2021 ATH)
- **Accumulate, DCA, Ride:** Windows 4 (Grinding Bear) + 5 (Grinding Bull)

For each tested cell: of all backfilled days where the cell fires, what proportion fall inside an appropriate pre-registered window? Framework should achieve at least 60% precision on each tested cell.

Hold, Reduce, and Defend are NOT precision-tested in v1. No pre-registered windows in this batch target the conditions these cells are designed for. Precision tests for these cells will be added in v2.

---

**Locked: 2026-05-06**

No window definitions, expectations, or pass/fail rules will be modified after the commit hash of this file. Any deviations from these specifications during the backfill or methodology revision must be documented as explicit overrides in a v1.1 addendum, not silent changes.