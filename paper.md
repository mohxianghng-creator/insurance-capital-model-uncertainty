# Capital Management Under Model Uncertainty and Reverse Stress Testing: A Decision-Focused Study on Danish Fire Large-Loss Data

**Working paper. The analysis concerns a stylised large-loss portfolio and has not undergone independent model approval.**
Version 0.5 empirical outputs are regenerated from the current fitting engine. Fresh seeded decision, coverage and inner-refit experiments are in `results/current/`; their generated technical report is `paper/current_validation.md`. Historical simulation and coverage outputs are retained as explicitly labelled version-comparison evidence. Figures are in `paper/figures/`.

---

## Abstract

We study defensible capital and reinsurance decisions when plausible risk models disagree. The setting is a stylised large-fire-loss portfolio, with annual VaR at 99.5% defined as a total resource level and capital above expected loss reported separately. Five severity and three frequency models on Danish claims give resource levels of roughly 1.14–2.30 billion DKK; a Norwegian replication reverses the relative conservatism of the selected baseline. Fresh synthetic experiments use 150 samples in each of five known worlds at 11- and 40-year horizons, B=80 bootstrap refits, and a separate 200-sample coverage study in three tail worlds with B=200. The experiments compare estimation shortfalls, asymmetric decision costs and parameter-interval coverage; pointwise Monte Carlo uncertainty and inner-refit sensitivity remain visible. Nominal-90% bootstrap coverage is 83.5%–89.5%; semi-Bayesian coverage is 90.0%–92.5%, with up to 66.0% of upper endpoints beyond the base numerical grid. Inner-refit sensitivity makes B=80 rule comparisons exploratory. Unresolved predictive quantiles trigger expanded grids and step refinement, with any remaining censored costs retained as bounds. Norwegian claim-influence and recent-window diagnostics investigate forecast over-prediction without treating valid extremes as data errors. A finite-layer reinsurance extension uses fixed hypothetical quotes and a common feasible action set under resource budgets; the conditional decision can be no cover or no feasible action. Neither numerical stability nor four to six annual backtest observations establishes extreme-tail calibration. Capital-rule and action preferences remain conditional on the candidate set, synthetic worlds, prices and decision costs. The contribution is reproducible implementation, explicit failure analysis and transparent decision comparison; no methodological novelty or institutional model approval is claimed.

## 1. Research question, hypotheses, contribution claims

**RQ.** How should an institution choose capital buffers and management actions when plausible risk models disagree, and can a transparent model-uncertainty framework reduce the risk of capital decisions that fail under adverse conditions?

| # | Hypothesis | Type | Verdict (this study) |
|---|---|---|---|
| H1 | Data/parameter uncertainty is at least as large as between-model uncertainty for the capital figure | empirical | Supported on one dataset (≈76% vs 24% of log-variance); depends on the chosen model set |
| H2 | Single-model plug-in capital understates the nominal confidence level when the tail is mis-specified or unseen | simulation | Supported (median true breach prob. 1.8% in W4 at T = 11, 1.3% at T = 40, vs 0.5% nominal); but plug-in is also short ≈ 50% of the time when correctly specified |
| H3 | A model-uncertainty rule lowers estimation shortfalls without higher decision cost | simulation | No general support: cost-ratio dependent and inner-refit sensitive; historical censored comparisons have unidentified upper losses |
| H4 | Historical backtests cannot validate 99.5% capital with ≈ 10 years of data | analytic + empirical | Supported (power table, §6.4) |

**Contribution claims (deliberately modest).** (i) A reproducible, tested engine that exposes how much capital and action rankings move under model, parameter and frequency choices; (ii) a decision rule whose closed form is a classical Hurwicz/minimax-regret result applied to a plausible-model set (Prop. 1; the mathematics is textbook, not new); (iii) a quantified negative result: where robust rules lose to a well-calibrated conventional model. **We do not claim methodological novelty.** The closest literature is listed in §2.

## 2. Literature and scope of contribution

This is an applied case study, not a new capital theory. The following primary sources were checked; the review is selective, not systematic.

