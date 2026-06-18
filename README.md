# Post-Submission Review of HMDA LAR Reporting Pipeline

A data reconciliation audit that compares a bank's submitted HMDA LAR regulatory file against its later-revised version, quantifies exactly what changed and why, and tests whether the discrepancies reflect a data pipeline failure or a real shift in lending behavior.

## Background

Under the Home Mortgage Disclosure Act (HMDA), lenders submit a Loan Application Register (LAR) to the CFPB each year. When a bank later issues a revised LAR, that revision is a regulatory red flag: examiners and senior leadership need to know whether the changes signal a data quality problem, an underwriting/process issue, or an actual change in the loan portfolio. Manually eyeballing 100k+ records across 86 fields is not viable, and getting the diagnosis wrong has real consequences — either a costly overreaction to noise, or a missed control failure that puts future submissions at risk.

This project answers, with statistical rigor, whether the differences between the submitted and revised files are systemic data-pipeline noise or a genuine business signal — and what to do about it.

## Approach

The analysis (`HMDA_Reconciliation.ipynb`) runs as a single reproducible pipeline over the submitted and revised LAR extracts:

1. **Population reconciliation** — set-based comparison on `application_id` to split records into Matched, Dropped (submitted-only), and Added (revised-only) populations.
2. **Field-level change audit** — vectorized boolean comparison across all 86 fields for matched records, classifying each discrepancy (numeric shift, categorical remap, null fill/removal) and ranking fields by how often they changed.
3. **KPI drift analysis** — approval/denial/withdrawal rate shifts, segmented by loan type, loan purpose, and geography, both on the full population and the matched-only subset (to isolate "values changed" from "population changed").
4. **Statistical significance testing** — two-sample Kolmogorov-Smirnov tests on continuous fields (income, DTI, CLTV, rate spread, interest rate) to separate real distributional drift from sampling noise.
5. **Anomaly detection** — boundary-breach checks on regulatory thresholds (e.g., CLTV pricing variance, Qualified Mortgage DTI cutoff at 43%, loan term/maturity rewrites) plus a 3-sigma control-limit scan for geographic outliers.
6. **Visualization** — four explanatory charts (income distribution by retained/dropped population, CLTV density shift, approval rate by segment, rate spread variance by state) saved for the executive deliverable.

## Key Findings

- **Root cause was a pipeline timing/mapping issue, not a market or underwriting shift.** 9,929 applications (7.6% of volume) were dropped between submission and revision due to a population sync cutoff, and ~1 in 5 matched loans had incorrect CLTV or loan term values due to a data engineering mapping error.
- **The business narrative didn't change.** Despite the high error volume, median income held flat at $100k and approval rates shifted by under 1 percentage point — the noise was loud, but not material to portfolio risk.
- **CLTV showed statistically significant drift** (KS p < 0.001) while income, DTI, rate spread, and interest rate did not — pinpointing exactly which fields need engineering fixes versus which are stable.
- Recommended a move from reactive, end-of-cycle audits to an immutable "regulatory data mart" with automated `dbt`-style data contract tests (not-null assertions, threshold bounds) and drift alerting before data reaches the CFPB.

Full writeup: `Patil_Vishal_Executive_Summary.pdf` (leadership-facing) and `Patil_Vishal_Project_Lighthouse_Deliverable.pdf` (full technical detail).

## Tech Stack

Python 3.9, pandas, NumPy, SciPy (`scipy.stats` — KS tests), matplotlib, seaborn.

## Running It

```bash
pip install pandas numpy matplotlib seaborn scipy jupyter
```

Place `LAR_Submitted.csv` and `LAR_Revised.csv` (raw HMDA LAR extracts, both with an `application_id` primary key) in the project root, then run:

```bash
jupyter nbconvert --to notebook --execute HMDA_Reconciliation.ipynb
```

The notebook prints each reconciliation phase to stdout and writes `Deliverable_Task1_FieldChanges.csv` plus four PNG charts to the working directory.

**Note:** the underlying LAR data files are not included in this repository (confidential/assessment data); the notebook is shared for methodology review.
