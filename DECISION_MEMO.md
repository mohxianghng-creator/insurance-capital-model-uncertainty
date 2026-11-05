# Decision memo: capital uncertainty and finite reinsurance quotations

**Status:** illustrative case study; no actual insurer, quotation or authority to bind cover.

**Decision to support:** identify which finite per-occurrence covers merit actual
quotations, given a funded resource budget and disagreement about the loss tail.

The Danish baseline annual resource level is 1,298m DKK. The 15 fitted
severity/frequency combinations span roughly 1,136–2,304m DKK. These resource
levels describe a stylised large-loss portfolio; they are not a complete
regulatory capital calculation. Public data and a short annual history do not
identify the true extreme quantile.

## Conditional action

Obtain terms for **500m xs 20m** and **250m xs 50m** as candidate layers. Retain
no cover in the comparison. The action analysis holds each assumed quotation
fixed across nine plausible risk views and first requires net annual VaR plus
the upfront premium to fit available resources in every view.

At **2,500m DKK resources and 6% annual funding cost**, the conditional
minimax-regret choice changes with the pricing assumption:

| Pricing view | Loading | Conditional action |
|---|---:|---|
| Spliced10 | 30% | 500m xs 20m |
| Spliced10 | 50% | 250m xs 50m |
| Spliced5 | 50% | No cover |

At **1,750m DKK resources**, the candidate set is much more constrained. Under
Spliced10 pricing, the 500m xs 20m layer is affordable across all risk views only
up to about **17.5% loading**. Under Spliced5 pricing, even zero loading exceeds
the budget. At **1,250m DKK**, none of the tested actions is feasible under the
tested loaded quotations.

These are conditional screening results, not purchase recommendations. A finite
layer returns losses above exhaustion to the insurer; increasing a retention
cover's limit can therefore matter more than comparisons based on unlimited
cover suggest.

## Evidence that would change the action

- **Quoted price:** under Spliced10 and 6% funding, the 500m xs 20m layer costs
  no more than no cover in all nine risk views up to about **27.2% loading**.
  This exceeds its **17.5% affordability boundary** at the 1,750m budget. A
  quotation can improve the assumed annual cost and still be unaffordable.
- **Available resources:** re-evaluate the common feasible action set. Do not
  choose a lower-capital model solely to make an action fit the budget.
- **Terms and recoverability:** exclusions, aggregate caps, reinstatements,
  collateral, default and recovery timing can alter both affordability and
  economic value. The present price assumes full recoverability.
- **Portfolio evidence:** exposure, concentration, claims development, consistent
  price basis and reporting thresholds can change the relevant loss models.

If no action is feasible, the supported outcome is to revisit resources, cover
terms or the risk appetite. The model should retain that infeasibility visibly.

## Validation before institutional use

Review the current interval coverage, B=80/B=240 precision diagnostic and
targeted B=960 follow-up in `paper/current_validation.md`. Capital-rule rankings
remain exploratory when inner-refit sensitivity is material. The finite-layer
choices above use deterministic fitted distributions and a separate objective.
Numerical convergence does not establish
statistical calibration. Claim dependence and frequency misspecification remain
outside the synthetic decision study, and the Norwegian replication shows that
baseline conservatism can change direction.

For a real institution, require actual portfolio inputs, quotations, a funded
decision objective and independent model challenge. No such approval is
established by this repository.

**Traceability:** `results/current/finite_treaty_costs.csv`,
`finite_treaty_decisions.csv` and `finite_treaty_boundaries.csv` contain the
underlying costs, constrained choices and exact conditional thresholds.
