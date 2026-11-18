# Clustering Evaluation and Experimental Design

## Silhouette as an Internal Evaluation Measure

Silhouette evaluates a partition from its dissimilarities without depending on the algorithm that produced it [[1]](ref.md#rousseeuw1987-silhouettes). This makes it a candidate for the proposed comparison of classical and quantum-assisted clustering.

For observation $i$, let $a(i)$ be its mean distance to the other members of its cluster, and let $b(i)$ be the smallest mean distance to any competing cluster. Its coefficient is

$$
s(i)=\frac{b(i)-a(i)}{\max\{a(i),b(i)\}}.
$$

Values near one indicate a strong fit, values near zero indicate ambiguity, and negative values favor a competing cluster on average. The overall score averages the observation-level coefficients [[2]](ref.md#scikit-learn-silhouette-score).

The plot groups individual coefficients by cluster; its widths reveal weak assignments, while its heights indicate cluster sizes. Comparing overall averages across candidate cluster counts can inform the choice of $k$ [[1]](ref.md#rousseeuw1987-silhouettes).

### Silhouette Assumptions and Interpretation

Rousseeuw's construction assumes ratio-scale dissimilarities and favors compact, separated clusters. Singletons receive zero by convention. A distant-outlier example demonstrates why a high mean alone needs scrutiny [[1]](ref.md#rousseeuw1987-silhouettes). For this project, we therefore propose inspecting curved-cluster cases separately and avoiding a universal pass/fail threshold.

The documented scikit-learn validity condition is $2\leq k\leq n-1$, where $k$ is the number of observed clusters and $n$ the number of evaluated observations [[2]](ref.md#scikit-learn-silhouette-score). We propose reporting invalid partitions as unavailable scores with explicit reasons and failure counts. A partition containing a singleton should be distinguished from an entirely singleton partition.

## Davies–Bouldin as a Complementary Internal Measure

Davies and Bouldin compare within-cluster dispersion with separation between representative centers. Their index averages each cluster's largest similarity ratio, so a lower value indicates better separation relative to spread. The formulation permits different choices of distance and dispersion [[3]](ref.md#davies1979-cluster-separation).

For this project, we propose fixing Euclidean centroid separation and mean Euclidean distance to the centroid, corresponding to the paper's choices $p=2$ and $q=1$. For a nonempty cluster $C_i$ of size $n_i$, define

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

We propose recording empty-cluster failures, observed cluster counts, and singleton counts explicitly. For coincident centroids, DBI should be reported as unavailable with a reason rather than interpreted as perfect quality. A singleton within an otherwise admissible partition should be flagged, not automatically equated with an entirely singleton partition. We propose excluding the latter from quality rankings. Handling numerically near-coincident centers remains an open protocol choice.

### Joint Interpretation of Internal Measures

Silhouette and DBI both assess within-group cohesion relative to between-group separation, but they use different summaries [[1]](ref.md#rousseeuw1987-silhouettes), [[3]](ref.md#davies1979-cluster-separation).

| Aspect | Silhouette | Proposed Euclidean DBI |
|---|---|---|
| Preferred direction | Higher | Lower |
| Distance summary | Observation-to-cluster average dissimilarities | Dispersion around centroids and distances between centroids |
| Overall weighting | Equal weight per observation | Equal weight per cluster |
| Diagnostic detail | Individual coefficients and cluster plots | Most similar competitor for each cluster |

These differences follow from their constructions [[1]](ref.md#rousseeuw1987-silhouettes), [[3]](ref.md#davies1979-cluster-separation). We therefore propose reporting both scores rather than combining them into one ranking. As an implication of the chosen geometry, curved or irregular groups should receive separate diagnostic discussion; disagreement between the measures should prompt inspection of cluster shape, size, and assignments. The reviewed papers do not establish either score as sufficient evidence for quantum advantage or for recovery of externally labeled classes.

## Adjusted Rand Index as an Agreement Measure

Hubert and Arabie compare partitions of the same observation set through pairwise membership relationships. A pair agrees when its observations are placed together in both partitions or apart in both; the ordinary Rand Index (RI) is the fraction of agreeing pairs. Their chance correction accounts for agreement expected under a generalized hypergeometric model that preserves the cluster sizes of each partition [[4]](ref.md#hubert1985-comparing-partitions).

For a nondegenerate comparison, the Adjusted Rand Index can be written as

$$
\operatorname{ARI}=\frac{RI-\mathbb{E}[RI]}{1-\mathbb{E}[RI]}.
$$

A value of one indicates identical group membership, zero indicates agreement equal to the chance baseline, and negative values indicate agreement below that baseline. Zero is the expectation under the stated random-partition model, not a score that every random realization must attain. The paper derives the expectation and corrected index in Section 2.1 [[4]](ref.md#hubert1985-comparing-partitions).

Because this calculation depends on membership relationships rather than group names, renaming cluster identifiers leaves ARI unchanged. The grouping $(0,0,1,1)$ therefore matches $(7,7,2,2)$ perfectly when the observation order is the same. This is a mathematical illustration, not a project result. Different cluster counts are permitted by the comparison framework, although splitting or merging groups changes the membership relationships being assessed [[4]](ref.md#hubert1985-comparing-partitions).

### Reference Labels, Validity, and Interpretation

For external evaluation, we propose comparing each output partition with reference labels on exactly the same observation IDs. Those labels would be reserved for evaluation and would not select scaling, PCA dimensions, graph parameters, optimizer settings, or the preferred stochastic run. Where reference labels are unavailable, external ARI would be reported as unavailable rather than inferred from an internal score.

ARI can also compare two algorithm outputs without treating either as ground truth, since the underlying formulation compares two partitions symmetrically [[4]](ref.md#hubert1985-comparing-partitions). We propose labeling such comparisons as between-run agreement and restricting them to shared observation IDs. The resampling protocol below develops this use separately from external agreement with reference labels.

Metric validity should be recorded separately. A one-cluster output can have a defined ARI against a nontrivial reference even when its Silhouette is undefined; this follows from the respective formulas [[2]](ref.md#scikit-learn-silhouette-score), [[4]](ref.md#hubert1985-comparing-partitions). We propose keeping the partition's failure or degeneracy flag alongside any defined metric. Comparisons with fewer than two observations, or a zero denominator in the displayed ARI expression, require an explicit convention before implementation; no convention is assumed here.

The chance baseline depends on cluster sizes [[4]](ref.md#hubert1985-comparing-partitions). Accordingly, we propose reporting reference and predicted cluster counts and sizes with ARI, particularly for small subsets. An ARI value is not a percentage of correctly labeled observations, a significance test, or evidence that the reference classes are the only meaningful grouping. Reference-label suitability and the effect of imbalanced groups remain questions for the evaluation design.

### Complementarity with Internal Quality

| Measure | Evaluation question | Preferred direction | Inputs beyond the output partition |
|---|---|---|---|
| Silhouette | How well do observations fit their clusters relative to competitors? | Higher | A declared dissimilarity representation |
| Proposed Euclidean DBI | How small is centroid-based dispersion relative to separation? | Lower | A shared feature representation |
| External ARI | How closely does membership agree with a reference beyond chance? | Higher | A reference partition on the same observations |

These distinctions follow from the three constructions [[1]](ref.md#rousseeuw1987-silhouettes), [[3]](ref.md#davies1979-cluster-separation), [[4]](ref.md#hubert1985-comparing-partitions). We propose reporting the measures separately. In particular, the published DBI Iris example motivates examining geometric quality and reference agreement together; it does not establish that the shared project will reproduce that example or any particular ARI value.

## Stability Under Resampling

Ben-Hur, Elisseeff, and Guyon evaluate stability through the distribution of similarities between partitions obtained from perturbed datasets. Their model explorer repeatedly draws two subsamples, clusters each separately, and compares the labels of observations common to both. Inspecting the distribution, rather than only its mean, can reveal variability or competing solutions concealed by an average [[5]](ref.md#benhur2002-stability).

The paper relates cluster-count selection to a transition from similarities concentrated near one to a broader distribution. Its experiments use average-link hierarchical clustering, with structured synthetic data, selected yeast microarray data, and uniformly random data. These illustrate a diagnostic heuristic rather than a general recovery guarantee. The experimental similarity is a co-membership correlation/cosine measure associated with Fowlkes–Mallows; Jaccard and matching measures are also discussed. It is not an ARI-based experiment [[5]](ref.md#benhur2002-stability).

### Proposed Separation of Variability Sources

For the shared project, we propose distinguishing two questions:

| Stability question | What changes | What is held fixed |
|---|---|---|
| Initialization or run stability | Algorithm initialization or optimizer random seed | Observation IDs, feature representation, graph rules, and declared execution conditions |
| Sampling stability | Observation subset, followed by reclustering | Parent dataset, sampling rule, preprocessing policy, algorithm settings, and declared execution conditions |

For sampling stability, we propose drawing paired subsets without replacement within each subset, using the same subset pairs for every method. Let $S_r$ and $T_r$ be the observation ID sets for pair $r$, and let $I_r=S_r\cap T_r$. Each partition is fitted using its whole subset. Agreement is then calculated only on the intersection. With $\ell_{S_r}(I_r)$ denoting the labels of those shared observations, a proposed score is

$$
A_r=\operatorname{ARI}\bigl(\ell_{S_r}(I_r),\ell_{T_r}(I_r)\bigr).
$$

This combines the paper's resampling design with the ARI formulation; replacing its reported similarity measure with ARI is a project proposal, not a replication of its experiments [[4]](ref.md#hubert1985-comparing-partitions), [[5]](ref.md#benhur2002-stability). We propose recording the parent instance, both subsets, their intersection, sampling seeds, algorithm seeds, and full-subset and intersection cluster counts. Working sampling and repetition settings are specified below for feasibility review.

To separate the two questions initially, we propose fixing algorithm seeds during a sampling comparison and varying those seeds separately on fixed observations. An eventual nested design could examine their interaction, subject to resource limits. Quantum shot variability and gate-noise conditions should remain separately identified in the optimizer/noise protocol; the resampling paper does not evaluate quantum execution.

### Interpretation and Limits

The paper explicitly identifies the one-group solution as trivially stable and warns that sampling too little can omit clusters. Its hierarchical experiments also use a cluster-size heuristic to handle outliers. The authors acknowledge that the example datasets have recognizable structure and that repeated clustering adds computational cost [[5]](ref.md#benhur2002-stability). We therefore propose checking trivial partitions, cluster sizes, and overlap sizes alongside agreement. Empty or insufficient overlaps and undefined scores should be counted with their reasons; they should not silently disappear from the summary. The paper's outlier heuristic is not automatically adopted for QAOA or the classical baselines.

We propose reporting the empirical agreement distribution, its median and interquartile range, and valid/excluded comparison counts. These summaries describe variation under the declared perturbation; they are not automatically confidence intervals for performance on new datasets. Repeated subsets of one parent dataset must not be presented as additional independent evaluation instances.

Stability would supplement internal geometry and external agreement. A consistently produced partition is not, by that fact alone, suitable for the intended task. We propose interpreting the measures together, reserving reference labels for external evaluation and choosing any stability-based settings on development instances only. No universal ARI stability threshold or automatic cluster-count rule is adopted here.

## Integrated Evaluation Proposal

The following protocol is a proposed initial design for comparing classical baselines with a small QAOA-assisted clustering demonstration. It does not assume quantum PCA or simulated annealing has been implemented. Its numerical settings are working choices for feasibility review, not literature-derived guarantees, teacher requirements, or completed experiments.

### Instances, Development, and Common Observations

We propose reserving separate development and evaluation instances before selecting settings. Development would establish feasible sample sizes, preprocessing rules, graph parameters, optimizer budgets, and stopping conditions. These decisions would then be frozen for evaluation. For synthetic studies, an evaluation instance would be an independently generated dataset with a recorded generator configuration and seed. For real-data studies, dataset provenance and any shared population would be disclosed. Different subsets of one dataset would remain within-dataset comparisons; disjointness alone would not establish independence.

Within an instance, every method would receive the same observation IDs. If quantum resources limit the sample size, classical comparisons would include that same subset. Larger classical-only runs could be supplementary but would not substitute for a matched comparison. We propose choosing subsets without consulting external labels and retaining draw seeds and IDs. Planned diagnostic conditions would include compact groups, curved groups, unequal group sizes, and a no-designed-cluster case, subject to dataset availability and agreed scope. These are proposed conditions, not generated datasets or results.

Headline Silhouette and Euclidean DBI would use one shared scaled original-feature representation for each instance. Method-specific PCA or graph-embedding scores would be supplementary. The scaling method and its fitting population must be declared: a deployment-style study would fit preprocessing on development data, whereas a transductive study may apply a prespecified label-free fitting rule within each instance. The two designs should not be mixed without identification. For initial algorithm-only resampling stability, we propose freezing the parent instance's scale transform; refitting the whole preprocessing pipeline would be a separately labeled sensitivity analysis.

The official Silhouette interface permits random metric subsampling [[2]](ref.md#scikit-learn-silhouette-score). We propose scoring all observations in the small common instances initially. Any later metric-only subsampling would use recorded common scoring IDs and a renewed validity check.

### Repetitions, Budgets, and Reproducibility

| Control | Provisional starting proposal | Decision before final evaluation |
|---|---|---|
| Fixed-observation stochastic runs | 10 independently initialized runs per method and instance | Confirm affordability on development instances; freeze the count and report every attempted run. Deterministic configurations need one identified run, not artificial repetitions. |
| Sampling stability | 20 independently drawn subset pairs per parent instance, shared across methods | Freeze the pair list and retain exclusions; these are not 20 independent datasets. |
| Subset size | Sample $m=\lceil0.8n\rceil$ observations without replacement within each subset | Require $m<n$ so data actually change; if infeasible, record sampling stability as unavailable for that instance. |
| Overlap screen | At least 4 shared observations, with trivial intersections flagged separately | This is a provisional screen, not a reliability guarantee. Reconcile it with sample size and cluster constraints; record all insufficient or undefined comparisons. |
| Optimizer and shot budgets | Declare objective-evaluation limits, stopping rules, shots per circuit, and total shots before evaluation | Select feasible values on development instances with the optimizer/noise review; record actual usage and early stopping. |

These working values prioritize a reviewable starting design. They do not establish statistical power or precise population uncertainty. If feasibility requires changes, revise and record them during development rather than adapting the budget after inspecting final scores. A deterministic seed manifest would identify independent streams for instance generation, subset draws, initialization, optimizer randomness, transpilation, and simulator sampling where supported. Equal seed integers across algorithms would not be interpreted as equivalent initial states.

Each eventual run record would identify dataset version and provenance, observation IDs, instance and repetition IDs, software versions, code revision, configuration, preprocessing fit, graph rules, cluster-count/balance constraints, seed assignments, execution mode, and termination reason. Quantum runs would also record circuit compilation settings and the bitstring-to-partition decoding rule, including infeasible outputs. Shared results would use the same run IDs in both course reports.

### Evaluation and Aggregation Procedure

For each reserved instance, we propose the following sequence:

1. Construct the common observation set and freeze its evaluation representation and reference-label policy.
2. Run all methods under their declared configurations and resource limits. Keep external labels outside parameter selection and output decoding.
3. Record task feasibility and observed cluster sizes before scoring. Calculate each metric only when its own conditions hold, retaining a reason for every unavailable score.
4. Report Silhouette, DBI, and external ARI separately. Where labels are absent, omit external ARI while retaining the internal and stability evidence.
5. Run the fixed-observation and paired-subset stability comparisons separately, aligning shared IDs before calculating between-run ARI.
6. Summarize stochastic quality within each instance using the median, interquartile range, attempted-run count, valid-score count, and failure count. Report distributions where feasible and preserve individual values.
7. Compare methods on paired instances and observation sets. Summarize across independently generated instances at the instance level; do not pool shots, seed repetitions, or overlapping subset pairs as independent datasets.

Invalid outputs, all-singleton partitions, zero-distance degeneracies, and trivial intersections would retain their previously stated flags. A numeric ARI on an invalid task output would not erase the task failure. We propose retaining failed or excluded attempts in rate denominators and labeling quality summaries as conditional on valid scores. Small or imbalanced subsets would include counts and size diagnostics, with limited evidence explicitly acknowledged. No universal combined score, significance claim, or ranking threshold is proposed.

### Quality and Resource Reporting

| Reporting category | Proposed fields | Interpretation |
|---|---|---|
| Internal quality | Silhouette, DBI, cluster sizes, and metric-validity reasons | Shared scaled original space; identify supplementary representations. |
| External agreement | ARI, reference provenance, and reference/predicted group counts | Evaluation labels only; unavailable if no suitable reference exists. |
| Stability | Perturbation type, agreement distribution, overlap sizes, and excluded-pair counts | State what changed and what remained fixed. |
| Classical and end-to-end cost | Wall time, machine/thread information, preprocessing, graph construction, optimization, and scoring time | Define timing boundaries; identify shared preprocessing charged once and per-method costs separately. |
| Optimization effort | Restarts, iterations, objective evaluations, stopping condition, and actual budget | Equal evaluation counts are meaningful only where the evaluated work is comparable; disclose each method's policy. |
| Quantum circuit resources | Qubits, compiled depth, two-qubit gate count, target/basis, circuit evaluations, and shots | Report both per-circuit quantities and totals where applicable. |
| Execution overhead | Compilation, simulator/device execution, decoding/postprocessing, and queue delay if present | Identify simulator and hardware modes; record queue delay separately. |

We propose reporting graph/QUBO objective values separately from the clustering metrics. Objective improvement would not itself establish better reference agreement or geometry. Resource quantities would accompany quality rather than be collapsed into an unsupported universal score. Simulator timings would be described as simulator costs, without a quantum-speedup claim.

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
| Preprocessing and dimensionality reduction | Scaling, fit population, missing-data policy, PCA settings, and stability refitting policy | Shared original-space evaluation proposed; detailed preprocessing decisions remain pending. |
| Clustering and similarity graphs, together with QAOA formulation | Graph construction, cluster count, balance constraints, objective, and feasible output decoding | Compare the same declared task; do not assume every method supports unrestricted $k$ or unseen-point assignment. |
| Optimizers and noise | Objective/shot budgets, stopping rules, compilation target, execution modes, and error models | Keep sampling, optimization, gate noise, and readout conditions separately identified. |

The corresponding review texts are not yet available for reconciliation in the current checkout. These dependencies are therefore explicit conditions on the proposal, not agreements or completed lessons. The initial evaluation design is documented here; final synthesis remains conditional on those decisions. No algorithms, benchmarks, or project performance results are presented.
