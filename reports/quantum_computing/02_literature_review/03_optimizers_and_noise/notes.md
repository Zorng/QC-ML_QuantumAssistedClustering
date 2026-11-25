# Research Notes: QAOA Optimizers and Noise

## Reading Scope and Source Identity

Three primary publications support the optimizer review: Farhi et al. for QAOA definitions, Spall for SPSA, and Pellow-Jarman et al. for an empirical QAOA optimizer/noise comparison. The last two are the core optimizer sources. Cai et al.'s survey is an additional review source for error mitigation, not a fourth primary experimental study. SciPy, PennyLane, and Mitiq supply supplementary method/API information. COBYLA and first-order SPSA remain the only optimizer candidates; ZNE is the only proposed mitigation technique.

The supplied `1411.4028v1.pdf` is the original QAOA preprint, not Powell's COBYLA paper. The supplied `s41598-024-66625-6.pdf` is the published optimizer comparison, not a standalone derivation of COBYLA. Its bibliography cites Powell's 1994 work, but that citation is not a claim that Powell's paper was read. The method description here uses the official SciPy explanation instead.

Reading coverage on 2026-11-22:

- **Farhi et al.:** Introduction, PDF pp. 2–3, and the beginning of Section II, p. 4, read for objective direction, angle definitions, sampling, and MaxCut context. The remaining performance proofs are not used as evidence for this review.
- **Spall:** All ten scanned PDF pages inspected. Sections I–II and V–VI read for construction, numerical design, and limits; Sections III–IV reviewed for regularity assumptions and conditional efficiency arguments. Proofs were inspected, not independently rederived.
- **Pellow-Jarman et al.:** Published PDF pp. 1–11 read, including methods, results, figure captions, discussion, and references. Relevant claims were also checked against arXiv v2; the preprint's pagination differs and is not treated as another independent study.
- **Official documentation:** COBYLA method/options and algorithm notes; SPSA API and linked source; finite-shot measurement explanation; noisy-channel examples; mixed-state device readout parameter. These pages establish supported behavior, not project performance.

Additional reading on 2026-11-24:

- **Cai et al., supplied `error-mitigation.pdf`:** Identified as arXiv:2210.00921v3, a 43-page author manuscript of the published review. Read the introduction and targeted Sections II.A–B, pp. 3–4; III.A, pp. 6–7; and IV.A, pp. 21–22, for estimator bias/variance, ZNE assumptions, noise amplification, calibration costs, and comparisons. The remaining mitigation techniques and the full mathematical survey were not comprehensively reviewed.
- **Mitiq ZNE documentation:** Read the ZNE overview, introduction, use-case limitations, options for global folding and linear extrapolation, theory, and the noise-scaling example's inverse-gate warning. These support the proposed software route, not measured improvement or completed library integration.

Additional reading on 2026-11-25: scikit-learn's `silhouette_score` and `adjusted_rand_score` documentation, specifically required inputs, interpretation, Silhouette validity, and ARI invariance under cluster-label permutation. These support the selected evaluation outputs without changing the optimizer candidates or introducing new benchmark results.

Bibliographic identities, exact supporting locations, access outcomes, and local citation numbers are in [ref.md](ref.md).

## Source Comparison

