# Clustering Evaluation and Experimental Design

## Initial Experimental Scope

The initial comparison uses the project's labeled breast cancer dataset and three main outputs: Silhouette, external Adjusted Rand Index (ARI), and end-to-end clustering runtime. Diagnosis labels are reserved for external evaluation. DBI and resampling stability are retained below as reviewed methods and possible later extensions, but their experiments are deferred. Budget-dependent repetitions on fixed observations, metric-validity checks, failure reporting, and quantum resource accounting remain part of the initial design. This is an evaluation proposal, not a report of completed experiments.

The metric definitions below are adopted from the cited literature. The proposed contribution is an evaluation protocol: which observations, scoring representation, repetitions, resource limits, and reporting rules will be shared across methods.

## Silhouette as an Internal Evaluation Measure

Silhouette evaluates a partition from its dissimilarities without depending on the algorithm that produced it [[1]](ref.md#rousseeuw1987-silhouettes). This makes it a candidate for the proposed comparison of classical and quantum-assisted clustering.

For observation $i$, let $a(i)$ be its mean distance to the other members of its cluster, and let $b(i)$ be the smallest mean distance to any competing cluster. Its coefficient is

$$
s(i)=\frac{b(i)-a(i)}{\max\{a(i),b(i)\}}.
$$

Values near one indicate a strong fit, values near zero indicate ambiguity, and negative values favor a competing cluster on average. The overall score averages the observation-level coefficients [[2]](ref.md#scikit-learn-silhouette-score).

The plot groups individual coefficients by cluster; its widths reveal weak assignments, while its heights indicate cluster sizes. Comparing overall averages across candidate cluster counts can inform the choice of $k$ [[1]](ref.md#rousseeuw1987-silhouettes).

### Silhouette Assumptions and Interpretation

Rousseeuw's construction assumes ratio-scale dissimilarities and favors compact, separated clusters. Singletons receive zero by convention. A distant-outlier example demonstrates why a high mean alone needs scrutiny [[1]](ref.md#rousseeuw1987-silhouettes). These limitations motivate caution about curved or irregular groups if encountered and against a universal pass/fail threshold. The initial breast cancer study does not add a separate synthetic cluster-shape sweep.

The documented scikit-learn validity condition is $2\leq k\leq n-1$, where $k$ is the number of observed clusters and $n$ the number of evaluated observations [[2]](ref.md#scikit-learn-silhouette-score). We propose reporting invalid partitions as unavailable scores with explicit reasons and failure counts. A partition containing a singleton should be distinguished from an entirely singleton partition.

## Davies–Bouldin as a Reviewed Complementary Measure

Davies and Bouldin compare within-cluster dispersion with separation between representative centers. Their index averages each cluster's largest similarity ratio, so a lower value indicates better separation relative to spread. The formulation permits different choices of distance and dispersion [[3]](ref.md#davies1979-cluster-separation).

For the reviewed Euclidean variant, use centroid separation and mean Euclidean distance to the centroid, corresponding to the paper's choices $p=2$ and $q=1$. This is the variant to consider if DBI is added later; it is not an initial experimental output. For a nonempty cluster $C_i$ of size $n_i$, define

$$
\mu_i=\frac{1}{n_i}\sum_{x\in C_i}x,
\qquad
S_i=\frac{1}{n_i}\sum_{x\in C_i}\lVert x-\mu_i\rVert_2,
\qquad
M_{ij}=\lVert\mu_i-\mu_j\rVert_2.
$$

For $k$ clusters, the index is

$$
R_{ij}=\frac{S_i+S_j}{M_{ij}},
\qquad
\operatorname{DBI}=\frac{1}{k}\sum_{i=1}^{k}\max_{j\ne i}R_{ij}.
$$

The maximum selects the most similar competing cluster for each $i$. Each cluster contributes equally to the final average, regardless of its membership count. These equations specialize the original construction rather than introducing a new clustering algorithm [[3]](ref.md#davies1979-cluster-separation).

### Evidence and Degenerate Partitions

The paper demonstrates the measure through alternative partitions, hierarchical examples, and Iris. In its Iris experiment, the procedure prefers two groups despite the dataset's three species, with two species overlapping in feature space [[3]](ref.md#davies1979-cluster-separation). For this project, this is a reason to distinguish geometric separation from agreement with reference labels. It is a published example, not a result from the shared project.

The original ratio requires at least two clusters and nonzero distances between their representative centers. A singleton has zero dispersion; allowing every distinct observation to form its own cluster can drive the index to zero. The authors constrain singleton handling in their demonstration while acknowledging other possible policies [[3]](ref.md#davies1979-cluster-separation).

We propose recording empty-cluster failures, observed cluster counts, and singleton counts explicitly. If DBI is added later, coincident centroids should produce an unavailable DBI score with a reason rather than be interpreted as perfect quality. A singleton within an otherwise admissible partition should be flagged, not automatically equated with an entirely singleton partition. We propose excluding the latter from quality rankings. A numerical tolerance for near-coincident centers would be needed before a future DBI analysis.

### Joint Interpretation of Internal Measures

Silhouette and DBI both assess within-group cohesion relative to between-group separation, but they use different summaries [[1]](ref.md#rousseeuw1987-silhouettes), [[3]](ref.md#davies1979-cluster-separation).

| Aspect | Silhouette | Reviewed Euclidean DBI |
|---|---|---|
| Preferred direction | Higher | Lower |
| Distance summary | Observation-to-cluster average dissimilarities | Dispersion around centroids and distances between centroids |
| Overall weighting | Equal weight per observation | Equal weight per cluster |
| Diagnostic detail | Individual coefficients and cluster plots | Most similar competitor for each cluster |

These differences follow from their constructions [[1]](ref.md#rousseeuw1987-silhouettes), [[3]](ref.md#davies1979-cluster-separation). If both are used, their scores should be reported separately rather than combined into one ranking. The initial study selects Silhouette as its internal score and defers DBI. As an implication of the chosen geometry, disagreement in a future joint analysis should prompt inspection of cluster shape, size, and assignments. The reviewed papers do not establish either score as sufficient evidence for quantum advantage or for recovery of externally labeled classes.

## Adjusted Rand Index as an Agreement Measure

Hubert and Arabie compare partitions of the same observation set through pairwise membership relationships. A pair agrees when its observations are placed together in both partitions or apart in both; the ordinary Rand Index (RI) is the fraction of agreeing pairs. Their chance correction accounts for agreement expected under a generalized hypergeometric model that preserves the cluster sizes of each partition [[4]](ref.md#hubert1985-comparing-partitions).

For a nondegenerate comparison, the Adjusted Rand Index can be written as

$$
\operatorname{ARI}=\frac{RI-\mathbb{E}[RI]}{1-\mathbb{E}[RI]}.
$$

A value of one indicates identical group membership, zero indicates agreement equal to the chance baseline, and negative values indicate agreement below that baseline. Zero is the expectation under the stated random-partition model, not a score that every random realization must attain. The paper derives the expectation and corrected index in Section 2.1 [[4]](ref.md#hubert1985-comparing-partitions).

Because this calculation depends on membership relationships rather than group names, renaming cluster identifiers leaves ARI unchanged. The grouping $(0,0,1,1)$ therefore matches $(7,7,2,2)$ perfectly when the observation order is the same. This is a mathematical illustration, not a project result. Different cluster counts are permitted by the comparison framework, although splitting or merging groups changes the membership relationships being assessed [[4]](ref.md#hubert1985-comparing-partitions).

### Reference Labels, Validity, and Interpretation

For the selected breast cancer dataset, diagnosis labels provide the proposed external reference. Compare each output partition with those labels on exactly the same observation IDs. Reserve the labels for evaluation: they must not select observation subsets, scaling, PCA dimensions, graph parameters, optimizer settings, stopping rules, decoding, or the preferred stochastic run. Exclude the diagnosis column and record identifiers from the feature matrix. The exact dataset variant and label coding still require documentation. If suitable labels are missing or cannot be aligned, report external ARI as unavailable rather than infer it from an internal score.

ARI can also compare two algorithm outputs without treating either as ground truth, since the underlying formulation compares two partitions symmetrically [[4]](ref.md#hubert1985-comparing-partitions). Such comparisons would be labeled between-run agreement and restricted to shared observation IDs if added later. The deferred resampling design below develops this use separately from the initial external agreement with diagnosis labels.

Metric validity should be recorded separately. A one-cluster output can have a defined ARI against a nontrivial reference even when its Silhouette is undefined; this follows from the respective formulas [[2]](ref.md#scikit-learn-silhouette-score), [[4]](ref.md#hubert1985-comparing-partitions). We propose keeping the partition's failure or degeneracy flag alongside any defined metric. Comparisons with fewer than two observations, or a zero denominator in the displayed ARI expression, require an explicit convention before implementation; no convention is assumed here.

The chance baseline depends on cluster sizes [[4]](ref.md#hubert1985-comparing-partitions). Accordingly, we propose reporting reference and predicted cluster counts and sizes with ARI, particularly for small subsets. An ARI value is not a percentage of correctly labeled observations, a significance test, or evidence that the reference classes are the only meaningful grouping. Reference-label suitability and the effect of imbalanced groups remain questions for the evaluation design.

### Complementarity with Internal Quality

| Measure | Evaluation question | Preferred direction | Inputs beyond the output partition |
|---|---|---|---|
| Silhouette | How well do observations fit their clusters relative to competitors? | Higher | A declared dissimilarity representation |
| Reviewed Euclidean DBI, deferred | How small is centroid-based dispersion relative to separation? | Lower | A shared feature representation |
| External ARI | How closely does membership agree with a reference beyond chance? | Higher | A reference partition on the same observations |

These distinctions follow from the three constructions [[1]](ref.md#rousseeuw1987-silhouettes), [[3]](ref.md#davies1979-cluster-separation), [[4]](ref.md#hubert1985-comparing-partitions). The initial experiment reports Silhouette and external ARI separately, with runtime as the efficiency output. DBI remains a reviewed complement for a possible extension. The published DBI Iris example motivates examining geometric quality and reference agreement together; it does not establish that the shared project will reproduce that example or any particular ARI value.

## Stability Under Resampling

Resampling stability is reviewed to compare its assumptions, evidence, and costs. Its experiments are deferred from the initial study; the construction below describes a possible extension rather than scheduled evaluation work.

Ben-Hur, Elisseeff, and Guyon evaluate stability through the distribution of similarities between partitions obtained from perturbed datasets. Their model explorer repeatedly draws two subsamples, clusters each separately, and compares the labels of observations common to both. Inspecting the distribution, rather than only its mean, can reveal variability or competing solutions concealed by an average [[5]](ref.md#benhur2002-stability).

The paper relates cluster-count selection to a transition from similarities concentrated near one to a broader distribution. Its experiments use average-link hierarchical clustering, with structured synthetic data, selected yeast microarray data, and uniformly random data. These illustrate a diagnostic heuristic rather than a general recovery guarantee. The experimental similarity is a co-membership correlation/cosine measure associated with Fowlkes–Mallows; Jaccard and matching measures are also discussed. It is not an ARI-based experiment [[5]](ref.md#benhur2002-stability).

### Variability Sources and a Deferred Resampling Design

The review distinguishes two questions. The initial experiment retains fixed-observation quality/runtime repetitions when affordable, while deferring a formal resampling-stability analysis:

| Stability question | What changes | What is held fixed |
|---|---|---|
| Initialization or run stability | Algorithm initialization or optimizer random seed | Observation IDs, feature representation, graph rules, and declared execution conditions |
| Sampling stability | Observation subset, followed by reclustering | Parent dataset, sampling rule, preprocessing policy, algorithm settings, and declared execution conditions |

For a future sampling-stability extension, paired subsets would be drawn without replacement within each subset, using the same subset pairs for every method. Let $S_r$ and $T_r$ be the observation ID sets for pair $r$, and let $I_r=S_r\cap T_r$. Each partition would be fitted using its whole subset, with agreement calculated only on the intersection. With $\ell_{S_r}(I_r)$ denoting the labels of those shared observations, a possible score is

$$
A_r=\operatorname{ARI}\bigl(\ell_{S_r}(I_r),\ell_{T_r}(I_r)\bigr).
$$

This combines the paper's resampling design with the ARI formulation; replacing its reported similarity measure with ARI is a project proposal, not a replication of its experiments [[4]](ref.md#hubert1985-comparing-partitions), [[5]](ref.md#benhur2002-stability). A future implementation would record the parent instance, both subsets, their intersection, sampling seeds, algorithm seeds, and full-subset and intersection cluster counts. Pair counts, fractions, and overlap rules would require a separate feasibility review before activating that extension.

In that extension, fixing algorithm seeds during sampling comparisons and varying them separately on fixed observations would help separate the two questions. A nested design could examine their interaction only with an additional budget. Quantum shot variability and gate-noise conditions remain separately identified in the optimizer/noise protocol; the resampling paper does not evaluate quantum execution.

### Interpretation and Limits

The paper explicitly identifies the one-group solution as trivially stable and warns that sampling too little can omit clusters. Its hierarchical experiments also use a cluster-size heuristic to handle outliers. The authors acknowledge that the example datasets have recognizable structure and that repeated clustering adds computational cost [[5]](ref.md#benhur2002-stability). We therefore propose checking trivial partitions, cluster sizes, and overlap sizes alongside agreement. Empty or insufficient overlaps and undefined scores should be counted with their reasons; they should not silently disappear from the summary. The paper's outlier heuristic is not automatically adopted for QAOA or the classical baselines.

If the extension is performed, report the empirical agreement distribution, its median and interquartile range, and valid/excluded comparison counts. These summaries describe variation under the declared perturbation; they are not automatically confidence intervals for performance on new datasets. Repeated subsets of one parent dataset must not be presented as additional independent datasets.

Stability would supplement internal geometry and external agreement in a future extension. A consistently produced partition is not, by that fact alone, suitable for the intended task. Reference labels remain reserved for external evaluation, and any future stability-based settings would be selected on development instances only. No stability threshold or automatic cluster-count rule is adopted, and deferring the experiment provides no evidence of robustness.

## Integrated Evaluation Proposal

The following protocol proposes a comparison of classical baselines and a small QAOA-assisted clustering demonstration on the labeled breast cancer dataset. Its main outputs are Silhouette, external ARI, and end-to-end runtime. DBI and resampling-stability experiments are deferred while their literature coverage is retained above. Quantum PCA and simulated annealing are not assumed to be implemented. Budgets and repetition counts require later feasibility work; no project results or teacher-imposed numerical settings are asserted.

Within the QAOA arm, the proposed optimizer comparison is COBYLA, first-order SPSA, and Adam, using the same declared graph objective, observation IDs, starting angle vectors, and final decoding policy. The QC optimizer review supplies the method details. This ML protocol applies the same headline metrics and timing boundary to all three configurations and the matched classical baselines; optimizer loss is reported separately from clustering quality.

### Instances, Development, and Common Observations

Record the exact breast cancer dataset source/version, feature columns, diagnosis coding, record identifiers, missing-data policy, and row IDs before evaluation. The dataset name alone does not establish a particular public variant, sample count, feature count, or class encoding. Reserve development and evaluation observation sets before selecting settings. Development would establish feasible sample sizes, preprocessing rules, graph parameters, optimizer budgets, and stopping conditions; these would then be frozen. Record any overlap and shared source population. Different subsets of this one dataset remain within-dataset comparisons; disjointness alone does not establish independence or across-dataset generalization.

Within each evaluation observation set, every method would receive the same IDs. If quantum resources limit sample size, classical comparisons would include that same subset. Larger classical-only runs could be supplementary but would not substitute for a matched comparison. Choose subsets without consulting diagnosis labels and retain draw seeds and IDs. Diagnosis labels and record identifiers are excluded from features; labels must not select preprocessing, PCA dimensions, graph settings, optimizers, stopping rules, decoding, or a favorable run. Synthetic shape/no-designed-cluster sweeps are possible future extensions, not part of the initial dataset roster.

Headline Silhouette would use one shared scaled original-feature representation for each observation set, even when PCA or graph embeddings are used for clustering. Align feature rows, predicted labels, and diagnosis labels by the same IDs. Declare the scaling method and fitting population: a deployment-style study would fit preprocessing on development data, whereas a transductive study may apply a prespecified label-free fitting rule within each observation set. Do not mix these designs without identification. A future stability extension would need its own fixed-versus-refitted preprocessing policy.

The official Silhouette interface permits random metric subsampling [[2]](ref.md#scikit-learn-silhouette-score). We propose scoring all observations in the small common instances initially. Any later metric-only subsampling would use recorded common scoring IDs and a renewed validity check.

### Repetitions, Budgets, and Reproducibility

| Control | Provisional starting proposal | Decision before final evaluation |
|---|---|---|
| Fixed-observation stochastic runs | Choose a run count $R$ from a declared development-stage resource budget | Estimate complete-run cost before setting $R$; freeze the count and report every attempted run. Deterministic configurations need one identified run, not artificial repetitions. |
| Common observation sets | Choose affordable breast cancer observation sets without diagnosis-label selection; share their IDs across methods | Freeze sizes, subset seeds, overlap disclosures, and the preprocessing-fit policy before evaluation. |
| DBI | Deferred from the initial experimental outputs | Retain the reviewed formulation and limitations; declare the variant and validity conventions before any extension. |
| Resampling stability | Deferred; no paired-subset fits are scheduled initially | A future pair count, fraction, overlap screen, and budget would require explicit review; current repetitions do not establish resampling robustness. |
| Optimizer and shot budgets | Declare charged objective-estimate limits, stopping rules, shots per circuit, and total shots for COBYLA, SPSA, and Adam | Count every executed gradient setting for Adam as well as ordinary objective estimates and SPSA perturbations; freeze feasible caps during development and record actual usage. |

Select repetition counts after a small feasibility pilot on development observations, once implementations exist. Estimate whole-fit cost, including optimizer evaluations, circuit executions, final shots, and decoding. Reserve resources for fixed-observation runs, noise conditions, and any separate fixed-parameter diagnostics. No resampling allocation is required for the initial campaign. If that extension is later activated, $P$ separately fitted subset pairs require up to $2P$ full fits per method, parent dataset, and configuration, before any nested repetitions.

The matched comparison uses the same affordable observation sets and declared repetition policy for classical and quantum-assisted methods; identify any extra classical-only repetitions separately. Show individual values when very few runs are affordable and avoid strong uncertainty claims. Do not substitute fixed-observation variability for the deferred resampling-stability analysis. Declare any hard resource-stop rule before evaluation and retain failed or incomplete attempts in the accounting.

Budget choices are not statistical-power guarantees. Revise settings during development rather than adapting counts after inspecting final scores. A deterministic seed manifest would identify independent streams for subset draws, initialization, optimizer randomness, compilation, training shots, and final evaluation shots where supported. Equal seed integers across algorithms would not be interpreted as equivalent initial states.

Each eventual run record would identify dataset version and provenance, observation IDs, instance and repetition IDs, software versions, code revision, configuration, preprocessing fit, graph rules, cluster-count/balance constraints, seed assignments, execution mode, and termination reason. Quantum runs would also record circuit compilation settings and the bitstring-to-partition decoding rule, including infeasible outputs. Shared results would use the same run IDs in both course reports.

### Evaluation and Aggregation Procedure

For each reserved observation set, we propose the following sequence:

1. Construct the common observation set and freeze its evaluation representation and reference-label policy.
2. Run all methods under their declared configurations and resource limits. For QAOA, freeze the returned parameters, obtain fresh final shots, and apply the prespecified feasible bitstring-to-partition rule. Keep diagnosis labels and evaluation scores outside parameter/run selection and output decoding.
3. Record task feasibility and observed cluster sizes before scoring. Calculate each metric only when its own conditions hold, retaining a reason for every unavailable score.
4. Report Silhouette, external ARI against the diagnosis labels, and end-to-end runtime as separate main outputs. If suitable aligned reference labels are missing, record external ARI as unavailable with a reason. DBI and resampling-stability scores are not scheduled initially.
5. Repeat fixed-observation runs only as prescribed by the frozen budget. Keep any fixed-parameter noise/mitigation diagnostic separate from clustering quality and runtime.
6. Summarize quality and runtime within each observation set using individual values, median, interquartile range, attempted-run count, valid-score count, and failure count. Preserve individual outcomes and identify summaries conditional on validity.
7. Compare methods on the same observation sets and report overlap/source provenance. Do not pool shots, seed repetitions, or subsets of this one dataset as independent datasets or claim across-dataset superiority.

Invalid outputs, all-singleton partitions, and zero-distance degeneracies retain their previously stated flags. A numeric ARI on an invalid task output does not erase the task failure. Retain failed or excluded attempts in rate denominators and label quality summaries as conditional on valid scores. Report diagnosis/predicted group counts and sizes for small or imbalanced subsets, acknowledging limited evidence. The selected labels do not make ARI a classification-accuracy percentage or establish the only meaningful grouping. No universal combined score, significance claim, or ranking threshold is proposed.

### Quality and Resource Reporting

| Main output | Inputs and proposed use | Interpretation |
|---|---|---|
| Silhouette | Shared scaled original features and predicted labels on the common IDs | Internal geometry; higher is preferred. Its validity conditions still apply. |
| External ARI | Diagnosis labels and predicted labels on exactly the same IDs | Agreement beyond the stated chance baseline; higher is preferred. Label names need not match. |
| Runtime | End-to-end clustering wall time under the common boundary below | Efficiency interpreted alongside quality, failures, and actual resource usage. |

The proposed headline runtime starts with preprocessing the already selected feature table and stops when a partition or a recorded failure is returned. Include method-required graph construction, circuit compilation, classical fitting or quantum optimization and objective evaluations, final quantum sampling, and decoding. Exclude dataset download/loading, metric computation, plotting, and separate fixed-parameter noise/mitigation diagnostics. Report shared setup and optimization-only time as components; if setup is reused, include its recorded cost once in each method's end-to-end total where that setup is required and disclose the accounting. Use the same machine, simulator/backend, timing boundary, and declared threading settings for matched optimizer comparisons. Retain the excluded stages' times separately where relevant.

| Reporting category | Proposed fields | Interpretation |
|---|---|---|
| Quality and validity | Silhouette, external ARI, cluster sizes, reference provenance, and metric-validity reasons | Main scores are separate; diagnosis labels are evaluation-only. |
| Deferred analyses | DBI and resampling stability | Preserve their literature coverage; do not present deferral as observed quality or robustness. |
| Timing components | Main end-to-end runtime, shared setup, optimization-only time, and separately timed metric/diagnostic work | Use the declared boundary and disclose reused setup accounting. |
| Optimization effort | Restarts, iterations, charged objective estimates including derivative settings, gradient method, stopping condition, and actual budget | Count underlying circuit work rather than top-level optimizer/gradient calls; equal iteration counts do not establish matched quantum effort. |
| Quantum circuit resources | Qubits, compiled depth, two-qubit gate count, target/basis, circuit evaluations, and shots | Report both per-circuit quantities and totals where applicable. |
| Execution overhead | Compilation, simulator/device execution, decoding/postprocessing, and queue delay if present | Identify simulator and hardware modes; record queue delay separately. |

Report graph/QUBO objective values and supporting resource counts separately from the three main outputs. Objective improvement does not itself establish better reference agreement or geometry, and runtime alone does not establish matched quantum effort. Any post-optimization ZNE objective-estimation diagnostic in the QC proposal has separate shot/time accounting and does not define an additional decoded partition in this design. Its raw/mitigated objective errors are separate from Silhouette and ARI. Simulator timings are simulator costs, without a quantum-speedup claim.

### Shot Variability and Operation Noise

Finite-shot variability arises from sampling measurement outcomes, including in ideal simulation. PennyLane's measurement documentation distinguishes stochastic samples and counts from probability outputs and describes finite-shot simulation [[6]](ref.md#pennylane-measurements). Gate noise concerns imperfect operations, modeled using error channels in its noisy-circuit tutorial [[7]](ref.md#arrazola2021-noisy-circuits). Readout errors are a separate measurement-error condition; the mixed-state device documents a dedicated readout-error probability [[8]](ref.md#pennylane-default-mixed). From that distinction, increasing shots addresses sampling precision but does not generally remove an operation or readout error. These sources support the terminology without selecting an implementation library.

We propose the following conditions, subject to feasibility and the optimizer/noise review:

| Condition | Purpose | Proposed controls |
|---|---|---|
| Ideal finite-shot execution | Describe variation from finite measurements | Fixed compiled circuit and parameters; repeat sampling with recorded seeds and shot counts. |
| Gate-noisy finite-shot execution | Assess the declared gate-error model alongside sampling | Match circuit, parameters, decoding, and shot budget to the ideal condition; record error channels and probabilities. |
| Readout-error sensitivity, if included | Avoid attributing measurement-assignment errors to gate noise | Identify readout-only or combined conditions explicitly. |

A fixed-circuit diagnostic would keep parameters and the compiled gate sequence unchanged across conditions. Full optimizer reruns under each condition would be a separate end-to-end comparison, since they also change the optimization trajectory. Physical hardware observations would require backend/calibration timestamps and repeated measurements; a simulator seed would not be presented as control over hardware measurement randomness. Any exact-probability or exact-expectation diagnostic would be labeled separately from finite-shot results and would not be counted as a sampled run.

### Reconciliation and Current Limits

| Related review | Required agreement before final evaluation | Current treatment |
|---|---|---|
| Preprocessing and dimensionality reduction | Breast cancer dataset identity/coding, feature exclusions, scaling, fit population, missing-data policy, and PCA settings | Shared original-space Silhouette proposed; diagnosis labels excluded from input features and tuning; detailed preprocessing decisions remain pending. |
| Clustering and similarity graphs, together with QAOA formulation | Graph construction, cluster count, balance constraints, objective, and feasible output decoding | Compare the same declared task; do not assume every method supports unrestricted $k$ or unseen-point assignment. |
| Optimizers and noise | Main output list, common IDs, runtime boundary, objective/shot budgets, stopping rules, compilation target, execution modes, and diagnostic costs | Silhouette, external ARI, runtime, and deferred DBI/resampling scope aligned with the QC proposal; remaining numerical and execution choices are open. |

The main output list and runtime boundary are aligned with the QC optimizer/noise proposal. Dataset metadata, preprocessing, task/decoding conventions, feasible repetition counts, and execution settings still need final agreement. Different subsets of the breast cancer dataset provide limited within-dataset evidence, not independent-dataset validation. No algorithms, benchmarks, or project performance results are presented.
