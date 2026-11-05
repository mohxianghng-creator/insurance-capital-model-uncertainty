# Model card

**Purpose.** Study which annual large-loss resource levels and reinsurance
actions remain defensible when plausible severity, frequency and parameter
treatments disagree. The framework supports research comparisons of capital and reinsurance decisions; institution-specific use requires additional portfolio and contract data.

**Unit of analysis.** A stylised one-peril large-loss portfolio. The Danish
annual aggregate is in millions of DKK; Norwegian severities are in units of
the nominal 500k NOK reporting threshold. There are no exposures, policy limits,
development triangles or actual treaty quotations.

**Outputs.** Annual VaR99.5 resource level, separately defined economic capital
above expected retained loss, parameter intervals, model-conditional breach
probabilities, reverse-stress boundaries and conditional treaty action costs.
These outputs do not implement an insurer's complete balance sheet or SCR.

**Methods.** Truncated lognormal, Lomax and fixed-threshold lognormal/GPD splices;
Poisson, moment-fitted negative binomial and Poisson trend counts; tilted FFT
aggregation; bootstrap and semi-Bayesian predictive mixtures; finite-set
Hurwicz capital rules and constrained finite-action minimax regret.

**Decision boundary.** AIC screening and weights are judgement tools. The
cost ratio is assumed. The action set is finite. A conditional minimum-regret
action is eligible for further quotation analysis, not a recommendation to bind
cover. No cover is a genuine action; no feasible action is a genuine outcome.

**Failure conditions.** Unresolved numerical capital, failed fit thresholds,
non-finite tail expectations, material grid sensitivity, insufficient data on a
splice side, or insufficient capital for every candidate action. Failures must
remain visible and must not be replaced with an attractive finite estimate.

**Known weak evidence.** Four to six annual out-of-sample observations cannot
validate a 0.5% annual tail. Same-peril replication does not establish broad
insurance or financial-risk transferability. Current simulation coverage is
world-specific; any calibration defect must remain in the executive narrative.

**Requirements for institutional adaptation.** Obtain exposure and portfolio
definitions; align inflation and reporting thresholds; model claims development
and event dependence; encode actual policy/treaty terms and recovery timing;
obtain quotes and a funded risk appetite; agree loss tolerances and validation
acceptance criteria with a model owner. Independent challenge and sign-off
would be separate activities, not something conferred by this repository.

**Revalidation triggers.** Material changes in portfolio exposure, inflation or
threshold basis, concentration, claim dependence, tail diagnostics, treaty
recoverability, numerical methods, candidate models or decision costs. This
project does not calibrate operational monitoring thresholds or prescribe who
may approve a real institution's capital.
