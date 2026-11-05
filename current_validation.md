# Current-engine validation and decision evidence

This report is generated from `results/current/`. Historical simulation files are not used in these tables.

## Experimental design

Five synthetic worlds, 150 independently seeded samples per world at each of T=11 and T=40; B=80 refits per plausible model. All rules share the same sample within a replication. A separate T=11 coverage experiment uses 200 samples per world in W2/W3/W5 and B=200. The first 12 decision samples per world are also fitted with B=240 to assess inner simulation sensitivity. Coverage and decision experiments reuse matching T=11 data-sample IDs; the additional coverage IDs supply further samples. Reported counts are analysis cases, not disjoint datasets across experiments. Random streams are recorded in `run_metadata.json`; failed tasks abort rather than disappear.

Initial aggregation uses a 0.5 grid and a compact, exponentially tilted aggregate domain. The circular alias mass bound is below 1.13e-7; compact-versus-padded CDF and quantile tests pass. Predictive spectra are averaged before a single inverse FFT, which is equivalent by linearity to mixing conditional distributions. Unresolved predictive quantiles are recomputed on expanded domains up to 2,097,152. A halved grid step must change a resolved expanded quantile by no more than 0.5%. Unresolved outputs retain their final evaluated ceiling and unbounded cost. DGP quantiles are independently checked on a finer, larger grid. Finite initial-grid points rely on the existing fitted-grid checks; the inner bootstrap study does not establish convergence for every possible sample.

Reverse-stress boundaries target 5%/50% using lattice quantiles. Achieved strict probabilities at the 5% target range from 4.979% to 4.996%; adjacent-cell masses explain the difference. 3 searches do not reach the requested target within the prescribed stress range and retain that explicit status. See `stress_target_diagnostics.csv`.

## Current interval coverage

| World | Method | Coverage | Pointwise 95% Monte Carlo interval | Infinite upper endpoint | Median width ratio |
|---|---|---:|---:|---:|---:|
| W2 | bootstrap | 85.5% | [80.0%, 89.7%] | 2.5% | 2.80 |
| W2 | semi_bayesian | 90.0% | [85.1%, 93.4%] | 9.5% | 4.08 |
| W3 | bootstrap | 83.5% | [77.7%, 88.0%] | 44.0% | 11.05 |
| W3 | semi_bayesian | 90.0% | [85.1%, 93.4%] | 66.0% | inf |
| W5 | bootstrap | 89.5% | [84.5%, 93.0%] | 0.0% | 1.24 |
| W5 | semi_bayesian | 92.5% | [88.0%, 95.4%] | 0.0% | 1.48 |

Wilson intervals describe finite-replication error. Inclusion of 90% is not proof of calibration. Every known true quantile in this experiment is below the base 16,384 ceiling. Thus the coverage event remains identified when an endpoint is right-censored above that ceiling: an upper endpoint still exceeds the truth, and a censored lower endpoint still excludes it. Width is then only partially identified; an infinite marker does not imply an infinite model quantile. Semi-Bayesian credible intervals and bootstrap intervals have different interpretations. These repeated-sample results evaluate both as procedures under the stated worlds, without giving either a universal frequentist guarantee.

## Decision costs and estimation shortfalls

Loss is 3 × relative capital shortfall + relative capital excess. It is an illustrative asymmetric decision cost, distinct from realised annual loss and from a regulatory capital requirement.