- McNeil (1997), *Estimating the tails of loss severity distributions using extreme value theory*, ASTIN Bulletin 27(1), 117–137. The original paper studies a related Danish data version (2,156 losses above one million), illustrates GPD threshold sensitivity and discusses uncertainty. Our evir file has 2,167 observations; its checksum defines the data used here. [Publisher full text](https://doi.org/10.2143/AST.27.1.563210).
- Kerkhof, Melenberg & Schumacher (2010), *Model risk and capital reserves*, Journal of Banking & Finance 34(1), 267–279. Closely related prior work separates estimation and misspecification risk in capital reserves. [University research record](https://research.tilburguniversity.edu/en/publications/model-risk-and-capital-reserves/).
- Cont, Deguest & Scandolo (2010), *Robustness and sensitivity analysis of risk measurement procedures*, Quantitative Finance 10(6), 593–606. Estimation procedure and risk measure jointly determine sensitivity. [Author full text](https://rama.cont.perso.math.cnrs.fr/pdf/RobustRisk.pdf).
- Pesenti, Millossovich & Tsanakas (2019), *Reverse sensitivity testing: What does it take to break the model?*, EJOR 274(2), 654–670. Their method finds a KL-minimal change of measure under output-risk constraints. Our one/two-parameter break-even stress search is simpler and does not implement that method. [Publisher record](https://doi.org/10.1016/j.ejor.2018.10.003).
- Hoeting et al. (1999), *Bayesian model averaging: a tutorial*, Statistical Science 14(4), 382–417 including discussion; corrected author copy. This is background on Bayesian averaging. Our Akaike weights are **not** posterior model probabilities and D1 is a predictive mixture, not an average of VaRs. [Corrected author copy](https://sites.stat.washington.edu/www/research/online/hoeting1999.pdf).

The contribution is implementation, transparent decision comparisons and documented failure cases on two non-life datasets. The finite-set minimax-regret formula below is proved directly. No claim of a literature gap or new optimisation method is required for the case study.

## 3. Setting and definitions

* **Institution (stylised).** Non-life insurer with an annual retained aggregate loss `S = Σ_{i≤N} X_i`. No real institution's capital is reproduced; no regulatory formula (e.g. Solvency II standard formula) is implemented.
* **Capital requirement V.** `V = VaR_{0.995}(S)`, the resource level exhausted with probability 0.5% (a Solvency-II-*style* level, used only as a convention). **Economic capital** `K = V − E[S]`. **TVaR₀.₉₉(S)** is reported but is a different quantity (Spliced10: V = 1,298 vs TVaR lower approximation ≈ 1,535). Liquidity needs are out of scope.
* **Four kinds of uncertainty.** *Parameter/data*: bootstrap variability of one model's capital. *Model*: spread across severity × frequency structures. *Data quality*: left-truncation at 1m, undocumented price basis, 11 years, no exposure (documented, not modelled). *Scenario*: stresses outside history (§5.4).

## 4. Data (verified inventory)

| Item | Value |
|---|---|
| Dataset | `danish` from R package **evir** 1.7-4 (CRAN), fire insurance claims supplied by Copenhagen Re |
| Retrieved from | `https://raw.githubusercontent.com/cran/evir/master/data/danish.rda` on 2026-10-17; MD5 `9defcdbc269e50aa95a1a9e7d36d1c68` (enforced in code) |
| Licence | Package GPL (≥ 2) |
| Observations | 2,167 claims ≥ 1.0 (million DKK), 1980-01-03 to 1990-12-31, daily timestamps |
| Counts | 109 claims > 10; 36 > 20; 7 > 50; 3 > 100; max 263.25 |
| Limits | left-truncated at 1; **price basis undocumented in the package page**; no exposure; industry-level; one peril |

Data-quality findings (`results/E0_diagnostics.json`): annual counts range 153–238 (dispersion index 4.9; χ² = 49.3, p = 3.6e-7 against Poisson); log-severity falls with time overall (Spearman ρ = −0.128, p ≈ 2e-9) but not among claims ≥ 10 (ρ = 0.04, p = 0.68), i.e. the body drifts, the tail does not detectably; lag-1 autocorrelation of log-severity 0.026. Hill estimates of ξ vary 0.54–0.72 with k, illustrating threshold sensitivity. Five exact (date, loss) duplicate rows were retained (no evidence they are errors).
**No data were fabricated or imputed.** The only synthetic data are in §6.5 and are labelled as such.

## 5. Methodology

### 5.1 Severity models (small, justified set)
LN (left-truncated lognormal, 2 parameters); Lomax (GPD on excesses over 1; 2); Spliced_u (truncated-LN body on [1,u) + GPD tail above u, 5 parameters, u ∈ {5, 10, 20} fixed a priori; u is not penalised in AIC, a limitation). GPD shape is constrained to ξ ∈ [1e-6, 5]. MLE; GPD via 1-D profile likelihood.

### 5.2 Frequency models
Poisson constant (λ = 197), negative binomial constant (r = 50, mean 197), Poisson log-linear trend (λ₁₉₉₁ = 246). Count-model scores: 130.0 / 109.9 / 100.3. The NB dispersion is fitted by moments, so its value is an AIC-style diagnostic rather than maximised-likelihood AIC. The trend is *not* separable from portfolio growth (no exposure data).

### 5.3 Aggregation
Compound Poisson/NB aggregate by FFT on a 0.5 grid (cap per claim 16,384). Severity mass above the grid is carried as overflow. Exponential tilting (16) suppresses aggregate circular aliasing; it does not remove grid or tail-truncation error. V is reported as +∞ when unresolved below the numerical ceiling; this denotes right-censoring, not an infinite true quantile. Checks include independent Panjer recursion, Monte Carlo, an aliasing counterexample and real-data grid sensitivity (§6.8). Expected losses and reinsurance stop-loss recoveries now use the full supported severity distribution, including infinite means. Reported finite TVaR values remain numerical lower approximations and are flagged unreliable when missing tail moments cannot be bounded.

### 5.4 Uncertainty and decision rules
* **Bootstrap.** Nonparametric resampling of claims (iid) plus parametric bootstrap of annual counts; predictive distribution = equal-weight mixture of refitted aggregates. Parameter intervals use the inverse empirical CDF, keeping unresolved +∞ draws. Fewer than 90% successful refits cause a hard failure. The within/between log-V variance comparison is a model-set-dependent descriptive diagnostic, not an exhaustive or causal attribution of uncertainty; its within component conditions on finite draws. Sensitivity restricted to plausible models is also saved.
* **Plausible set.** Models with ΔAIC ≤ 10 (a chosen screening convention, not a calibrated confidence set). On the real data: Lomax, Spliced5, Spliced10.
* Rules: **D0** AIC-best plug-in (baseline); **D1** Akaike-weighted mixture of bootstrap predictives (pseudo-BMA, not full Bayes); **D2** AIC-best with bootstrap predictive; **D3(θ)** `V = θ·max_m V_m + (1−θ)·min_m V_m` over the plausible set; θ = 1 is the worst case. **Dorc** (simulation only): MLE in the true family — the "well-calibrated conventional model".

**Proposition 1.** Let the loss be `L(V,V_m) = c_u (V_m − V)⁺ + c_o (V − V_m)⁺` and regret equal loss (the best response to known m has zero loss). Over a finite set with extremes V_min, V_max the minimax-regret choice is `V* = θ V_max + (1−θ) V_min`, θ = c_u/(c_u + c_o).
*Proof.* Worst-case regret is `max(c_u (V_max − V)⁺, c_o (V − V_min)⁺)`, because each term is monotone in V_m. Outside [V_min, V_max] it is strictly larger; inside, the first term decreases and the second increases in V, so the minimum is at equality, giving V*. ∎ (verified numerically in tests).

### 5.5 Reverse stress test
Given V₀ (the conventional **total resource level**; stored as `K0` in legacy CSV fields), find the smallest stress such that `P(S > V₀)` reaches 5% or 50%: frequency multiplier φ, severity scale s, GPD shape shift Δξ. Also the illustrative single claim x\* = V₀ − E[S] on top of mean background loss, and its annual occurrence probability under each frequency model. This is not the actual conditional ruin probability: background losses are random and dependence is unmodelled. Plausibility is read against bootstrap spread of ξ̂ and the frequency trend (λ₁₉₉₁ trend / constant = 1.25).



**Lattice precision.** Reverse-stress search uses `VaR_alpha(stressed) >= V0`. On a discrete lattice this approximates, rather than exactly equals, the strict-exceedance target `P(S > V0) = 1-alpha`. Achieved strict probabilities and the adjacent-cell masses are saved in `results/current/stress_target_diagnostics.csv`; verification allows the lattice/root-search tolerance.

## 6. Results

### 6.1 Capital estimates and the two uncertainties (E1; Fig. F1)
Poisson-constant V₉₉.₅: LN 1,136 · Lomax 1,659 · Spliced5 1,942 · **Spliced10 1,298** · Spliced20 1,873. Bootstrap 5–95% for Spliced10 is [888, 3,426] (ratio 3.9) and for Spliced5 [1,073, 5,147]. Variance decomposition of log V: within-model 0.169 vs between-model 0.055 (model share 24%; 23–25% under each frequency model). Frequency choice shifts V by +3% (NB) and +19% (trend) for Spliced10. The ratio max/min over the 15 combinations is 2.03. *Caveat:* the "between" share depends on the models chosen; five models cannot bound the true model uncertainty.

### 6.2 Out-of-sample behaviour (E2; Fig. F3)

Expanding-window, one-year-ahead origins 1984–1989 give 1,334 test claims and six annual losses. No annual 99.5% VaR breach occurred under any model. Realised annual losses were 609–904. Across all five **constant-Poisson severity models**, VaR ranged 728–6,487; for the **Spliced10 baseline alone** it ranged 1,046–1,598. Constant-Poisson annual PITs were 0.643–0.992; trend-model PITs across all severity candidates were 0.319–0.984. These distributional diagnostics do not validate the extreme annual quantile.

At claim level, 99% severity-quantile exceedances numbered 18–22 against 13.3 expected. Kupiec p-values were 0.029 (LN), 0.052 (Lomax), and 0.143–0.223 (spliced). These pooled tests are approximate diagnostics with changing estimated thresholds, not independent proof of calibration. The tail-censored log score (threshold 10) favours Spliced10 (−511.7) over LN (−514.1) by about 2.3 log-units; the full log score favours LN. No paired uncertainty analysis establishes that these score differences identify a superior model.

### 6.3 Management actions (E3)

Actions are no reinsurance (A0), or unlimited per-claim excess-of-loss with retention 50/20/10/5 (A1–A4). Annual cost is `6% × K_net + premium − expected recovery`; 6% is a stylised funding assumption, **not** a statement of a current regulatory cost-of-capital rate. Premium is `(1 + loading) × expected recovery` under a separate pricing model. These are assumed quotations, not observed market prices. Full-tail stop-loss expectations are used; an unlimited layer is not given a finite expected price if its severity mean diverges.

| Pricing severity | Loading | Lowest-cost action across 9 plausible severity/frequency cells |
|---|---|---|
| Spliced10 | 20% | A1_R50: 7/9 cells, A4_R5: 2/9 cells |
| Spliced10 | 50% | A1_R50: 9/9 cells |
| Spliced5 | 20% | A1_R50: 4/9 cells, A4_R5: 3/9 cells, A0_none: 2/9 cells |
| Spliced5 | 50% | A0_none: 5/9 cells, A1_R50: 4/9 cells |

The choice depends on both risk and pricing views. A1 is a candidate for obtaining actual quotations, not a recommendation to bind cover. The exercise compares five fixed retentions; it does not optimise treaty limits, reinstatements, credit risk, liquidity or a capital-availability constraint.

### 6.4 Reverse stress (E4; Fig. F4) and the limits of validation
At V₀ = 1,298 (Spliced10, Poisson): the breach probability implied by other plausible models is 1.1% (Lomax; 94-year return period), 1.6% (Spliced5; 63 years) and 1.2% under Spliced10 with trend (80 years), versus 0.5% (200 years) under the baseline. Bootstrap probability that the baseline model's own re-estimated V exceeds V₀ is 46%. To bring P(S>V₀) to 5% under the baseline model needs a 54% frequency increase, or a 47% severity increase, or a +0.29 shift in ξ (bootstrap sd of ξ̂ ≈ 0.15, so ≈ 1.9 sd). The frequency trend alone already implies a 25% increase. The single exhausting claim is x\* ≈ 634, 2.4× the largest observed claim (263). Its annual probability is 0.5% under Spliced10 but 1–3% under other plausible models.

**Limits of statistical validation.** With 6 backtest years the exact binomial test (nominal size 5%, actual size 2.96%) detects a true breach probability of 5% with power 26.5%, and 1% with power 5.9%; with 11 years the figures are 10.2% and 0.5% (actual size 0.13%). This apparent fall in power is caused by discreteness: the rejection threshold jumps from one breach to two. It is not evidence that fewer years are generally better. For 80% power one needs ≈ 29, 59, 335 and 1,811 years for true breach probabilities of 10%, 5%, 2% and 1% (`E2_years_needed_80pct_power.json`). Observed claims above 100 number three. Hence **backtests cannot validate a 99.5% capital figure at this sample size; the conclusions of §6 are model-conditional, not empirically validated.**

### 6.5 Historical simulation study (SYNTHETIC; Fig. F5; `results/SIM_*sim2*`)

Historical saved tail-break cells span 75–97% estimation shortfalls across the archived rule/horizon results. Current-engine evidence is in §6.10.
Worlds anchored in the Danish fit but fully synthetic: W1 lognormal; W2 spliced ξ = 0.50; W3 ξ = 0.80; W4 *regime break*: ξ = 0.5 up to 100, ξ = 1.0 beyond (outside every fitted model class); W5 ξ = 0.25. Each replication draws T years, applies every rule and scores against a deterministic lattice approximation to the true V (FFT). T = 11: 150 replications per world (shortfall SE ≤ 4 points); T = 40: 30 per world (SE ≤ 9 points). Loss = c_u × relative shortfall + c_o × relative excess, c_o = 1.

**Historical T = 11 results. Estimation-shortfall frequency P(V̂ < V_true) and loss at c_u/c_o = 3. Losses are LOWER BOUNDS wherever a rule is right-censored; this is not an annual breach frequency.**

| World | D0 short | D1 short | D3(0.5) short | D0 loss | D1 loss | D3(0.5) loss | D3(0) loss | Oracle loss | Paired Δ D3(0.5)−D0 (95% CI) |
|---|---|---|---|---|---|---|---|---|---|
| W1 LN | 49% | 17% | 3% | 0.37 | 0.39 | 0.69 | 0.32 | 0.20 | unidentified (censored) |
| W2 ξ=.5 | 59% | 47% | 37% | 0.55 | 0.55 | 0.54 | 0.52 | 0.54 | 0.00 (−0.07, 0.07) |
| W3 ξ=.8 | 51% | 43% | 43% | 1.26 | 1.31 | 1.34 | 1.40 | 1.26 | unidentified (censored) |
| W4 break | 91% | 85% | 80% | 1.46 | 1.37 | 1.35 | 1.42 | n/a | unidentified (censored) |
| W5 ξ=.25 | 41% | 29% | 23% | 0.12 | 0.14 | 0.20 | 0.13 | 0.12 | +0.09 (0.05, 0.14) |

The high-tail W3 world contains unresolved outputs in 8.7% of D0, 16.7% of D1 and 24.7% of D3(0.5) replications. Treating those outputs as exactly 16,384 understates their over-capitalisation cost. In v0.4, the saved outputs are therefore audited as intervals: mean decision loss has a finite lower bound and an unbounded upper endpoint for affected world/rule cells (`SIM_loss_bounds.csv`). Paired confidence intervals are withheld when either rule has censored outputs; an overall T=11 meta-regret ranking is also withheld. Raising the cost ratio cannot identify an unknown upper endpoint.

T=40 has only 30 replications per world (binomial standard errors up to about 9 percentage points). All these results are conditional on the five chosen data-generating processes. A plug-in estimate falling below the true quantile about half the time is plausible under an approximately median-unbiased estimator, but is **not** an arithmetic identity or a theorem for every quantile estimator. Its distance from the truth can shrink with sample size while the sign remains roughly balanced. The separate annual loss breach probability is `1 − F_true(V̂)`.

The study supports a trade-off: adding conservatism can reduce estimation-shortfall frequency, and its value depends on cost asymmetry. It does not establish a generally preferred default rule. Both the hand-built unseen-tail regime and numerical censoring limit stronger claims. A new simulation run must use a fresh filename and version sidecar; it cannot append new-engine outputs to the historical CSVs.

### 6.6 Historical bootstrap/posterior diagnostics (SYNTHETIC; `results/COVERAGE_summary.json`)
150 replications in each of W2 and W5 (T = 11, B = 80, Spliced10 fitted). The nominal-90% bootstrap interval for V contained the true V in **80.7%** (W2) and 89.3% (W5) of cases; the bootstrap-predictive V was below the truth in 41% / 37% of cases versus 54% / 53% for the plug-in, so parameter-uncertainty adjustment helps but is insufficient. When the GPD shape estimate sat on its lower bound (7 of 150 samples in W5) coverage was 43% (n = 7; too few to estimate precisely). **Under-coverage is evidence of interval miscalibration in that experiment; it does not prove that any realised interval is a lower bound on uncertainty.** The historical coverage calculations also dropped unresolved draws. The revised implementation retains them and uses empirical-CDF quantiles; fresh repeated-sampling results are reported in §6.10. The figures in this subsection remain historical.

*Historical repair attempt (semi-Bayesian posterior; `uncertainty.posterior_capital`).* GPD tail (ξ, β): grid posterior with ξ ~ U(0,2) and flat log β; split probability Beta; spliced body from a body-only bootstrap. Same 150 samples per world: nominal-90% interval coverage rises to **86.7% (W2)** and **93.3% (W5)**; the posterior-predictive V falls below the truth in 21% / 17% of samples (bootstrap 41% / 37%, plug-in 54% / 53%). Intervals are wider (median 95/5 ratio 3.5 vs 2.7 in W2). The reported coverage estimates have Monte Carlo uncertainty and do not certify calibration. The prior bound is a judgement. The code also restricts log β to ±3 around the log mean excess, a data-dependent finite support: this is explicitly semi-Bayesian. New support/grid sensitivity is in §6.8.

*Decision value of the posterior rules (R = 60 paired samples per world, T = 11, same data as §6.5; `results/sim3_post_T11.csv`).*

| World | D0 short | D1 boot short | D1 post short | D0 loss r=3 | D1 boot | D1 post | D0 loss r=10 | D1 boot | D1 post |
|---|---|---|---|---|---|---|---|---|---|
| W1 LN | 55% | 18% | 13% | 0.43 | 0.37 | 0.60 | 0.90 | 0.51 | 0.65 |
| W2 ξ=.5 | 65% | 48% | 23% | 0.57 | 0.55 | 0.82 | 1.37 | 1.00 | 0.97 |
| W3 ξ=.8 | 48% | 42% | 28% | 1.21 | 1.29 | 1.55 | 2.72 | 2.41 | 2.25 |
| W4 break | 92% | 88% | 82% | 1.50 | 1.37 | 1.24 | 4.72 | 4.14 | 3.36 |
| W5 ξ=.25 | 33% | 25% | 10% | 0.12 | 0.17 | 0.30 | 0.24 | 0.24 | 0.32 |

The posterior-rule table above describes historical lower-bound losses where outputs hit the cap. D1-post is censored in about 27% of W3 samples, so its unconditional mean cost and paired cost improvement are not identified. No automatic bootstrap/posterior winner is inferred from this table. The explorer lets users compare candidates without an empirical-winner badge.

### 6.7 Second real dataset: Norwegian fire (E6; `results/E6_*`)
**Data.** CASdatasets `norfire` (9,181 fire losses 1972–1992 from an unnamed Norwegian company, nominal NOK thousands, priority 500k NOK; GPL ≥ 2; MD5 `d79a19ad111bf85ae94a3d5ff746eed5`). Window 1985–1992 (5,433 claims; earlier years excluded because annual counts grew sixfold, i.e. portfolio growth); severity in units of the 500k threshold. Annual counts 607–827 (dispersion index 9.9); nominal log-severity drifts upward with year (ρ = 0.038, p = 0.005, inflation); no exposure; 8 annual observations; 18 claims > 100 units.

**Capital (Poisson-constant, units of 500k NOK).** LN 3,644 · Lomax 5,416 · Spliced20 8,254 · Spliced10 10,312 · **Spliced5 21,054 (AIC-best, ΔAIC(Spliced10) = 1.46)**. The ratio max/min across severity models is **5.8** (Danish data: 1.7), and the AIC-best model is the *most conservative* (the opposite of the Danish case, where the AIC-best model was among the lowest). Bootstrap 5–95% intervals: Spliced5 [12,034, 42,553], Spliced10 [5,850, 22,700]; posterior intervals are similar except for Spliced20 (4,784–46,710 versus 4,394–17,822), where the tail index is weakly identified. Both parameter and model uncertainty are large; the plausible set (ΔAIC ≤ 10) contains only Spliced5 and Spliced10.

**Reverse stress at V₀ = 21,054 (Spliced5/Poisson).** Spliced10 implies a 766-year return period for V₀ (breach prob. 0.13%); Spliced20 1,518 years; Lomax about 6,000 years. P(S > V₀) reaches 5% under the baseline only with +275% frequency or +251% severity or Δξ ≈ +0.27 (Spliced5). In this dataset the conventional capital is conservative relative to the alternatives, so the "hidden breach" phenomenon seen in the Danish data does not recur; model disagreement here makes the baseline more conservative than the alternatives; the true capital remains unknown.

**Out-of-sample (origins 1988–1991, 2,585 test claims).** No annual breach under any candidate. For the **AIC-best Spliced5/Poisson baseline**, forecast capital was 22,916–30,924 versus realised loss 2,272–3,446; annual PITs were 0.0003–0.3561. This diagnoses over-prediction of the evaluated annual distributions, without establishing that the unknown 99.5% quantile is overstated. The largest observed claim (about 931 units) occurred in 1988 and is in every training window; largest-claim influence and recent-window frequency/severity sensitivity are investigated in §6.10. These diagnostics do not identify causal effects or resolve inflation and exposure confounding. Claim-level 99% exceedances were 15–21 against 25.9 expected (Kupiec p=0.020–0.322). Tail-censored scores favour Spliced20 (−1,115.3), Spliced5 (−1,116.3) and Spliced10 (−1,116.5) over LN (−1,121.6); score-difference uncertainty was not calibrated.

**Reading.** The qualitative message (capital is highly model-dependent; backtests of 4–6 years cannot validate it) replicates, but the *direction* of baseline conservatism relative to other fitted models does not: external validity is partial and the framework must not be read as "conventional models always understate". Caveats: unknown company, nominal inflation drift not modelled, three of the five severity models are excluded by AIC.

### 6.8 Numerical and prior sensitivity (new empirical checks)

`numerical_convergence.csv` compares all 30 fitted model/dataset/frequency combinations using half the grid step, twice the severity/aggregate range, and the historical untilted FFT. The maximum fitted 99.5% VaR change from halving the step is **0.167%**; expanding the range and adding tilting leave these fitted grid quantiles unchanged. This supports numerical stability of the fitted points. It does not establish stability of every bootstrap/posterior draw, the far-tail first moment or statistical calibration.

`prior_sensitivity.csv` varies the GPD shape upper bound 1/2/4 and doubles the quadrature resolution 110→220, using 200 draws and common random numbers. It covers Spliced5 and Spliced10 on both datasets under constant Poisson frequency. Edge masses and unresolved shares are retained. For example, Danish Spliced10 predictive VaR is 1,770 / 1,862 / 1,845 for shape bounds 1 / 2 / 4. These finite-draw differences combine support effects with Monte Carlo error; common random numbers do not remove that error. This is a finite empirical sensitivity exercise, not an independent coverage study or proof that all reasonable priors give the same decision.

### 6.9 Decision use for a non-life insurer or consulting client

The immediate decision supported here is whether a single-model resource number is sufficient for discussion, and which reinsurance retentions merit market quotations. Danish results justify presenting model and parameter ranges, with the frequency-trend alternative shown separately. Norwegian results show why a model-risk adjustment must not automatically be positive relative to whichever baseline was selected.

Before moving from illustration to an institution-specific decision, the model owner would need exposure/earned premium, consistent inflation and reporting thresholds, current policy and treaty terms, claims development, and actual quotations. An action would then be compared on gross/net loss, capital availability, funding cost and recovery cash flows. A material change in exposure, threshold, inflation assumptions, tail diagnostics or treaty recoverability should trigger revalidation rather than a mechanical reuse of the present margin. The present project does not calibrate numerical monitoring limits from these public data.

### 6.10 Current-engine validation and decision extensions

<!-- CURRENT_STUDY_START -->
The version-0.5 study uses 150 fresh samples per world at each of T=11 and T=40 and B=80. A separate coverage study uses 200 samples in W2/W3/W5 with B=200. All reported results below come from `results/current/`, with source/dependency hashes and independent task seeds. Historical tables above do not serve as current-engine evidence.

**Coverage of nominal-90% parameter intervals.** Wilson intervals quantify finite-replication error.

| World | Method | Coverage | 95% Monte Carlo interval | Infinite upper endpoint |
|---|---|---:|---:|---:|
| W2 | bootstrap | 85.5% | [80.0%, 89.7%] | 2.5% |
| W2 | semi_bayesian | 90.0% | [85.1%, 93.4%] | 9.5% |
| W3 | bootstrap | 83.5% | [77.7%, 88.0%] | 44.0% |
| W3 | semi_bayesian | 90.0% | [85.1%, 93.4%] | 66.0% |
| W5 | bootstrap | 89.5% | [84.5%, 93.0%] | 0.0% |
| W5 | semi_bayesian | 92.5% | [88.0%, 95.4%] | 0.0% |

**Numerical resolution.** Predictive mixtures average conditional spectra before inversion. The compact tilted aggregate domain has circular alias mass bound <1.13e-7 and passes padded-domain comparisons. Unresolved capitals are expanded up to 2,097,152 and accepted only after a halved-step change of at most 0.5%. Across all current rule outputs, 0.0% is the largest world/horizon/rule censored share. Censored outputs retain their actual final ceiling and unbounded upper cost. Finite quantiles do not establish calibration or precise mean decision costs.

**Decision precision.** Same-sample paired costs for ratios 1/3/10 and Wilson intervals for estimation shortfalls are saved in `decision_paired.csv` and `decision_summary.csv`. The first 12 data samples/world are compared at B=80 and B=240. W1/W4 additionally receive a targeted B=960 refit on those fixed samples after the initial diagnostic. These are precision diagnostics, not production convergence certificates; B=80 rankings remain exploratory. The full generated report retains all worlds and adverse findings; no automatic capital-rule winner is inferred.

**Norwegian influence.** Largest-claim and top-three-claim deletions hold counts fixed; recent severity and recent frequency windows are changed separately. Removing extremes changes capital, but does not justify discarding valid claims or establish the true annual tail. Full scenario/model outputs and PIT diagnostics are in `norwegian_diagnostics.csv`.

**Finite-layer decisions.** Eight actions, fixed quotations from two pricing views, three loadings and funding rates, and three resource budgets are compared across nine plausible risk views. Resources fund net VaR plus premium upfront. Feasibility is imposed across all views before minimax regret is computed over the common feasible set. `finite_treaty_boundaries.csv` gives conditional loading boundaries for budget feasibility and comparison with no cover. Actual quotes, terms, recovery delays and counterparty risk remain outside the case study.

See **[the generated current-validation report](current_validation.md)** and **[the offline evidence report](../app/evidence_report.html)** for tables, charts and interpretation.
<!-- CURRENT_STUDY_END -->

## 7. Limitations

1. Both datasets are large fire losses, with no exposure measure or development triangles. They are not complete insurer balance sheets. Dependence across lines, catastrophe accumulation, reserve, market and credit risks are not modelled.
2. Historical frequency growth and severity drift challenge iid/stationarity assumptions. Trend, portfolio growth and reporting/inflation effects are not identified separately.
3. Candidate models, fixed splice thresholds, the ΔAIC cutoff, positive-only GPD shapes and the equal-weight variance diagnostic are judgement choices. Akaike weights are not posterior model probabilities.
4. Current capital-interval coverage is measured in three synthetic tail worlds with Monte Carlo intervals. These experiments do not confer universal calibration; infinite endpoints, interval width and finite inner-refit precision must be considered together. Historical coverage estimates are version-comparison evidence.
5. Prior support and quadrature sensitivity are explored on four real-data fits; the body/frequency treatment is still hybrid and the log-scale prior has data-dependent finite support.
6. Historical simulation fits remain separate from the fresh current-engine study. Current T=11/T=40 experiments have 150 samples/world and a small B=80/B=240 precision diagnostic. Synthetic worlds share Poisson frequency and a Danish anchor. Any remaining censored costs have unbounded upper endpoints; finite heavy-tailed costs can still be imprecisely estimated.
7. Reinsurance prices, funding cost and under/over-capitalisation ratios are assumptions. No optimal real-world treaty or capital policy is claimed. Mean losses and unlimited-layer prices are infinite when the specified severity tail has ξ≥1, even though finite quantiles can exist.
8. Finite TVaR figures are numerical lower approximations unless tail reliability is established. A small overflow probability alone does not control the tail expectation.
9. The one-year 99.5% convention resembles a regulatory calibration level only. Solvency II SCR refers to changes in basic own funds, not simply aggregate claim VaR; no SCR compliance, risk margin or regulatory approval is claimed. [EIOPA, Article 101](https://www.eiopa.europa.eu/rulebook/solvency-ii-single-rulebook/article-2188_en).

## 8. Reproducibility and provenance

Use Python 3.12 and `pip install -r requirements-lock.txt` (or `pip install -e .`). `python scripts/reproduce.py verify` runs the tests, raw-to-result/explorer integrity checks and file inventory. `python scripts/reproduce.py all` regenerates the two real-data experiments, E5 explorer data, fresh current simulations, extension studies, figures and reports before verification. The `empirical` and `research` modes rebuild the respective subsets; source provenance prevents mixing incompatible current-study engines. Both entry points use the active Python interpreter and work on Windows and Unix.

Seeds and bootstrap counts are saved with empirical outputs: E1 B=300, E5 B=200, E6 B=120. Interval endpoints use the inverse empirical CDF and retain unresolved draws. Bootstrap/posterior predictive mixtures accumulate in O(grid-size) memory. The SHA-256 manifest detects file drift; it is not independent model approval. Historical simulation CSV checksums and status are saved separately. For a new long simulation study use fresh `sim_current_*` filenames as in `run_all_sims.sh`; new engine versions cannot resume old files without matching metadata. Fresh repeated-sampling coverage and inner-refit sensitivity are reported in §6.10 and `paper/current_validation.md`. They evaluate the stated experiments rather than certifying institution-specific calibration.

## Appendix: software–claim traceability
Capital figures → `aggregate.py::AggDist.var`; bootstrap → `uncertainty.py::bootstrap_capital`; rules → `simulation.py::decide`, `decision.py`; stress → `stress.py`; power → `validation.py::breach_test_power`. A bug in the GPD boundary fit (β exploding when ξ̂ hit its lower bound) was found during simulation review; it inflated bootstrap capital by an order of magnitude and invalidated the first simulation run. It is fixed and covered by a regression test, and the historical results were regenerated at that stage. The v0.4 empirical rerun is distinguished from those historical simulations as described above.
