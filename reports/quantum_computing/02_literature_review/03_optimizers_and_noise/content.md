# QAOA Optimizers, Noise Evaluation, and Error Mitigation

## Scope and Objective Conventions

The original QAOA constructs a depth-$p$ parameterized state with $d=2p$ angles and optimizes the expectation of a diagonal cost operator. Farhi, Goldstone, and Gutmann formulate their objective as a maximization; measurement then produces candidate bitstrings [[1]](ref.md#farhi2014-qaoa). A minimization interface therefore requires an explicit sign convention. Circuit depth $p$, parameter count $d$, and the number of measurement shots are different quantities.

For this project's graph-based clustering demonstration, we propose comparing three classical optimizers within the same QAOA loop: COBYLA, first-order SPSA, and Adam. The initial evaluation uses the project's labeled breast cancer dataset, with Silhouette, external Adjusted Rand Index (ARI), and runtime as the three main outputs. Davies–Bouldin (DBI) and resampling stability remain topics of the broader ML literature review but are deferred from the initial experiments. Zero-noise extrapolation (ZNE) is the sole proposed error-mitigation technique, initially restricted to evaluation at frozen parameters after optimization. Simulated annealing is outside the comparison. The optimizer would receive a scalar estimate of the agreed graph/QUBO objective; Silhouette and ARI would assess the decoded clustering separately. This section proposes later experiments; it contains no implementation or project results.

The proposal concerns how established methods will be selected, combined, and compared. The cited QAOA construction, optimizer updates, and ZNE method supply the mathematical basis; the project choices are the clustering objective, configurations, budgets, and evaluation procedure.

Before evaluation, the QAOA formulation must specify the graph objective, QUBO-to-Ising conversion, constants, penalty terms, bit order, and decoding/feasibility rules. The expected-cost expression below follows QAOA [[1]](ref.md#farhi2014-qaoa). The additional loss expression is an optional affine conversion for the minimization interface:

$$
E(\theta)=\langle\psi_p(\theta)|H_C|\psi_p(\theta)\rangle,
\qquad
L(\theta)=\frac{\sigma E(\theta)+b}{a},
\quad a>0,\quad \sigma\in\{-1,+1\}.
$$

Here $\theta$ contains the agreed angle ordering. If a lower raw cost is preferred, the optimizer can use $L=E$ directly; this sets $\sigma=+1$, $b=0$, and $a=1$. If a higher raw score is preferred, use $L=-E$. An additional offset or scale would be introduced only for a stated reason, fixed before evaluation, and shared by all three optimizers on an instance. This conversion aligns the optimization direction and units; it does not define the clustering objective. Report the raw objective and penalty/feasibility information as well as the optimization loss.

Arbitrary weighted clustering objectives should not inherit angle periods or approximation-ratio formulas without checking their assumptions: the original paper's $\gamma$ interval uses an integer-valued cost spectrum [[1]](ref.md#farhi2014-qaoa).

## Evidence for a Three-Optimizer Comparison

Pellow-Jarman et al. compare ten optimizers on minimum vertex cover using 21 non-isomorphic five-vertex graphs. Their optimizer comparison uses depths one through five, ten runs per graph and condition, 10,000 shots in sampled simulations, and a stated cap of 5,000 function evaluations. Conditions include statevector simulation, ideal sampled execution, and a sampled device-noise model [[2]](ref.md#pellowjarman2024-qaoa-optimizers-noise).

Their results section reports similar statevector performance, greater differentiation with noise, and strong SPSA performance under their device-derived noise model; COBYLA is more affected in that setting. However, the abstract emphasizes Adam/AMSGrad for shot noise, whereas the results text describes a slight SPSA advantage. We therefore use the study as evidence that execution conditions and resource accounting matter, without extracting a universal ranking. Its minimum-vertex-cover formulation, small graphs, software, and noise model differ from this project's still-to-be-agreed clustering configuration [[2]](ref.md#pellowjarman2024-qaoa-optimizers-noise).

### COBYLA, SPSA, and Adam

COBYLA constructs linear interpolation models of the objective and constraints within a trust region, using $d+1$ interpolation points for $d$ variables. It does not require a supplied gradient [[3]](ref.md#scipy-cobyla). The interpolation points are maintained across the search; their count is not a rule that every update costs $d+1$ new evaluations.

SPSA estimates a gradient by perturbing all parameters simultaneously. For a random vector whose independent components $\Delta_{t,i}$ take $-1$ and $+1$ with equal probability, its basic estimate and update are

$$
\widehat g_{t,i}=
\frac{\widehat L(\theta_t+c_t\Delta_t)-\widehat L(\theta_t-c_t\Delta_t)}{2c_t\Delta_{t,i}},
\qquad
\theta_{t+1}=\theta_t-a_t\widehat g_t.
$$

One perturbation pair requires two objective evaluations, independently of $d$. Averaging $q$ independent pairs costs $2q$ evaluations. Coordinate-wise central finite differences instead require $2d$ evaluations for a basic gradient estimate. These are gradient-estimation counts, excluding additional work [[4]](ref.md#spall1992-spsa).

Spall establishes convergence and efficiency results under assumptions on smoothness, perturbations, gain sequences, measurement errors, and stability of the iterates. His numerical study concerns a classical statistical objective, not QAOA. Its findings motivate SPSA as a candidate for noisy objective measurements; they neither establish global optimization nor guarantee an advantage over COBYLA for shallow clustering circuits [[4]](ref.md#spall1992-spsa).

Adam supplies a gradient-based contrast. Kingma and Ba maintain exponential moving averages of the gradient and its elementwise square, correct their zero-initialization bias, and use them to adapt each parameter's update [[5]](ref.md#kingma2015-adam). With $g_t$ denoting the gradient estimate at $\theta_{t-1}$, the paper's Algorithm 1 is

$$
\begin{aligned}
m_t&=\beta_1m_{t-1}+(1-\beta_1)g_t,\\
v_t&=\beta_2v_{t-1}+(1-\beta_2)g_t^{\odot2},\\
\widehat m_t&=\frac{m_t}{1-\beta_1^t},\qquad
\widehat v_t=\frac{v_t}{1-\beta_2^t},\\
\theta_t&=\theta_{t-1}-\alpha
\frac{\widehat m_t}{\sqrt{\widehat v_t}+\epsilon}.
\end{aligned}
$$

Operations are elementwise, $m_0=v_0=0$, $\alpha>0$, $0\leq\beta_1,\beta_2<1$, and $\epsilon>0$. The second moment is an average of squared gradients, not the centered gradient variance. The original evidence concerns classical machine-learning objectives; its convex-analysis discussion does not establish convergence for this nonconvex QAOA task [[5]](ref.md#kingma2015-adam). Moving averages motivate testing Adam with fluctuating gradient estimates, but do not remove gate-noise bias. Its inclusion in the ten-optimizer QAOA study provides directly relevant, problem-specific motivation rather than proof of superiority for clustering [[2]](ref.md#pellowjarman2024-qaoa-optimizers-noise).

For the sampled comparisons, we propose parameter-shift gradients of the expected graph loss, subject to checking support for the chosen gates, observable, and fixed noise channels. PennyLane documents gate-dependent shift recipes and frequency-based generalizations [[6]](ref.md#pennylane-parameter-shift). Such a recipe can be exact for an expectation while its finite-shot estimate still fluctuates. The familiar two-shift count applies to a supported individual gate parameter; QAOA reuses each angle across many gates, so a full gradient is not automatically $2d$ circuit evaluations. Count the actual shifted circuits after decomposition, parameter sharing, and classical chain-rule processing. Simulator backpropagation or exact gradients, if used as optional references, must be labeled separately from this sampled comparison. Gradients act on the expected loss, not on decoded labels, Silhouette, or ARI.

| Comparison dimension | COBYLA | First-order SPSA | Adam |
|---|---|---|---|
| Derivative requirement | Objective values; no supplied gradient | Objective values used to estimate a stochastic gradient | Requires a gradient; proposed finite-shot parameter-shift estimates |
| Basic evaluation accounting | Count actual calls for model construction and search; no universal fixed count per update | Two calls per perturbation pair; more for averaging or added monitoring | All shifted-circuit estimates for a complete gradient, plus any forward/monitoring work; not one call per update |
| Initialization | Initial angle vector and initial trust-region radius | Initial angle vector, perturbation randomness, and gain schedules | Initial angle vector, zero moments, and reset timestep |
| Settings to declare | Initial radius, terminal radius/tolerance, evaluation cap, and any constraints | $a_t$, $c_t$, their schedule constants/exponents, perturbation distribution, and averaging count | $\alpha$, $\beta_1$, $\beta_2$, $\epsilon$, any schedule, gradient method, and shot allocation |
| Noise question | How reliable are local models when measurements fluctuate? | How do perturbation scale and stochastic gradient variance affect progress? | How do finite-shot gradients and moment adaptation affect progress and resource use? |

The method descriptions follow the cited method and software sources; the noise questions are proposed comparisons [[3]](ref.md#scipy-cobyla), [[4]](ref.md#spall1992-spsa), [[5]](ref.md#kingma2015-adam), [[6]](ref.md#pennylane-parameter-shift). No method is selected as the expected winner.

### Software, Calibration, and Restart Costs

SciPy provides `minimize(method="COBYLA")`; its documented `maxiter` limits function evaluations. The documentation records a switch to PRIMA in SciPy 1.16.0 [[3]](ref.md#scipy-cobyla). Consequently, an older paper's COBYLA result is not automatically a result for the current implementation.

PennyLane provides `SPSAOptimizer` with learning-rate and perturbation schedules. Its `step_and_cost` performs an additional objective call beyond the two perturbation calls and returns the cost before the update. In the inspected source, initialization assigns schedule constants without objective-based calibration [[7]](ref.md#pennylane-spsa-optimizer). We propose setting and recording the constants explicitly, rather than treating defaults as tuned settings.

PennyLane provides `AdamOptimizer`, configurable moments and step size, and a `reset` method. Its inherited `step_and_cost` returns the pre-update cost and requests a forward evaluation if one is not already available from gradient computation [[8]](ref.md#pennylane-adam-optimizer). Count actual forward and derivative executions without assuming a fixed extra call or free automatic differentiation. Reset Adam's moments and timestep for every independent run and restart. Pin the interface and version and set all hyperparameters explicitly: the inspected implementation's defaults differ from the original paper, and its numerical-stability convention places $\epsilon$ with the uncorrected second moment [[8]](ref.md#pennylane-adam-optimizer).

Any development-stage calibration or hyperparameter trial would count as tuning work. Any objective-based calibration during a reported run, added gradient resampling, monitoring, and restart initialization would count against that run's budget. If a different implementation adds blocking, line searches, or calibration, its extra calls must be included. Coordinate-wise numerical gradients are not the proposed Adam route; if a finite-difference fallback is later used, declare its step size, $2d$ basic central-difference estimates, and finite-shot sensitivity, and report it as a changed configuration. Each restart is another optimization attempt, not a free chance to improve the reported result.

## Separating Sampling Variability and Operation Noise

PennyLane documents that finite shots simulate sampled measurements, while supported simulator calculations with `shots=None` return exact expectations [[9]](ref.md#pennylane-measurements). For a fixed circuit and a diagonal classical cost, we propose estimating its loss from $S$ sampled bitstrings as

$$
\widehat L_S(\theta)=\frac{1}{S}\sum_{s=1}^{S}\ell(z_s),
$$

where $\ell$ applies the declared sign, scaling, offset, and penalties to a measured bitstring. Under independent sampling, this mean has variance $\operatorname{Var}(\ell(Z))/S$. This is a sampling calculation, not an observed project result. A larger shot count reduces sampling variance but increases execution cost.

Gate noise changes the circuit's state evolution; the PennyLane tutorial describes noisy channels, including depolarizing errors [[10]](ref.md#arrazola2021-noisy-circuits). Readout error changes recorded measurement outcomes and is represented separately in the mixed-state device's documented `readout_prob` setting [[11]](ref.md#pennylane-default-mixed). Increasing shots estimates the resulting noisy distribution more precisely; it does not generally restore the ideal distribution. This inference also explains why SPSA's handling of fluctuating measurements should not be interpreted as error mitigation.

### Proposed Execution Comparisons

| Comparison | Procedure | Interpretation |
|---|---|---|
| Ideal finite-shot optimization | Optimize all three methods with perfect gates/readout and a fixed finite shot allocation | Combined initialization, optimizer, and measurement-sampling behavior |
| Fixed-parameter noise robustness | Freeze the parameters returned by an ideal finite-shot run; evaluate that same compiled circuit under ideal and declared noisy conditions | Sensitivity of a fixed prepared solution; no reoptimization |
| Noisy optimization | Start again from the matched initial vectors and optimize with the declared noise profile active throughout | Behavior of the complete loop under the noisy objective |

For fixed-parameter comparisons, use fresh measurement batches and matched evaluation allocations across conditions. For noisy optimization, evaluate returned parameters using fresh samples under the same declared noisy condition; an ideal evaluation of those parameters may be an explicitly labeled additional diagnostic. An exact-expectation reference is optional if feasible and must be reported separately from sampled execution. Fixed-parameter degradation and the outcome of reoptimization answer different questions.

### Proposed Synthetic Noise Assumptions

We propose one small, stationary synthetic noise profile initially. Its parameters would be chosen on development instances and frozen before evaluation. PennyLane's documented single-qubit depolarizing convention applies a nonidentity Pauli error with total probability $u$ [[10]](ref.md#arrazola2021-noisy-circuits):

$$
\mathcal D_u(\rho)=(1-u)\rho+
\frac{u}{3}\bigl(X\rho X+Y\rho Y+Z\rho Z\bigr).
$$

| Component | Proposed placement and meaning | Limitation |
|---|---|---|
| One-qubit gate error | Apply $\mathcal D_{u_1}$ to the wire after each compiled one-qubit gate | A gate-independent synthetic channel |
| Two-qubit gate error | After each compiled two-qubit gate, apply $\mathcal D_{u_2}$ independently to each participating wire | Local errors after an entangling gate; not a correlated two-qubit depolarizing channel or a calibrated gate infidelity |
| Readout error | Independently flip each recorded computational-basis bit with probability $r$ | Symmetric assignment error; no asymmetric or correlated readout model |

The independent readout rule has assignment matrix

$$
P(\widetilde z\mid z)=
\begin{pmatrix}1-r&r\\r&1-r\end{pmatrix},
$$

with rows indexed by the recorded bit and columns by the true bit. We propose $u_1=u_2=r=0$ for the ideal condition and one declared nonzero profile for the initial noisy comparison. Numeric probabilities remain development choices, not values inferred from the cited experiment or a physical backend. Initial preparation of $|0\cdots0\rangle$ is assumed ideal; any compiled unitary state-preparation gates receive the same assigned gate-error channels as other unitary gates.

Compile the ideal circuit to a declared gate basis and connectivity first; then associate channels with the actual gate names, wires, and arities. Keep the compiled template fixed across optimizers and paired execution conditions. Record routing, layout, compiler/version, and compilation seed where relevant. Do not add both an error to a high-level operation and errors to its decomposed gates. If zero-angle simplification changes the gate sequence, disclose that change or disable that simplification for the controlled comparison. These are proposed controls for matching errors to executed gates.

This initial model assumes independent, stationary errors and omits drift, coherent miscalibration, leakage, crosstalk, and duration-dependent relaxation. A combined gate/readout condition supports a combined-noise conclusion only. Optional gate-only and readout-only fixed-parameter diagnostics would isolate components if the budget permits; there is no proposed large noise grid or hardware-fidelity claim.

## Proposed Error Mitigation with ZNE

Cai et al. describe quantum error mitigation as estimating ideal expectation values from noisy circuit data. Reducing systematic bias can increase estimator variance, so mitigation should be assessed through accuracy and resource cost together. Their review explains ZNE as measuring at increased noise levels and extrapolating to the zero-noise limit, subject to assumptions about noise scaling and the fitted response [[12]](ref.md#cai2023-quantum-error-mitigation).

Mitiq documents digital unitary folding and selectable extrapolation models, including `fold_global` and `LinearFactory(scale_factors=[1, 3])` [[13]](ref.md#mitiq-zne-guide). We propose this single, two-point linear configuration as a small initial diagnostic, not as an established best configuration or an additional optimizer.

### Frozen Parameters and a Limited Mitigation Target

Use a small, prespecified set of instances and starts, including the parameters returned by all three optimizers in the ideal finite-shot optimization condition. Freeze those vectors before mitigation; do not use ZNE to retune them or select favorable restarts. The target is the raw objective expectation $E(\theta)$ for each fixed vector. This scalar estimator does not itself produce corrected bitstrings or feasible partitions and does not by itself change Silhouette or ARI. Clustering outputs retain their ordinary decoding and evaluation rules; ZNE estimation errors and runtime are reported as a separate diagnostic.

For the main ZNE diagnostic, use the gate-only version of the declared noise profile: keep $u_1$ and $u_2$ fixed and set $r=0$ in both the unmitigated and ZNE arms. Folding adds gate operations without proportionally scaling a terminal readout error. Consequently, extrapolating gate noise does not generally remove a fixed readout contribution. A combined-noise test, if later added, must be labeled separately and must not be compared against a gate-only baseline as though the conditions matched.

### Folding and Linear Extrapolation

Let $U_\theta$ be the unitary part of the compiled circuit at frozen parameters. The proposed nominal scale factors are $\lambda\in\{1,3\}$, with

$$
U_1=U_\theta,
\qquad
U_3=U_\theta U_\theta^\dagger U_\theta.
$$

These have the same ideal action. With matched finite-shot estimates $\widehat E_1$ and $\widehat E_3$, a line through the two measured points has zero-noise intercept

$$
\widehat E_{\mathrm{ZNE}}=
\frac{3\widehat E_1-\widehat E_3}{2}.
$$

This is a derived two-point formula, not a project measurement. The factors are nominal relative folding scales, not measured hardware error probabilities. The method assumes that they approximately parameterize one smooth noise response. Gate insertion can change the effective error model; Cai et al. and Mitiq both discuss limitations of noise amplification [[12]](ref.md#cai2023-quantum-error-mitigation), [[13]](ref.md#mitiq-zne-guide). Two points always define a line and therefore cannot establish that the underlying response is linear.

Fold only the ideal unitary circuit, excluding initialization/reset operations, measurements, and noise channels. Preserve terminal measurements and qubit ordering. Resolve inverse gates in the chosen basis, protect the inserted gate pairs from compiler cancellation, and then attach the original per-gate error channels to every executed gate, including inverse gates. Do not also multiply the channel probabilities by the folding factor. A later implementation must verify ideal equivalence and record actual gate counts/depth after all compilation steps; nominal tripling is not sufficient evidence of accurately tripled noise for the proposed local channel model.

### Matched Resources and Accuracy Assessment

For one computational-basis measurement setting, reserve an even total of $T$ shots per comparison arm at each frozen vector:

| Arm | Shot allocation | Quantity reported |
|---|---|---|
| Unmitigated gate-noise baseline | $T$ shots at $\lambda=1$ | Raw objective estimate |
| ZNE | $T/2$ shots at each of $\lambda=1$ and $\lambda=3$ | Both scale estimates and their extrapolated intercept |
| Ideal reference, where feasible | Exact noiseless expectation at the same vector | Accuracy target; record simulator work separately |

Use independent shot batches for the two ZNE scale estimates and the matched baseline. Executing both sampled arms therefore costs $2T$ shots per frozen vector and diagnostic repetition. Reusing an earlier baseline is not assumed. The ZNE arm has the same total shots as the raw arm but longer circuits: if folding actually triples the gate count, its equal allocation uses twice as many gate applications overall. Report actual circuit executions, scale-specific shots, compiled depth, two-qubit counts, gate applications weighted by shots, and processing time; equal shots do not establish equal execution time.

For independent scale estimates, the extrapolation has variance

$$
\operatorname{Var}(\widehat E_{\mathrm{ZNE}})=
\frac{9}{4}\operatorname{Var}(\widehat E_1)+
\frac{1}{4}\operatorname{Var}(\widehat E_3).
$$

Thus bias reduction need not imply lower total error at the available budget. Where the ideal reference and repeated diagnostic batches are affordable, compare absolute errors and empirical mean squared error, including unsuccessful mitigation. With very few batches, report individual errors and variance estimates without claiming established bias reduction. Without a suitable reference, an estimator shift alone does not demonstrate improved accuracy. Retain values outside the observable's allowed range with a warning flag rather than clipping them to create apparent improvement. ZNE inside the optimizer loop and other mitigation techniques are beyond this initial proposal.

## Fair Budgets and Reproducible Comparisons

We propose a small initial campaign with the same instances, graph construction, cost convention, compiled circuit, depth, and actual starting angle vectors for all three optimizers. One fixed shallow depth, provisionally $p=1$ if compatible with the QAOA formulation, one training shot allocation, and one nonzero noise profile would keep the initial contrast focused. Additional depths or settings would be separate extensions. External class labels would remain outside optimizer tuning and parameter selection.

Prespecify development and evaluation observation sets before tuning, with their shared dataset provenance, overlap, and preprocessing-fit policy recorded. Give each optimizer a declared development budget; freeze its settings before using reserved evaluation sets. Subsets from the same breast cancer dataset, initialization repetitions, and simulator measurement batches are not independent datasets. Record separate random streams for subset selection, initial angles, SPSA perturbations, compilation, training shots, and final evaluation shots. A common seed integer alone does not establish equivalent random draws across libraries.

### Accounting Units and Stopping

A charged objective estimate is one base-cost expectation estimate at a particular executed circuit setting, including a shifted setting inside a gradient calculation. It may require one or several measurement groups. A top-level Adam gradient call can therefore contain many charged estimates; batching them in one software request does not make them one execution. Let $N_{\mathrm{train}}$ include every such training estimate, including startup, in-run calibration, monitoring, derivatives, and restarts. With $G_j$ executed measurement groups for estimate $j$, the proposed shot ledger is

$$
Q_{\mathrm{train}}=
\sum_{j=1}^{N_{\mathrm{train}}}\sum_{g=1}^{G_j}S_{jg},
\qquad
Q_{\mathrm{total}}=Q_{\mathrm{train}}+Q_{\mathrm{final}}+Q_{\mathrm{diagnostic}}.
$$

For a diagonal QUBO cost, one computational-basis sample batch can provide all diagonal term values; multiple Hamiltonian terms do not automatically require separate shot batches. Record actual circuit executions and measurement grouping rather than equating two SPSA objective calls with two individual shots.

The diagnostic ledger includes both the matched raw and ZNE arms, including every scale, repeated batch, failed attempt, and any additional scaling/calibration checks. Record ZNE calls separately from optimizer-training calls. Select and freeze its diagnostic subset, $T$, and repetition count within the campaign cap before evaluation. The post-optimization proposal adds diagnostic executions without multiplying the full optimization-run count.

Use equal training charged-estimate caps $B$ and shot caps for all three optimizers within a matched condition, counting every Adam derivative setting. With fixed grouping and shots per estimate, their relationship is transparent; otherwise the shot ledger governs quantum usage. Stop before requesting work that would exceed either cap, including a complete SPSA perturbation pair or Adam gradient/update. If an unexpected failure interrupts a gradient, charge the partial work and retain the last completed iterate rather than applying an undeclared partial-gradient update. Report actual consumption, termination reason, failures, and any incomplete work. Iteration counts alone are not a fair quantum-resource budget. Early stopping may leave unused resources and should not be represented as equal consumption.

The initial return policy is to evaluate the vector returned by each pinned optimizer at termination, recording that implementation's return rule. Use an equal, separately reserved final-evaluation allocation with fresh shot seeds. Do not use those final samples to select parameters, tune settings, or discard restarts. Repetitions are reported individually and summarized within each instance; any future best-of-restarts procedure must include all attempts and be declared separately.

Choose the number of evaluation observation sets and repetitions after a later development-stage feasibility check measures complete-run cost. Then freeze the counts and a campaign resource cap. Three optimizers and two optimization conditions require $6IR$ full optimizations for $I$ evaluation observation sets and $R$ starts, before fixed-parameter diagnostics. At fixed $I$ and $R$, including Adam increases this run count by 50% relative to two optimizers, so feasibility must be checked against the full campaign cap. These $R$ starts include all planned initialization attempts; no extra automatic restarts are proposed. No resampling-stability fits are scheduled in the initial campaign. Fixed-observation repetitions are retained to describe stochastic run variation, with their count determined by the budget. If affordable repetitions are very few, show individual outcomes and describe the comparison as exploratory.

### Quality, Resources, and Remaining Agreements

The selected dataset is the project's breast cancer dataset with diagnosis labels available for external evaluation. Its exact source/version, feature columns, label coding, and missing-data policy must be recorded before experiments; no particular dataset variant or sample count is assumed here. Exclude the diagnosis column and record identifiers from the input features. Reserve diagnosis labels for scoring only: they must not select observation subsets, scaling, PCA dimensions, graph parameters, optimizer settings, stopping rules, or the chosen bitstring/restart.

After an optimizer returns its parameters, obtain a partition using the same fresh-shot allocation and prespecified feasible-decoding rule for all three methods. Align predicted labels, diagnosis labels, and evaluation features by the same observation IDs. Use one shared scaled original-feature representation for headline Silhouette scores, even when PCA features are used to construct the graph. The preprocessing-fit population remains an explicit agreement with the preprocessing review.

| Main output | Calculation and inputs | Interpretation |
|---|---|---|
| Silhouette | Mean coefficient from the shared evaluation features and predicted cluster labels [[14]](ref.md#scikit-learn-silhouette-score) | Internal cohesion/separation; higher is preferred |
| External ARI | Chance-adjusted agreement between diagnosis labels and predicted cluster labels on the same observations [[15]](ref.md#scikit-learn-adjusted-rand-score) | Reference agreement; higher is preferred |
| Runtime | End-to-end clustering wall time under the common boundary defined below | Efficiency, interpreted alongside quality, failures, and actual resource usage |

The documented Silhouette validity condition is $2\leq k\leq n-1$ for $k$ observed groups and $n$ evaluated observations [[14]](ref.md#scikit-learn-silhouette-score). Record unavailable scores with reasons rather than assigning a favorable numeric value. ARI is unchanged by permuting cluster names [[15]](ref.md#scikit-learn-adjusted-rand-score); no label matching based on diagnoses is needed. A defined ARI must not erase an infeasible or degenerate task output. Record predicted/reference group counts, group sizes, and failure flags, especially for small subsets. Diagnosis agreement is an evaluation of the grouping against that reference, not a classification-accuracy percentage.

The proposed headline runtime begins with preprocessing the already selected feature table and ends when a partition or a recorded failure is returned. It includes graph construction, circuit compilation, optimization and its objective evaluations, final sampling, and decoding. Exclude dataset download/loading, metric calculation, plotting, and the separate ZNE diagnostic. Report shared setup and optimization-only time as components; if setup is reused, include its recorded cost once in each method's end-to-end total and disclose that accounting. Use the same machine, simulator/backend, timing boundary, and declared threading settings for all three optimizers. Record ZNE circuit execution and postprocessing time separately.

Report optimization loss and raw graph objective separately from these three main outputs and from task feasibility. For each observation set, retain every attempted run, invalid partition, and failure reason. Proposed summaries are individual values, median, interquartile range, and valid/attempted counts. Do not pool shots, starts, or overlapping subsets as independent datasets or claim generalization across datasets from this single source.

DBI and resampling stability are excluded from the initial experimental output list; their literature coverage is retained in the broader ML review. This does not remove fixed-observation repetitions, validity checks, or failure reporting. Supporting resource records remain required: objective calls, circuit executions, total shots by stage, restarts, qubits, compiled depth, two-qubit gate count, and timing components. Identify raw versus ZNE objective-estimation errors and mitigation overhead separately; Silhouette, ARI, and clustering runtime alone do not establish ZNE effectiveness. Simulator time is a simulator cost, not evidence of quantum speedup.

| Related design | Required agreement before final experiments | Current treatment |
|---|---|---|
| QAOA/clustering formulation | Graph task, objective sign/scale, penalties, angle ordering/domain, bit order, feasible decoding, and allowed depth | Explicit requirements above; no final formulation assumed |
| ML evaluation | Breast cancer dataset identity/coding, common observation IDs, label isolation, original-space Silhouette, external ARI, runtime boundary, failure policy, repetition budgets, and shared run IDs | Initial output list narrowed to Silhouette, ARI, and runtime; DBI/resampling experiments deferred; alignment with the broader ML draft remains pending |
| Quantum execution | Software versions, gate basis, routing, noise placement/probabilities, measurement grouping, and stopping/return rules | Proposed controls; implementation-dependent values remain open |
| ZNE diagnostic | Frozen-vector subset, gate-only profile, preservation of folded gates, inverse-gate errors, extrapolation configuration, ideal reference, and complete diagnostic budget | One post-optimization proposal; compatibility and affordable counts remain to be established |

The final experimental specification requires the loss, decoding, compilation target, noise probabilities, mitigation controls, and resource caps to be agreed together. Until those decisions are fixed, this proposal remains conditional; it establishes neither a preferred optimizer nor an observed mitigation benefit.