| T | World | Rule | P(estimate below truth) | Mean loss r=3 | Censored | Expanded |
|---:|---|---|---:|---:|---:|---:|
| 11 | W1 | D0 plug-in | 43.3% | 0.347 | 0.0% | 0.0% |
| 11 | W1 | D1 mixture | 8.0% | 0.781 | 0.0% | 0.0% |
| 11 | W1 | D3 theta=0.5 | 2.7% | 0.980 | 0.0% | 0.7% |
| 11 | W1 | D3 theta=0.75 | 1.3% | 1.446 | 0.0% | 0.7% |
| 40 | W1 | D0 plug-in | 51.3% | 0.209 | 0.0% | 0.0% |
| 40 | W1 | D1 mixture | 19.3% | 0.280 | 0.0% | 0.0% |
| 40 | W1 | D3 theta=0.5 | 4.7% | 0.340 | 0.0% | 0.0% |
| 40 | W1 | D3 theta=0.75 | 0.7% | 0.514 | 0.0% | 0.0% |
| 11 | W2 | D0 plug-in | 52.0% | 0.547 | 0.0% | 0.0% |
| 11 | W2 | D1 mixture | 38.0% | 0.625 | 0.0% | 0.0% |
| 11 | W2 | D3 theta=0.5 | 26.0% | 0.879 | 0.0% | 1.3% |
| 11 | W2 | D3 theta=0.75 | 20.0% | 1.130 | 0.0% | 1.3% |
| 40 | W2 | D0 plug-in | 50.0% | 0.240 | 0.0% | 0.0% |
| 40 | W2 | D1 mixture | 40.7% | 0.237 | 0.0% | 0.0% |
| 40 | W2 | D3 theta=0.5 | 40.7% | 0.240 | 0.0% | 0.0% |
| 40 | W2 | D3 theta=0.75 | 40.0% | 0.242 | 0.0% | 0.0% |
| 11 | W3 | D0 plug-in | 62.7% | 1.412 | 0.0% | 4.0% |
| 11 | W3 | D1 mixture | 48.7% | 1.482 | 0.0% | 12.0% |
| 11 | W3 | D3 theta=0.5 | 46.0% | 1.502 | 0.0% | 18.7% |
| 11 | W3 | D3 theta=0.75 | 38.0% | 1.733 | 0.0% | 18.7% |
| 40 | W3 | D0 plug-in | 50.0% | 0.703 | 0.0% | 0.7% |
| 40 | W3 | D1 mixture | 43.3% | 0.711 | 0.0% | 0.7% |
| 40 | W3 | D3 theta=0.5 | 40.7% | 0.690 | 0.0% | 0.7% |
| 40 | W3 | D3 theta=0.75 | 39.3% | 0.690 | 0.0% | 0.7% |
| 11 | W4 | D0 plug-in | 88.7% | 1.463 | 0.0% | 0.7% |
| 11 | W4 | D1 mixture | 82.0% | 1.462 | 0.0% | 2.0% |
| 11 | W4 | D3 theta=0.5 | 78.0% | 1.382 | 0.0% | 3.3% |
| 11 | W4 | D3 theta=0.75 | 73.3% | 1.488 | 0.0% | 3.3% |
| 40 | W4 | D0 plug-in | 97.3% | 1.304 | 0.0% | 0.0% |
| 40 | W4 | D1 mixture | 94.7% | 1.224 | 0.0% | 0.0% |
| 40 | W4 | D3 theta=0.5 | 94.7% | 1.218 | 0.0% | 0.0% |
| 40 | W4 | D3 theta=0.75 | 94.7% | 1.213 | 0.0% | 0.0% |
| 11 | W5 | D0 plug-in | 51.3% | 0.109 | 0.0% | 0.0% |
| 11 | W5 | D1 mixture | 26.7% | 0.119 | 0.0% | 0.0% |
| 11 | W5 | D3 theta=0.5 | 18.7% | 0.169 | 0.0% | 0.0% |
| 11 | W5 | D3 theta=0.75 | 18.7% | 0.210 | 0.0% | 0.0% |
| 40 | W5 | D0 plug-in | 56.0% | 0.069 | 0.0% | 0.0% |
| 40 | W5 | D1 mixture | 48.0% | 0.063 | 0.0% | 0.0% |
| 40 | W5 | D3 theta=0.5 | 48.0% | 0.063 | 0.0% | 0.0% |
| 40 | W5 | D3 theta=0.75 | 48.0% | 0.063 | 0.0% | 0.0% |

Paired bootstrap intervals and loss bounds for cost ratios 1, 3 and 10 are in `decision_paired.csv`. They are pointwise, simulation-conditional intervals, with no multiplicity adjustment. They resample finite paired losses and can miss rare extremes; their coverage is not established when loss moments are uncontrolled. Finite grid quantiles do not establish existence of population mean or variance of decision cost. Empirical MCSE columns are descriptive finite-sample calculations. These diagnostics do not select an optimal actual-insurer rule.

## Inner refit sensitivity

