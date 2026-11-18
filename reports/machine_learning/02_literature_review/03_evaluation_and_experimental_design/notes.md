# Research Notes: Evaluation and Experimental Design

## Evidence Coverage

The Silhouette and Davies–Bouldin papers have been read in full, including their examples and discussions. The downloaded DBI paper was checked visually where its scanned equations and text extraction needed confirmation. For Hubert and Arabie, the introduction and Sections 2–2.2 (printed pp. 193–201) were read for the ARI synthesis; later extensions were skimmed and are not treated as fully reviewed evidence. The scanned correction formula and worked example were checked visually. Ben-Hur et al.'s complete 12-page text, including the model explorer procedure, experimental discussion, figure captions, and conclusion, has been read. The official `silhouette_score` reference has been read for its definition, validity condition, and sampling parameters. The comparison below distinguishes primary research from software documentation. Verified access details and bibliographic records are in [ref.md](ref.md).

This evidence supports a comparison of Silhouette, DBI, ARI, and resampling-based stability, with an integrated evaluation proposal. Relevant PennyLane documentation and its noisy-circuit tutorial have also been read to support the shot/noise distinction. Sampling/repetition settings are provisional starting choices; reconciliation with the preprocessing, clustering, and optimizer/noise reviews remains pending. Candidate publications for remaining questions are not treated as read or cited evidence here.

## Source Comparison

