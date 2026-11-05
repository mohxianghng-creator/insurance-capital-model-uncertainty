# Validation scope and acceptance criteria

This document separates software correctness, numerical accuracy, statistical
calibration and decision usefulness. Passing one category does not establish the
others. This case study has not undergone independent validation or institutional approval.

| Question | Evidence | Acceptance / interpretation |
|---|---|---|
| Do severity and aggregation calculations agree with independent references? | Analytic distribution properties, quadrature, Panjer recursion, Monte Carlo and regression tests | Tests must pass in the pinned Python environment. Numerical reference tests have explicit tolerances. |
| Does an optimizer return a credible fit? | Analytic-score lognormal fit compared with derivative-free likelihood optimization; convergence checks and fallback | A successful fit must have finite objective and a small score; an unsuccessful fallback raises rather than returning an unchecked fit. This does not prove global optimality for arbitrary data. |
| Does the FFT domain alter the answer? | Compact-versus-padded CDF tests; fitted-point step/range tests; expanded predictive quantiles with halved-step checks | Compact alias mass bound <1.13e-7 at tilt 16; expanded predictive quantiles require <=0.5% refinement change. No acceptance of an unresolved capital as a finite number. |
| Is the capital interval calibrated in repeated samples? | Fresh W2/W3/W5 coverage study; 95% Wilson intervals for the coverage estimate | Report deviations from 90%, width and infinite endpoints together. Nominal coverage inside a Monte Carlo interval is not a certificate of calibration. |
| Is inner simulation noise material? | Paired B=80/B=240 diagnostic on fixed data, followed by targeted B=960 in W1/W4 | Report relative changes; do not interpret differences of comparable size as a stable ordering. Twelve pairs per world and a selected follow-up are diagnostics, not a production precision certificate. |
| Does a rule have lower decision cost? | Same-sample differences at cost ratios 1/3/10; pointwise paired bootstrap intervals | No finite comparison when a capital output is unresolved. No universal winner from selected worlds, assumed costs or multiple unadjusted comparisons. |
| Does the second dataset behave sensibly? | Norwegian annual PITs and largest-claim/recent-window refits | Investigate distributional over-prediction. Four forecast years cannot establish extreme-quantile calibration. Deleting a valid large claim is a diagnostic, not a data-cleaning policy. |
| Is a treaty action feasible? | Finite per-occurrence cover, fixed pricing assumptions, common feasible action set under resource budgets | Net VaR plus upfront premium must fit the budget in every included risk view. If none does, report no feasible action. |
| Can the published evidence be traced? | Source/dependency/seed metadata, raw-data hashes, result verification and file inventory | Source drift and incomplete replication grids fail verification. Hashes establish byte consistency, not review independence or economic validity. |

## Change classification

Changes to severity fitting, aggregation, uncertainty intervals or rule
construction require fresh current-engine validation. Historical simulation
files are retained for comparison and must not be relabelled as new evidence.
Changes to narrative or UI must be checked against the saved numerical outputs.

A narrow accepted-cache migration is recorded in
`results/current/accepted_guard_cache_provenance.json`: adding a failure after
unsuccessful grid refinement changed no calculation for previously accepted
finite tasks. Verification checks the exact source delta and unchanged
published values. This exception does not permit reuse after a changed fit,
distribution, seed scheme or decision rule.

The current empirical pipeline, fresh validation study and extension studies
have separate reproduction commands. CI runs tests and saved-evidence checks;
it does not silently rerun hours of nested simulations.

Git attributes preserve exact bytes across Windows/Linux checkouts so that
automatic newline conversion does not invalidate source and evidence hashes.

## Remaining model risks

- Synthetic worlds all use Poisson annual counts and are anchored to the same
  Danish fit. Real-data NB/trend comparisons do not validate frequency
  misspecification in the synthetic decision study.
- Claim resampling is iid. Temporal clustering, common-event dependence and
  portfolio exposure changes are not reconstructed from these datasets.
- Candidate-model uncertainty is narrower than uncertainty about the entire
  risk-generating process. A model-set maximum does not protect against all
  unseen tail regimes.
- Semi-Bayesian tail support and body treatment remain judgement choices.
  Prior sensitivity and repeated-sample calibration address different questions.
- A finite numerical quantile does not ensure a stable mean decision cost.
  Heavy-tailed fitted-capital estimates can leave large Monte Carlo error.
- Finite-layer quotes assume full recoverability, immediate recovery and no
  aggregate cap or reinstatement. These omissions can change a real decision.

## Professional context

EIOPA's [validation tools](https://www.eiopa.europa.eu/rulebook/solvency-ii-single-rulebook/article-5901_en)
connect sensitivity and stability analysis with decisions and reverse stress
testing. This is context for the validation questions above, not a claim that
this project implements an approved Solvency II internal model.

## Optional browser verification

The explorer has 18 empirical-control combinations, 54 treaty scenarios and
eight horizon/cost-ratio cases. The browser check compares displayed treaty
choices with saved evidence and recomputes displayed decision costs from raw
simulation samples. It also checks JavaScript errors, mobile overflow and
automated WCAG 2 A/AA findings in light and dark explorer themes.

The following PowerShell commands keep optional tooling inside ignored `work/`:

```powershell
npm install --prefix work/browser-check playwright@1.64.1 @axe-core/playwright@4.14.0 --no-audit --no-fund
$env:PLAYWRIGHT_BROWSERS_PATH = Join-Path (Get-Location) 'work/pw-browsers'
$env:TMP = Join-Path (Get-Location) 'work'
$env:TEMP = $env:TMP
node work/browser-check/node_modules/playwright/cli.js install chromium --only-shell
node scripts/check_browser.cjs
```

Automated accessibility checks do not replace a manual accessibility review.
Python CI checks scientific and saved-output integrity; the optional browser
runtime is not a scientific dependency.