| Source | Method or question | Assumptions | Evidence read | Limitations | Project relevance |
|---|---|---|---|---|---|
| [Farhi et al.](ref.md#farhi2014-qaoa) | Alternating cost/mixer circuit; optimize an expected diagonal cost | Defined cost operator and ansatz; original angle interval uses integer eigenvalues | Introduction, pp. 2–3, Eqs. (1)–(10); MaxCut start, p. 4 | No COBYLA/SPSA or noisy-clustering comparison; ideal optimal-value statements are not finite-budget guarantees | Fix $d=2p$, objective direction, angle ordering, and measurement meaning; do not copy a maximization convention into a minimizer unchanged |
| [Spall](ref.md#spall1992-spsa) | Simultaneous random perturbations versus coordinate finite differences | Smooth objective, valid inverse perturbation moments, suitable gain sequences, controlled noise differences, stable iterates | Section II, pp. 332–333: two-call construction and $2q$ averaging; Sections III–IV, pp. 334–338: conditional theory; Section V, pp. 338–340: noisy statistical example | Not quantum; compares primarily with finite-difference stochastic approximation, not COBYLA; asymptotic reasoning need not predict short runs | Justify SPSA as a contrasting candidate and audit calibration/averaging costs without promising a winner |
| [Pellow-Jarman et al.](ref.md#pellowjarman2024-qaoa-optimizers-noise) | QAOA optimizer/depth comparison across execution conditions | Minimum vertex cover, five qubits, selected software and device-derived noise | Published pp. 4–6: ten optimizers, 21 graphs, ten repeats, 10,000 shots, 5,000-call cap; pp. 5–8: condition-dependent performance and depth behavior | Small problem family; defaults/tuning, restart accounting, statistical uncertainty, and complete measurement ledgers are not fully specified in the prose; no clustering evidence | Motivate separate execution conditions and explicit budgets; do not import their ranking, shot count, repeat count, or preferred depth |
| [SciPy COBYLA](ref.md#scipy-cobyla) | Linear interpolation within a trust region; software controls | Scalar objective, initial point/radius, declared constraints if used | Algorithm Notes and method options; PRIMA implementation-change note | Documentation, not evidence of noise robustness; implementation differs from older studies | Official support and meaning of evaluation cap/tolerances |
| [PennyLane SPSA](ref.md#pennylane-spsa-optimizer) | Concrete first-order update and gain schedules | Scalar objective and declared schedule/randomness | API plus `__init__`, `compute_grad`, `step`, `step_and_cost` source | Defaults are not problem-specific tuning; software versions may change | Check extra monitoring calls and absence of objective-based initialization calibration in the inspected implementation |
| [PennyLane measurements](ref.md#pennylane-measurements) | Finite-shot versus exact simulator expectations | Supported measurement/device combination | “Changing the number of shots” | Documentation; no proposed shot budget is experimentally validated | Keep sampling precision separate from gate/readout errors |
| [Arrazola tutorial](ref.md#arrazola2021-noisy-circuits) | Mixed states and error channels | Chosen channel convention and placement | “Noisy operations,” bit-flip and depolarizing examples | Examples do not calibrate this project's circuit or hardware | Specify a small synthetic model and disclose omitted mechanisms |
| [DefaultMixed](ref.md#pennylane-default-mixed) | Readout-error software setting | Supported mixed-state device and measurement | Class Parameters: `readout_prob` | A configurable probability is not a measured hardware error rate | Official support for a separately identified readout condition |
| [Cai et al.](ref.md#cai2023-quantum-error-mitigation) | Survey of error-mitigated estimators and ZNE | Chosen observable, valid noise amplification, appropriate extrapolation response, finite sampling | Sections II.A–B: bias/variance and MSE; III.A: extrapolation and gate-insertion limits; IV.A: calibration and use-case-specific comparisons | Secondary synthesis, not new QAOA-clustering evidence; no guarantee that a selected fit reduces error | Motivate one carefully budgeted ZNE diagnostic and distinguish estimator improvement from corrected clustering outputs |
| [Mitiq ZNE guide](ref.md#mitiq-zne-guide) | Digital folding and selectable extrapolation | Supported circuit/executor interface, preserved ideal action, appropriate noise scaling | `fold_global`, `LinearFactory`, theory, use-case caveats, and inverse-gate warning | Documentation examples are not project benchmarks; gate/inverse errors can differ | Official support for the proposed two-point configuration and implementation checks |
| [Silhouette API](ref.md#scikit-learn-silhouette-score) | Mean internal clustering coefficient | Declared feature/distance representation and a valid partition | Definition and Parameters: evaluation features/distances, predicted labels, and $2\leq k\leq n-1$ | Documentation supplements the method literature; geometric quality is not diagnosis agreement | Support the headline internal score on shared scaled original features |
| [ARI API](ref.md#scikit-learn-adjusted-rand-score) | Chance-adjusted partition agreement | Reference and predicted labels on the same observation set | Definition, Parameters, and permutation examples | A reference-agreement score is not classification accuracy or a task-feasibility check | Use available diagnosis labels only for external evaluation |

## Evidence and Interpretation Checks

### SPSA Is a Gradient Estimate, Not an Error-Correction Method

Use $d$ for parameter dimension in this report; Spall uses $p$, which would otherwise conflict with QAOA depth. Basic SPSA uses two scalar objective estimates; $q$ independent perturbation pairs cost $2q$. Central coordinate differences use $2d$. These counts exclude evaluation of the unperturbed point, calibration, line searches, and restarts. Two objective estimates are not two shots.

Spall's Section II assumes a conditionally zero-mean difference between paired measurement errors. Section III adds smoothness, moment, step-sequence, boundedness, and attraction conditions. They are conditions on a stochastic approximation problem, not a certificate for a finite QAOA run. Gate/readout noise can change the expected objective relative to the ideal target. An optimizer may then optimize the noisy objective without recovering the ideal optimum.

Section V compares SPSA and finite differences at matched measurement counts, including a 20-dimensional statistical example and different gain schedules. It discusses mixed small-sample behavior and cautions about using asymptotic results in practical problems. The paper also reports reduced relative advantage in a smaller-dimensional ancillary case. We therefore do not transfer a high-dimensional efficiency factor to a $p=1$ QAOA circuit with only two angles.

### QAOA Comparison: What Transfers and What Does Not

The published comparison separates statevector, ideal sampled, and device-noisy sampled simulations. Its Results report greater optimizer differentiation with noise. The abstract and Results emphasize different shot-noise leaders, so a single “best optimizer” sentence would hide a qualification in the source itself. Record that discrepancy rather than resolving it by assumption. The synthesis reports the stated set of 21 graphs without assuming exhaustive coverage of graph families.

The paper describes parameter-shift gradients for its listed gradient-based methods. Its later explanation discusses evaluation expense when contrasting these methods with SPSA. Our review does not claim those experiments used numerical finite differences. The numerical-gradient cost comparison comes from the mathematical construction, not an invented description of that implementation. Its table places SPSA among gradient-free methods; this review explicitly calls it a stochastic gradient estimate to distinguish absence of a supplied exact gradient from absence of any gradient approximation.

The six-layer optimum discussed in the depth study belongs to selected five-qubit minimum-vertex-cover cases and a specific noise model. It is not a project depth recommendation. The paper uses an IBM Belem-derived simulation model; reading that published evidence does not require adopting an IBM service or citing inaccessible IBM documentation. No hardware experiment is performed here.

The paper's approximation-ratio display is unclear in the published PDF. Use a separately verified project objective convention rather than copying that expression. The original QAOA paper maximizes a satisfaction/cut objective, whereas the empirical study concerns a minimization problem. This is a concrete reason to agree on sign, constants, normalization, and any feasibility penalties before implementation.

## Proposed Accounting and Controls

| Item | Proposed treatment | Failure prevented |
|---|---|---|
| Derivatives | Neither selected candidate requests an analytic or coordinate numerical gradient; count SPSA perturbation calls | Treating a gradient estimate as free |
| COBYLA setup | Charge interpolation-model/startup and subsequent objective calls | Confusing interpolation points with a fixed per-update charge |
| SPSA update | Basic $q=1$ initially; record all calls from the selected software path | Reporting only the two-call theoretical kernel |
| Calibration and tuning | Equal declared development allowances; any in-run calibration consumes the run budget | Hiding parameter-search cost |
| Monitoring | Count additional objective measurements and their shots | Free learning-curve/selection measurements |
| Initialization | Share actual starting vectors, angle convention, and circuit depth | Assuming the same seed produces the same initialization |
| Restarts | Treat each as a full attempt; disclose all attempts and selection policy | Comparing best-of-many against one run |
| Termination and output | Common caps; record native returned-vector rule, status, and actual usage | Equating iterations or claiming every method consumed its cap |
| Final scoring | Equal reserved allocation and fresh shot seeds, unavailable to tuning | Selecting and evaluating with the same favorable shot batch |

For the inspected PennyLane path, `step` uses the perturbation evaluations; `step_and_cost` adds a forward evaluation at the pre-update point. The source initializes gain constants algebraically rather than measuring a calibration gradient. This observation applies to that implementation/version, not all SPSA libraries. Its generic documentation mentions Hamiltonian-term execution costs; for a diagonal QUBO, the measurement plan may share computational-basis samples, so actual grouping/executions must be recorded.

Budget terminology in `content.md` counts training objective calls, executed measurement settings, and shots separately. Training includes startup, in-run calibration, monitoring, and restarts. Final evaluation and fixed-parameter diagnostics have separate reserved ledgers. Development tuning costs are disclosed separately from held-out evaluation. A finite-shot run with one fixed basis and $S$ shots per call has training usage $N_{\mathrm{train}}S$; this identity does not apply unchanged if measurement groups or shot allocations vary.

As a constructed accounting illustration, ten basic SPSA updates require 20 perturbation calls. If every update also requests one additional forward cost, the count becomes 30. With one measurement setting and 100 shots per call, these are 2,000 versus 3,000 shots, before any final evaluation. These arbitrary numbers illustrate accounting; they are neither proposed budgets nor project results.

## Proposed Noise Design and Feasibility Limits

The main proposal has ideal finite-shot optimization, frozen-parameter ideal/noisy evaluation, and reoptimization under the same noise profile. One synthetic profile includes independently applied local depolarizing channels after compiled gates and symmetric independent readout flips. A local channel on each wire after an entangling gate is explicitly not a correlated two-qubit depolarizing model. The Pauli-error probability convention in the content matches the tutorial's Kraus operators; it must not be silently substituted for a different depolarizing parameterization or hardware gate infidelity.

Choose the gate basis, mapping/routing, and compilation settings before attaching errors. Record every targeted gate family, arity, wire set, channel, and parameter. Check that no error is attached to a gate name absent from the compiled circuit, omitted from a relevant gate family, or double-counted before and after decomposition. The initial zero state is ideal; compiled unitary state-preparation gates are noisy under the same per-gate rule. Idle errors, correlated errors, drift, and leakage are outside this initial proposal. ZNE is the separately budgeted mitigation diagnostic described below.

Fixing parameters isolates circuit sensitivity from reoptimization. It does not remove finite-shot variation, which requires fresh batches and recorded seeds. Combined gate/readout noise alone cannot identify which component caused a change; component-only diagnostics are optional and separately budgeted.

The initial campaign fixes a shallow depth, training shot allocation, and one nonzero noise profile. A later feasibility check determines $B$, the shot cap, evaluation-observation-set count $I$, and repetition count $R$ before reserved evaluation. The two methods and two optimization conditions imply $4IR$ optimization runs, plus separate diagnostic evaluations. Different subsets of the breast cancer dataset are not additional independent datasets. Resampling-stability fits are deferred; there is no nested restart grid or depth/noise/hyperparameter sweep. Fixed-observation runs remain necessary to describe stochastic variation when affordable. If only very few repetitions fit, retain individual values and limited-evidence language. A deterministic optimizer can still vary because its sampled objective varies.

## ZNE: Evidence, Proposed Choices, and Limits

Cai et al. frame error mitigation around estimating an ideal expectation value. Sections II.A–B explain that reduced bias often costs increased variance at fixed shots, and give the decomposition $\mathrm{MSE}=\mathrm{Bias}^2+\mathrm{Var}$. Section III.A describes extrapolation, dependence on the fitted response, and the need to increase noise without changing its character. Its gate-insertion discussion explains why that assumption can fail for more general errors. Section IV.A separates calibration from response measurements and cautions against generalizing a method's performance from one use case. These are reviewed literature findings, not this project's measurements.

Our relative folding scale $\lambda$ is dimensionless; Cai et al. use the same symbol for an absolute circuit fault rate. The symbols should not be equated. Also, the two-point linear configuration is a project choice supported by documented options, not a teacher-imposed setting or an experimentally selected optimum.

| Design choice | Initial proposal | Reason or limitation |
|---|---|---|
| Mitigation target | Raw graph/QUBO expectation at fixed returned parameters | A scalar extrapolation is not a corrected probability distribution or partition |
| Place in the workflow | After ideal finite-shot optimization, for a prespecified subset including both optimizers | Adds diagnostics without an extra mitigated optimization campaign |
| Noise condition | Same gate probabilities in both arms, with readout probability $r=0$ | Gate folding does not scale fixed readout error; combined-noise results cannot stand in for a matched gate-only comparison |
| Scaling and fit | Deterministic global folding at nominal factors 1 and 3; two-point linear extrapolation | Small circuit set; weak-noise/smooth-response assumptions still require checking |
| Execution | Fold ideal unitary gates, preserve the folds through native-gate compilation, then attach noise to executed gates | Folding a channel or allowing inverse-pair cancellation invalidates the intended procedure |
| Shots | Equal total $T$ per arm; ZNE splits its allocation equally across two scales | Initial transparent allocation, not a claim of variance-optimal allocation |
| Diagnostic repeats | Fresh independent batches, with count chosen from a separate feasibility budget | Repeated measurement batches are not independent problem instances |
| Reference and outcome | Ideal expectation where feasible; compare errors, variance, MSE, and resources | Extrapolated values can worsen; no automatic clustering-quality improvement |

For $\lambda=1,3$, fitting $E(\lambda)=\alpha+\beta\lambda$ gives $\widehat\alpha=(3\widehat E_1-\widehat E_3)/2$. The weights sum to one and cancel the term linear in $\lambda$; quadratic/higher terms and inaccurate scaling can leave residual bias. Two data points have no degrees of freedom to assess curvature, so a perfect line through them is not a fit-quality certificate. This is an algebraic interpretation of the proposal.

Under independent scale batches, the variance is $9\operatorname{Var}(\widehat E_1)/4+\operatorname{Var}(\widehat E_3)/4$. As a constructed accounting illustration, if each scale has the same per-shot variance $v$ and receives $T/2$ shots, ZNE has variance $5v/T$, versus $v/T$ for a raw estimate using $T$ shots. This conditional calculation illustrates variance amplification; it is not a measured overhead or a required shot count. A different allocation would change the factor.

Both matched sampled arms together require $2T$ shots per frozen vector per diagnostic repetition, before additional validation/calibration. If actual compiled gate counts are $n$ and $3n$, the ZNE arm uses $nT/2+3nT/2=2nT$ gate applications versus $nT$ for the raw arm, even though each arm uses $T$ shots. Actual depth, two-qubit gate applications weighted by shots, and runtime must therefore accompany shot totals. All such runs belong inside the existing diagnostic ledger; do not add a second charge for the same shots or reuse an old baseline without a declared policy.

For this local depolarizing channel model, a nominal factor of three does not by itself prove a uniformly tripled effective noise response. Inverse-gate representation, compilation, and propagation through the circuit matter. Check ideal equivalence and scaling behavior on development circuits; disclose any failed check rather than claiming first-order error cancellation from gate counts alone. No automatic multiplication of channel probabilities accompanies gate folding.

Mitiq's options page supplies the proposed named functions; its examples also include exact density-matrix expectations. A future finite-shot experiment needs an executor that actually returns finite-shot estimates, with the shots recorded. Backend conversion, observable/bit ordering, preservation of terminal measurements, and version compatibility remain implementation checks. No Mitiq dependency is added by this review.

The initial diagnostic does not add mitigation inside COBYLA or SPSA updates, a mitigation-method sweep, readout calibration/inversion, or an end-to-end clustering-benefit claim. If an ideal reference or enough repeats are unavailable, retain individual values and resource reporting without asserting reliable bias reduction. Extrapolated values outside the observable's physical range must remain visible with a flag; clipping would change the estimator and conceal a failure.

## Selected Dataset and Initial Evaluation Scope

The selected project data are a breast cancer dataset with diagnosis labels. The exact variant, source/version, observation identifiers, feature list, missing-data policy, and label coding have not been established in the reviewed project material; do not infer a particular public dataset, sample count, feature count, or class encoding from the name alone.

| Output or analysis | Initial experimental scope | Technical reason |
|---|---|---|
| Silhouette | Main internal quality score, using the same scaled original features and observation IDs for both methods | Describes feature-space geometry without reference labels |
| External ARI | Main reference-agreement score, calculated only after decoding is fixed | Diagnosis labels are available for evaluation; obtaining ARI from existing labels does not require another QAOA optimization |
| Runtime | Main efficiency output, with common setup and optimization components disclosed | End-to-end cost must use a consistent timing boundary |
| DBI | Deferred from initial experiments; retain the broader ML literature coverage | The initial output list is limited to one internal score, one external score, and runtime |
| Resampling stability | Deferred from initial experiments; retain the broader ML literature coverage | Additional subset fits increase workload; deferral is not evidence of robustness |
| Fixed-observation repetitions | Retained, with affordable counts frozen after development | Repetition describes stochastic variation and does not imply a full resampling-stability study |
| Objective, failure, shot, and circuit records | Retained as supporting records | Three headline outputs do not replace fair resource accounting or task-validity checks |
| ZNE objective-error and cost diagnostic | Retained separately at frozen parameters | Scalar extrapolation does not create new cluster labels or directly improve Silhouette/ARI |

Keep the diagnosis column and record identifiers outside the feature matrix. Reference labels must not influence sampling, preprocessing/PCA, graph or optimizer settings, stopping, bitstring decoding, or restart selection. All methods receive the same selected observation IDs. Align each feature row, predicted label, and diagnosis label explicitly. Check the actual selected subset's reference/predicted counts when interpreting ARI; do not assume a small subset represents the whole dataset well. Silhouette is evaluated on shared scaled original features, with its fitting policy reconciled before experiments.

Headline runtime starts with preprocessing the selected feature table and stops when a partition or a failure is returned. Include graph construction, compilation, optimization, final shots, and decoding. Exclude download/loading, metric computation, plotting, and the separate ZNE diagnostic; record relevant extra times separately. When common setup is reused, record its time and include it once in each method's end-to-end total, rather than comparing one method with setup against another without it. Record the machine/backend, threading settings, timing components, and any failure/early-stop status.

This is a single-dataset exploratory comparison. Different observation subsets, starts, and shot batches are repeated or within-dataset evaluations, not independent datasets. Do not infer population-wide or across-dataset superiority. Report all attempted runs and conditional-on-validity score summaries; a numeric ARI does not cancel a task failure.

## Compatibility with Existing Project Material

The project README identifies graph-based QAOA clustering, open optimizer choices, and no implemented dependency stack. No completed algorithm or software selection is assumed. The QAOA topic files and ML evaluation files on `main` are empty at the inspected revision `0167d18`.

The existing ML evaluation draft was read from `machine-learning/3` at `17bb112` without merging or editing it. This is a technical compatibility check against that draft, not final cross-topic agreement. Its relevant proposed controls are common observation IDs, scaled original-space internal metrics, labels reserved for evaluation, independent evaluation instances, separate shot/gate/readout conditions, and budget-dependent repetitions. The current QC proposal preserves shared scoring, label isolation, and resource controls, but narrows the initial experimental outputs to Silhouette, external ARI, and runtime on one labeled breast cancer dataset. DBI and resampling-stability experiments are deferred. The broader ML draft still needs alignment with this narrower experimental scope; its literature coverage is retained. Within-dataset repetitions do not satisfy an independent-dataset comparison by relabeling them as independent instances.

Research files in related topics have not established a final graph loss, balance constraint, compiled gate basis, or noise profile. Those dependencies remain unresolved; no lesson completion or experimental result is inferred from a placeholder folder.

## Unresolved Technical Questions

| Question | Why it matters | Current treatment |
|---|---|---|
| What graph/QUBO task and balance/cluster-count constraints are agreed? | Defines the meaning of quality, penalties, and feasible decoding | Await QAOA/clustering formulation; no alternative task silently substituted |
| What are $H_C$, sign/offset/scale, angle ordering, and initialization domain? | Both methods must optimize the same objective and parameterization | Explicit common interface proposed; weighted costs require checking any assumed periodicity |
| Which backend, library versions, and gate basis will be used? | Determines executable gates, native return rules, and correct noise placement | SciPy/PennyLane are supported candidates, not installed project dependencies |
| What values of $u_1$, $u_2$, and $r$ define the synthetic profile? | Needed to reproduce a noise experiment | Choose on development instances; no hardware calibration inferred |
| Which gates are virtual or eliminated by compilation? | Attaching errors before decomposition or to nonexistent gates changes the model | Freeze and inspect a compiled template; record exclusions and simplification policy |
| What evaluation/shot/campaign caps and repetition counts are affordable? | Full noisy optimization dominates cost; small samples limit conclusions | Later feasibility check, then freeze settings; retain failed/incomplete attempts |
| What is the exact breast cancer dataset and its diagnosis coding? | Features, reference labels, identifiers, and provenance must be reproducible | Record source/version and column roles; do not assume a particular public variant or class encoding |
| How will development and evaluation observations and scaling fits be defined? | One dataset can supply repeated subsets without establishing independent-dataset evidence | Prespecify IDs, overlap, fit population, and seed rules; keep diagnosis labels out of selection |
| How representative are the affordable evaluation subsets? | Tiny subsets can omit a reference group or distort size distributions | Report reference/predicted counts and limited scope; do not treat repeated subsets as independent datasets |
| How is a bitstring converted into a feasible partition, including symmetric labels? | Lowest sampled objective can still yield an invalid task output | Agree on decoding and failure policy with QAOA and ML reviews; external labels remain unavailable to selection |
| Which component-only noise diagnostics are affordable? | Combined noise does not identify a gate-only or readout-only cause | Optional fixed-parameter extensions, not part of a large optimization grid |
| Can the backend preserve ideal folding and assign consistent errors to inverse gates? | Nominal gate multiplication may not yield the assumed noise scaling | Verify compilation, ideal equivalence, and response on development circuits before the ZNE diagnostic |
| Which frozen vectors, diagnostic shot allocation, and batch count fit the campaign cap? | Mitigation and its matched raw baseline add executions even without new optimization runs | Prespecify the subset and count, include both optimizers, and charge all diagnostic work |
| Is an ideal reference available for the selected ZNE cases? | A shifted expectation alone cannot establish mitigation benefit | Use exact small-circuit expectations where feasible; otherwise limit conclusions |