| World | Rule | Median relative change B=80 to B=240 | Maximum | Finite paired samples |
|---|---|---:|---:|---:|
| W1 | D0 plug-in | 0.0% | 0.0% | 12/12 |
| W1 | D1 mixture | 12.4% | 189.6% | 12/12 |
| W1 | D3 theta=0.5 | 8.1% | 85.8% | 12/12 |
| W1 | D3 theta=0.75 | 9.1% | 107.1% | 12/12 |
| W2 | D0 plug-in | 0.0% | 0.0% | 12/12 |
| W2 | D1 mixture | 3.0% | 7.3% | 12/12 |
| W2 | D3 theta=0.5 | 2.4% | 7.2% | 12/12 |
| W2 | D3 theta=0.75 | 3.0% | 9.8% | 12/12 |
| W3 | D0 plug-in | 0.0% | 0.0% | 12/12 |
| W3 | D1 mixture | 1.9% | 14.3% | 12/12 |
| W3 | D3 theta=0.5 | 2.0% | 13.3% | 12/12 |
| W3 | D3 theta=0.75 | 2.3% | 16.1% | 12/12 |
| W4 | D0 plug-in | 0.0% | 0.0% | 12/12 |
| W4 | D1 mixture | 4.0% | 56.9% | 12/12 |
| W4 | D3 theta=0.5 | 3.9% | 40.3% | 12/12 |
| W4 | D3 theta=0.75 | 4.7% | 50.9% | 12/12 |
| W5 | D0 plug-in | 0.0% | 0.0% | 12/12 |
| W5 | D1 mixture | 0.9% | 3.9% | 12/12 |
| W5 | D3 theta=0.5 | 0.8% | 2.8% | 12/12 |
| W5 | D3 theta=0.75 | 0.7% | 3.5% | 12/12 |

This deliberately small paired diagnostic is a precision warning, not a claim that B=80 is adequate for production. Rule differences comparable to this variation should not drive a decision. Data samples are held fixed across B; random streams after the first model are not fully nested.

### Targeted B=960 refinement

W1 and W4 were selected after inspecting the B=80/B=240 diagnostic. The same first 12 training samples/world were refitted with B=960. This post-analysis diagnostic supplements the main experiment; it does not replace selected rows or establish an unbiased new rule ranking.

| World | Rule | Median change B=80 to B=960 | Median change B=240 to B=960 | Maximum B=240 to B=960 | Finite triples |
|---|---|---:|---:|---:|---:|
| W1 | D0 plug-in | 0.0% | 0.0% | 0.0% | 12/12 |
| W1 | D1 mixture | 2.9% | 4.6% | 114.4% | 12/12 |
| W1 | D3 theta=0.5 | 3.7% | 3.8% | 82.5% | 12/12 |
| W1 | D3 theta=0.75 | 4.4% | 4.5% | 100.7% | 12/12 |
| W4 | D0 plug-in | 0.0% | 0.0% | 0.0% | 12/12 |
| W4 | D1 mixture | 2.1% | 2.2% | 11.8% | 12/12 |
| W4 | D3 theta=0.5 | 3.4% | 1.4% | 11.9% | 12/12 |
| W4 | D3 theta=0.75 | 4.7% | 2.0% | 13.1% | 12/12 |

Relative changes condition on finite triples; their counts prevent unresolved outputs from silently disappearing. The B=80 capital-rule comparisons remain exploratory. Higher-B stability on a small selected subset does not validate production precision in every world. The finite-treaty analysis uses deterministic fitted distributions and has a separate decision objective.

## Norwegian failure analysis

The following table holds the Spliced5/constant-Poisson model family fixed. Removing the largest claims changes severity fitting only; annual counts remain unchanged to isolate an influence diagnostic. It is not a legitimate alternative dataset or evidence that extreme claims should be discarded.

| Scenario | Median predicted VaR / realised annual loss | Annual PIT range |
|---|---:|---:|
| full_history | 10.45 | 0.0003–0.3561 |
| recent_frequency_three_years | 10.72 | 0.0000–0.2476 |
| recent_severity_three_years | 7.92 | 0.0001–0.3941 |
| remove_largest | 8.84 | 0.0003–0.4144 |
| remove_largest_three | 6.97 | 0.0005–0.5087 |

Recent-window severity and frequency refits are separated. This helps attribute sensitivity, but cannot identify exposure growth, inflation, threshold changes or the true extreme quantile. The raw results also include alternative severity/frequency models and the AIC-selected family in each refit. Four forecast years cannot establish stable predictive calibration.