| Source | Method or scope | Assumptions | Evidence and location | Limitations | Project relevance |
|---|---|---|---|---|---|
| [Rousseeuw, 1987](ref.md#rousseeuw1987-silhouettes), primary research | Silhouette | Ratio-scale dissimilarities; compact groups | Construction, Sec. 2; country and Ruspini examples, Secs. 1–3; outlier, pp. 60–61 | Illustrative evidence; no project benchmark | Candidate geometric diagnostic |
| [scikit-learn, `silhouette_score`](ref.md#scikit-learn-silhouette-score), official documentation | Mean score and optional random subsampling | Valid cluster-count range; selected distance metric | Opening definition; Parameters: `metric`, `sample_size`, `random_state` | API contract rather than independent empirical validation; mutable documentation | Specify validity and reproducible metric sampling |
| [Davies and Bouldin, 1979](ref.md#davies1979-cluster-separation), primary research | Average of maximum pairwise cluster-similarity ratios | Representative-centered dispersion; nonzero inter-center distances; distance/dispersion choices declared | Definitions 4–5 and degeneracies, p. 225; ISODATA and hierarchy, p. 226; Iris, p. 227 | Zero-dispersion singletons can favor overpartitioning; geometric and class partitions can differ; demonstrations do not establish universal accuracy | Complement Silhouette; fix $p=2,q=1$ for the proposed comparison and record degeneracies |
| [Hubert and Arabie, 1985](ref.md#hubert1985-comparing-partitions), primary research; ARI sections reviewed | Pairwise partition agreement corrected for chance | Same observation set; disjoint exhaustive groups; fixed cluster sizes under the stated null model | Pair types, pp. 194–196; expectation and correction, pp. 197–199; alternative correction and worked example, pp. 200–201 | Chance correction is model-dependent; does not establish semantic validity of labels or quantify uncertainty for the project | External agreement without label matching; proposed between-run agreement measure |
| [Ben-Hur et al., 2002](ref.md#benhur2002-stability), primary research | Distribution of co-membership similarities across clustered subsample pairs | Sufficient representation in subsamples; compare shared observations; chosen clustering and perturbation procedure | Model explorer and Remark 3.1, Sec. 3, PDF pp. 4–5; experiments, pp. 5–10; discussion, pp. 10–11 | Trivial one-group stability; illustrative datasets; algorithm and sampling choices matter; repeated-run cost; reported score is not ARI | Support paired-subset stability diagnostics, with an explicitly proposed ARI substitution |
| [PennyLane, Measurements](ref.md#pennylane-measurements), official documentation | Finite measurement samples and counts | Declared shots | Measurement definitions, Counts, and Changing the number of shots | API guide; no clustering benchmark | Support finite-shot terminology |
| [Arrazola, Noisy circuits](ref.md#arrazola2021-noisy-circuits), official tutorial | Imperfect operations and channels | Explicit error model | Noisy operations section | Illustrative noise models | Support gate-error terminology |
| [PennyLane, DefaultMixed](ref.md#pennylane-default-mixed), official documentation | Mixed-state simulation and readout error | Declared readout probability | Class Parameters | Device-specific model | Keep readout conditions separate |

## Reading Map

- Primary paper: printed pages 53–65 correspond to PDF pages 1–13. Definition: pp. 55–56; display and aggregation: pp. 57–59; examples: pp. 60–63; related displays and alternatives: pp. 62–64.
- The copy-ready definition, equation, and interpretation appear in [content.md](content.md). Keep literature claims linked to their source instead of treating the proposed protocol as a published finding.
- Official documentation: distinguish sampling for metric calculation from selecting the clustering instance itself. Record both sets of observation IDs if both operations are used.
- DBI paper: printed pp. 224–227 correspond to PDF pp. 1–4. Its article begins below references from the preceding article on p. 224. Definitions 4–5, distance/dispersion choices, and singleton limitations are on p. 225; examples and conclusion occupy pp. 225–227. The authors denote the overall index by $\overline{R}$; `content.md` uses DBI and $k$ for the number of clusters to avoid confusion with sample count.
- ARI paper: printed pp. 193–218 correspond to PDF pp. 1–26. Section 2 starts on p. 194; Section 2.1 on p. 197; Eqs. (3)–(5) are on p. 198; Section 2.2 and Table 3 are on pp. 200–201. The later matrix and triple-based generalizations are distinct from the ARI definition and are outside this synthesis.
- Stability paper: use PDF page numbers for precise locations. Similarity measures: Sec. 2, pp. 2–4; model explorer: Sec. 3 and Fig. 2, pp. 4–5; distributions and experiments: Sec. 4, Figs. 3–7 and Table 1, pp. 5–10; discussion and conclusion: Secs. 5–6, pp. 10–11. The official proceedings identify the publication as volume 7, pp. 6–17 (2002).

## DBI Evidence and Comparison Notes

| Reading point | What the paper establishes or reports | Limitation or interpretation for this review |
|---|---|---|
| Abstract and theoretical formulation, p. 224 | The proposed summary assumes density decreases with distance from a representative vector; distance and dispersion properties are specified | This motivates caution about using a center-based diagnostic for arbitrary cluster shapes. Curved-cluster implications in the synthesis are our interpretation, not a reported curved-cluster experiment. |
| Definitions 4–5 and specialization, p. 225 | $R_{ij}=(S_i+S_j)/M_{ij}$; average the maximum ratio for each cluster and minimize it. The original framework permits different $p$ and $q$. | Proposed $p=2,q=1$ fixes one variant. Do not silently mix it with a root-mean-square dispersion variant or describe every experiment in the paper as using $q=1$. |
| Four-point example, Fig. 1, p. 225 | With $p=q=2$, the two partitions receive 2.0 and 0.5; the lower score favors the partition with smaller spread relative to separation | This illustrates the definition; it is neither a project benchmark nor proof that the procedure finds a global optimum. |
| Degenerate partitions, p. 225 | At least two groups and nonzero denominator distances are needed. Singleton dispersion is zero; all distinct observations as separate clusters yield zero. | The demonstration requires clusters of at least two members and assigns large dispersion to singleton clusters during its merge procedure (pp. 225–226). That heuristic is not the ordinary formula and is not automatically adopted for this project. |
| ISODATA example, Fig. 2, p. 226 | The adapted 225-point dataset originated from nine approximately Gaussian parent distributions; the reported index minimum is at eight groups | An internal geometric optimum need not reproduce the generating group count. The paper notes minor discrepancies from digitizing the data. |
| Hierarchical example, Fig. 3, p. 226 | The 130-point example has approximately equal local minima at 3, 6, 10, and 13 groups | Different grouping scales may be defensible. Report candidate-count settings and avoid claiming that every dataset has one unambiguous geometric optimum. |
| Iris, Fig. 5 and discussion, p. 227 | The procedure selects two groups for data covering three species; the paper notes overlap between Virginica and Versicolor | Supports distinguishing internal quality from external agreement. The ARI review below addresses partition agreement; no ARI for this published DBI experiment has been calculated here. |

Silhouette averages observation-level coefficients, whereas DBI averages one maximum ratio per cluster. Thus a small cluster has a different relative contribution under the two summaries; this follows from the equations, not from a measured project result. Neither metric should be presented as an objective-independent guarantee of clustering quality.

The primary DBI paper is retained because it directly exposes dispersion variants, singleton handling, coincident-center limitations, and the distinction between class counts and geometric optima. This fills evidence gaps beyond an API definition. Partition agreement is supported separately by Hubert and Arabie; resampling stability is supported by Ben-Hur et al. below.

## ARI Evidence and Comparison Notes

| Reading point | What the paper establishes | Limitation or implication for this review |
|---|---|---|
| Section 2, pp. 194–196 | Four pair types distinguish agreement and disagreement between two partitions of the same objects; overlap counts form a contingency table | Observation IDs must align. Permuting group names only reorders table rows or columns, leaving the pair counts unchanged. |
| Section 2.1, pp. 197–198 | Under the generalized hypergeometric model, cluster sizes are fixed; the expected pair counts determine the adjustment | State the model. Zero is a baseline in expectation, not proof that a particular output was generated randomly. |
| Eqs. (4)–(5), p. 198 | Subtract the expected index and normalize its distance from the upper bound; the corrected Rand form permits negative values | Interpret negative values as below-baseline agreement. Do not report ARI as a percentage accuracy, probability, or p-value. |
| Discussion, pp. 199–200 | Other normalizations and random-partition models are possible | ARI does not remove every choice or bias from evaluation; its fixed-size chance model is a declared assumption. |
| Commentary and Table 3, pp. 200–201 | A previously used correction relies on an incorrect expectation identity. For the six-object example, raw RI is $9/15$; their corrected result is $2/17$, compared with $1/3$ under the criticized correction. | This is a mathematical worked example from the paper, not a project experiment or a benchmark showing an algorithm is superior. |

### Contingency-Table Form

For reference groups $U_i$ and output groups $V_j$, let $n_{ij}$ count shared members, with row sums $a_i$ and column sums $b_j$. Define

$$
T=\binom{n}{2},\qquad
P_U=\sum_i\binom{a_i}{2},\qquad
P_V=\sum_j\binom{b_j}{2},\qquad
P_{UV}=\sum_{i,j}\binom{n_{ij}}{2}.
$$

Equation (5) can then be written, when its denominator is nonzero, as

$$
\operatorname{ARI}=
\frac{P_{UV}-P_UP_V/T}
{\tfrac12(P_U+P_V)-P_UP_V/T}.
$$

This is a change of notation, not a new result. It makes label-permutation invariance and symmetry apparent. At $n<2$, $T$ is zero; identical trivial partitions can also give a zero denominator. The eventual software convention for those cases needs verification rather than an assumed formula value.

### Illustrative Pair-Counting Check

The following four-object examples were constructed for explanation and checked arithmetically from the displayed formula. They are not observations from a dataset, published benchmark scores, or implementation tests.

| Reference labels | Compared labels | Raw RI | ARI | Interpretation |
|---|---|---|---|---|
| $(0,0,1,1)$ | $(7,7,2,2)$ | $1$ | $1$ | Same grouping under different names |
| $(0,0,1,1)$ | $(7,2,7,2)$ | $1/3$ | $-1/2$ | Agreement below the fixed-size baseline |
| $(0,0,1,1)$ | $(7,7,7,7)$ | $1/3$ | $0$ | ARI is defined although Silhouette is invalid for the output |

For the mixed two-group example, $T=6$, $P_U=P_V=2$, and $P_{UV}=0$. Thus ARI is $(-2/3)/(4/3)=-1/2$. The one-group example illustrates why mathematical metric validity and success at the intended clustering task must be reported separately.

For small or imbalanced instances, we propose reporting group counts and sizes rather than treating chance adjustment as a guarantee of reliability. Pair counts depend on group sizes through binomial coefficients; the practical impact and uncertainty for the chosen datasets remain unresolved. External reference labels must be reserved for evaluation. Between-run ARI would describe agreement, not establish correctness or stability under untested perturbations.

## Stability Evidence and Comparison Notes

| Reading point | What the paper establishes or reports | Limitation or project interpretation |
|---|---|---|
| Introduction, PDF pp. 1–2 | The authors motivate perturbation-based validation and argue that the distribution of similarities can reveal information lost in an average | A single mean stability score should not be the only diagnostic. This is a motivation for empirical distribution reporting, not a calibrated confidence interval. |
| Section 2, PDF pp. 2–4 | Co-membership matrices define a dot product and correlation/cosine similarity associated with Fowlkes–Mallows; matching and Jaccard measures are discussed | The reported experiments use the correlation score, not ARI. The source's near-one behavior and any suggested cutoff must not be transferred directly to ARI without justification. |
| Section 3 and Fig. 2, PDF pp. 4–5 | Repeatedly cluster two subsamples and compare their labels on their intersection, for candidate cluster counts | Fit partitions on full subsamples before restricting labels. Reuse the same subset pairs across project methods, and track overlap counts and IDs. |
| Remark 3.1, PDF p. 5 | One cluster is trivially stable; a sampling fraction that is too low may omit groups | High agreement alone does not establish useful structure. Minimum overlap and group-coverage diagnostics need explicit rules. |
| Section 4 and Fig. 3, PDF pp. 5–6 | Average-link hierarchical clustering is used; in the four-Gaussian example, scores are concentrated near one through the four-group partition and broaden when further splits are imposed | The transition is an illustrated heuristic, not a theorem or evidence about QAOA. The paper notes that symmetric arrangements can behave differently at smaller cluster counts. |
| Outlier heuristic, PDF p. 6 | The hierarchy is cut to obtain a prescribed number of sufficiently large clusters; extra singleton or small clusters may remain | The parameter need not equal the total cluster count. This hierarchy-specific rule is not automatically appropriate for the shared project. |
| Yeast experiment, PDF pp. 6–8 | Selected functional classes and PCA produce persistent major groups, with overlapping annotations and a singleton affecting the total count | Class selection makes this an illustrative example. Its reported class-comparison similarity is not an ARI score. This does not authorize using evaluation labels to choose project features or samples. |
| Uniform-data example, Fig. 7 and Table 1, PDF pp. 9–10 | Their random-data example lacks a clear transition like those in structured examples | This supports testing for absence of robust structure in that setting, not universal detection of all unstructured datasets. |
| Discussion and conclusion, PDF pp. 10–11 | Repeated clustering adds cost; examples were chosen with recognizable structure; alternative perturbation/comparison designs are discussed | The proposed project design must state its algorithm, preprocessing, subset rule, and resource budget. Stability under one perturbation does not establish stability under every other perturbation. |

### Proposed Paired-Subset Design

- Start with a parent instance that every method can process. Draw pairs of subsets with a declared fraction and sampling seed, without replacement within each subset. Subset pairs are shared across methods and selected without external labels.
- Cluster each whole subset, then align and compare labels only on the intersecting observation IDs. Using ARI is our adaptation, supported by the ARI formulation; it is not the original paper's experimental similarity measure.
- Vary initialization/optimizer seeds separately on fixed observations. Initially fix those seeds for sampling comparisons; any later nested design must identify the interaction being studied and its budget.
- Record full-subset sizes, intersection size, predicted group counts before and after restriction, singleton counts, and invalid or excluded comparison reasons. Define the minimum informative overlap before examining final results.
- Show empirical agreement distributions and propose median/interquartile summaries. Do not interpret shared-parent repetitions as independent datasets or treat empirical quantiles as population-performance confidence intervals.
- Keep quantum execution conditions separate from data resampling. The optimizer/noise review must define finite-shot and gate/readout-noise comparisons; Ben-Hur et al. provide no quantum execution evidence.

### Illustrative Distribution Check

These are constructed similarity values, not published scores or project results. The lists $(0.89,0.90,0.91)$ and $(0.70,1.00,1.00)$ both average to $0.90$, yet have different spreads. They illustrate the reason to inspect more than a mean; they do not define a stability threshold or simulate the project.

## Integrated Protocol Decisions

The expanded protocol in `content.md` is a project proposal rather than a benchmark result or a teacher-prescribed design. Working values are 10 initialization runs, 20 subset pairs, a subset fraction of 0.8 with upward rounding, and an overlap screen of 4 observations. These values make the proposal concrete for feasibility review; they do not imply statistical power or reliable inference on tiny intersections. The overlap screen is additional to undefined-score and trivial-partition checks. At sample sizes where rounding yields the whole dataset, record sampling stability as unavailable rather than reporting unchanged samples as evidence of robustness.

| Design decision | Proposed treatment | Evidence boundary or unresolved dependency |
|---|---|---|
| Budget selection | Assess affordability on development instances, document revisions, then freeze counts, objective limits, and shots before evaluation | No feasibility pilot has been run. Method-specific objective and shot limits need the optimizer/noise review. |
| Independent evaluation | Use separately generated synthetic datasets or a disclosed real-data instance roster | Different draws or disjoint subsets of one dataset do not automatically establish independence. |
| Reproducibility | Retain data/configuration versions, observation IDs, code revision, seed manifest, software versions, decoding rules, and termination reasons | No implementation or software version is selected by this proposal. |
| Shared evaluation geometry | Use the same scaled original features for internal scores, with a declared fitting population | Confirm transductive versus development-fitted preprocessing; keep representation-specific results supplementary. |
| Sampling stability | Freeze the parent scale transform for the initial algorithm-only analysis; identify any refitted pipeline analysis separately | Reconcile this choice with the preprocessing review. |
| Failures and aggregation | Report all attempted runs, validity reasons, failure rates, and conditional-on-valid quality distributions | Do not conceal failures by summarizing only successful runs or treating all metrics as sharing one validity rule. |
| Quantum conditions | Separate ideal finite-shot, declared gate-noisy finite-shot, and optional readout conditions | The source documentation defines mechanisms; the experiment matrix is our proposal. |
| Fixed circuit versus full optimizer | Hold compiled circuit and parameters fixed for a sampling/noise diagnostic; label optimizer reruns separately | Full reruns measure the combined optimization and execution behavior. |
| Resource accounting | Separate quality, wall-time stages, optimizer effort, circuit resources, total shots, and optional queue delay | Budgets across unrelated objectives cannot be assumed equivalent merely from evaluation counts. |

The finite-shot explanation is supported by the measurement guide; operation noise is supported by the noisy-circuit tutorial, and the device reference identifies a separate readout parameter. The statement that additional samples do not generally remove such errors is an inference from separating sampling precision from those error models. We do not adopt an example error probability as a project parameter or select a software library through these citations.

## Proposed Choices and Their Rationale

These are project proposals, subject to revision as the remaining literature is compared.

| Proposal | Rationale for the comparison | Knowledge still needed |
|---|---|---|
| Common subsets for all methods | Keep the observations being compared fixed within an instance | Feasible quantum instance size and subset-selection policy |
| Shared scaled original-space internal scores | Establish one declared evaluation geometry across representation choices | Scaling method, fitted population, and agreement with preprocessing review |
| Full-instance Silhouette initially | Avoid adding metric subsampling to already small instances | Feasible evaluation cost and minimum informative cluster sizes |
| Euclidean DBI with mean centroid distance for dispersion | Keep the same variant for every method, alongside Silhouette | Confirm feature scaling, numerical tolerances, and any differences from the eventual library implementation |
| External ARI on matched observation IDs | Assess reference agreement without manually matching arbitrary cluster names | Reference-label suitability, label availability, and exceptional-case conventions |
| Paired-subset stability using ARI | Apply a common resampling design while retaining chance-adjusted partition agreement | Confirm affordability and informativeness of the provisional fraction, pair count, overlap screen, and triviality rules |
| Separate initialization and sampling stability | Identify which change gives rise to disagreement | Which random seeds to hold fixed and whether a nested design is feasible |
| Record invalid partitions and cluster-size diagnostics | Make unsuccessful or degenerate outputs visible | Treatment of coincident observations and zero-distance degeneracies |
| Separate seeds by purpose; summarize repeated runs | Make each source of variability identifiable | Confirm the provisional run count and distinguish empirical variability from population uncertainty |
| Reserve evaluation instances and external labels | Separate parameter selection from final assessment | What constitutes an independent instance, and whether labels exist |

## Unresolved Technical Questions

1. **DBI operational policy:** Which tolerance should identify near-coincident centers? How should singleton flags and empty-cluster failures affect reported validity rates? Before implementation, verify that the selected library follows the declared formula and document any special-case conventions; do not assume a numeric return value proves mathematical validity.
2. **ARI evaluation policy:** Which reference labels are suitable for the intended task, and are they available on every common subset? How should trivial partitions and zero-denominator cases be flagged? What practical limitations and uncertainty arise on small or imbalanced samples, beyond the fixed-size chance correction already derived?
3. **Stability budgets and validity:** Are 10 initialization runs, 20 pairs, a 0.8 fraction, and an overlap screen of 4 feasible and informative for the chosen sample sizes? Which trivial intersections should be excluded or flagged? Is a nested sampling/initialization design feasible, and which uncertainty claims can the eventual instance roster support?
4. **Sampling:** Label-free subset selection is proposed. How will rare groups and changes in sample size be handled without using evaluation labels to tune the experiment?
5. **Evaluation instances:** Which datasets, independently generated instances, or reserved subsets will be used for development and evaluation? Repeated seeds on one fixed instance must not be presented as additional independent instances.
6. **Preprocessing reconciliation:** Which scaling and PCA settings are proposed? Is preprocessing fitted on a development population or within each transductive instance, and how will that distinction be recorded?
7. **Clustering reconciliation:** Is the task a fixed two-way partition or variable-$k$ clustering? How will balance constraints, graph construction, infeasible outputs, and quantum bitstrings map to comparable partitions?
8. **Optimizer/noise reconciliation:** Which objective limits, shot budgets, stopping rules, gate channels, readout conditions, and compilation targets will be compared? The protocol separates the mechanisms, but their parameter values require the optimizer/noise review.
9. **Resource reporting:** Are the proposed timing boundaries, objective counts, circuit quantities, and total-shot fields available consistently for all selected methods? How should shared preprocessing and method-specific overhead be charged?

## Remaining Literature Synthesis

The current evidence includes four primary publications (with the ARI paper reviewed selectively for the claims used) and four supplementary documentation/tutorial sources. The stability paper fills a distinct sampling and perturbation gap; the DBI method paper is retained for its formulation and degeneracy evidence, so the initial two-to-three-paper target is exceeded for stated coverage reasons. No additional primary sources are planned without a specific remaining evidence gap. The initial protocol now specifies working budgets, instance-level evaluation, failure accounting, separate quality/resource fields, and a shot/noise comparison matrix. Final reconciliation concerns the actual instance roster, feasibility of the starting values, small-sample interpretation, and preprocessing/clustering/optimizer choices. The related review `content.md` files are empty in the current checkout, so agreement with them cannot be asserted. No implementation has been selected or benchmark performed here.
