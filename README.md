# When Risk Models Disagree: Insurance Capital & Reverse Stress Testing

**Which capital level and reinsurance actions remain defensible when credible risk models disagree?**

A non-life actuarial research case study connecting public fire-loss data, tail modelling, parameter uncertainty, finite reinsurance layers and reverse stress testing.

## What the evidence shows

- Danish severity/frequency choices produce annual VaR99.5 resource levels of roughly **1.14–2.30 billion DKK**. Parameter uncertainty can exceed the spread of fitted models.
- The AIC-selected Norwegian baseline is more conservative than alternatives: an adjustment should not mechanically add capital to every chosen baseline.
- Numerical checks can pass while statistical intervals under-cover. Nominal 90% bootstrap intervals cover 85.5% and 83.5% in two current synthetic worlds.
- Finite treaty limits, assumed prices and resource budgets change the conditional action. **No cover** and **no feasible action** remain valid outputs.

`V = VaR99.5(annual retained aggregate loss)` is a total resource level. `K = V - E[S]` is separately reported economic capital above expected loss. Neither is a complete regulatory SCR.

## Review in five minutes

1. Read the [decision memo](docs/DECISION_MEMO.md) for the business question, quotation thresholds and conditional actions.
2. Download and open the [offline evidence report](app/evidence_report.html) and [interactive decision explorer](app/decision_explorer.html).
3. Read the [current validation](paper/current_validation.md), [model card](docs/MODEL_CARD.md) and [validation criteria](docs/VALIDATION.md).
4. Consult the [research paper](paper/paper.md) for methods, empirical results and the decision framework.

![Current repeated-sample interval coverage](paper/figures/F6_current_coverage.png)

## Design and implementation

Truncated-lognormal, Lomax and lognormal/GPD splice severities; Poisson, negative-binomial and trend counts; exponentially tilted FFT aggregation; bootstrap and semi-Bayesian predictive mixtures; finite-set asymmetric capital rules and constrained minimax-regret treaty decisions.

Current decision evidence uses five known worlds, T=11/40 and 150 independently seeded samples per world/horizon, with B=80 refits. Coverage uses 200 samples in W2/W3/W5 and B=200. Matched T=11 samples overlap across experiments. Paired B=240 diagnostics and a targeted B=960 follow-up expose internal simulation sensitivity; they do not replace selected rows or validate every rule ranking.

## Reproduce with Python 3.12

```text
python -m venv .venv
# Activate .venv using your platform's normal command.
python -m pip install -r requirements-lock.txt
python scripts/reproduce.py verify
```

Verification runs unit/numerical tests, raw-to-result/explorer checks, current-evidence provenance/completeness/arithmetic checks and the file inventory. Scratch files stay in ignored `work/`. The published engine and numerical results are unchanged in this curated distribution.

To intentionally rebuild all evidence, run `python scripts/reproduce.py all`. This is a substantial compute job. `empirical` and `research` modes rebuild subsets; independent seeds and source-aware checkpoints protect compatible resumption. Worker count can be set in `scripts/run_research.py --workers 4` and `scripts/run_precision_refinement.py --workers 4`. The existing regeneration entry point explicitly writes a new inventory after successful regeneration; review generated changes before publishing.

## Limits that matter

B=80 rule rankings remain exploratory: some fixed-sample estimates change materially even in the higher-B follow-up. The W4 tail-break world leaves large estimation shortfalls. Pointwise simulation intervals are unadjusted and can miss rare extremes. Four to six annual backtest observations cannot validate a 0.5% annual tail.

Synthetic worlds use Poisson counts and do not validate frequency misspecification or event dependence. Exposure, inflation/threshold alignment, claims development, real treaty quotations, counterparty risk and recovery timing are absent. Finite treaties assume full and immediate recovery. Institutional use would require portfolio data, terms, funded risk appetite and independent review.

Historical `sim2_*`, `sim3_post_T11.csv` and `COVERAGE*` evidence is retained because the paper and verification use it; it is explicitly separate from `results/current/`. The current study does not establish extreme-tail calibration or an institution-specific capital recommendation.

[Public-release scope](docs/PUBLIC_RELEASE.md) · [Data attribution and licences](THIRD_PARTY_NOTICES.md) · [Publication terms](PUBLICATION_TERMS.md)

Development and documentation used AI assistance. The GitHub Actions workflow has not yet been verified on GitHub.