## Finite-layer actions under a resource constraint

Eight fixed actions include no cover and seven finite per-occurrence layers. A notation such as `100_xs_20` means a 100m limit above a 20m DKK retention; losses above 120m return to the insurer. Premiums use either Spliced10 or Spliced5, with 10%/30%/50% loading; annual funding rates are 3%/6%/12%. Quotes are assumptions held fixed across nine plausible risk views. Resource budgets of 1,250/1,750/2,500m DKK fund net annual VaR plus premium paid upfront.

An action is admissible only when the budget holds in every risk view. Minimax regret is then computed over this common feasible set. The clairvoyant comparator can choose the best feasible action in each risk view, but cannot use an action outside that common set.

| Pricing view | Loading | Funding | Budget | Feasible actions | Conditional minimax-regret action | Worst regret |
|---|---:|---:|---:|---:|---|---:|
| Spliced10 | 10% | 6% | 1250 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced10 | 10% | 6% | 1750 | 1 | 500_xs_20 | 0.00 |
| Spliced10 | 10% | 6% | 2500 | 8 | 500_xs_20 | 0.00 |
| Spliced10 | 30% | 6% | 1250 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced10 | 30% | 6% | 1750 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced10 | 30% | 6% | 2500 | 8 | 500_xs_20 | 7.24 |
| Spliced10 | 50% | 6% | 1250 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced10 | 50% | 6% | 1750 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced10 | 50% | 6% | 2500 | 8 | 250_xs_50 | 10.93 |
| Spliced5 | 10% | 6% | 1250 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced5 | 10% | 6% | 1750 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced5 | 10% | 6% | 2500 | 8 | 500_xs_20 | 10.64 |
| Spliced5 | 30% | 6% | 1250 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced5 | 30% | 6% | 1750 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced5 | 30% | 6% | 2500 | 8 | 250_xs_50 | 14.75 |
| Spliced5 | 50% | 6% | 1250 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced5 | 50% | 6% | 1750 | 0 | NO_FEASIBLE_ACTION | nan |
| Spliced5 | 50% | 6% | 2500 | 8 | none | 6.87 |

**Decision-change boundaries at 6% funding cost.** Negative feasibility boundaries mean even a zero-loading quotation would exceed the budget in at least one risk view. The cost boundary compares a layer with no cover in every risk view; it does not establish optimality against all layers.

| Pricing view | Layer | Resource budget | Maximum loading feasible in all views | Maximum loading no more costly than no cover in all views |
|---|---|---:|---:|---:|
| Spliced10 | 250_xs_50 | 1750 | -898.2% | 47.7% |
| Spliced10 | 250_xs_50 | 2500 | 1687.8% | 47.7% |
| Spliced10 | 500_xs_20 | 1750 | 17.5% | 27.2% |
| Spliced10 | 500_xs_20 | 2500 | 1001.7% | 27.2% |
| Spliced5 | 250_xs_50 | 1750 | -622.7% | -3.3% |
| Spliced5 | 250_xs_50 | 2500 | 1070.7% | -3.3% |
| Spliced5 | 500_xs_20 | 1750 | -8.2% | -0.5% |
| Spliced5 | 500_xs_20 | 2500 | 761.2% | -0.5% |

A conditional action is a quotation-screening result, not an instruction to buy insurance. Treaty exclusions, aggregate limits, reinstatements, counterparty default, claims development and recovery delays remain outside this calculation. A finite layer can have a finite price even when gross mean loss is infinite; it does not make the retained mean finite in that case.

## What the evidence supports

- Compare capital rules jointly on estimation shortfalls, decision costs and numerical resolution.
- Report calibration failures and inner-refit sensitivity alongside point estimates.
- Use finite-layer and budget sensitivity to structure requests for actual quotations.
- Retain regime-break stress as an explicit limitation of candidate-model robustness.

It does not establish regulatory compliance, a calibrated actual-insurer capital policy, a universal winning rule, or independent institutional validation.

![Current coverage](figures/F6_current_coverage.png)

![Current decisions](figures/F7_current_decisions.png)

![Norwegian diagnostics](figures/F8_norwegian_diagnostics.png)
