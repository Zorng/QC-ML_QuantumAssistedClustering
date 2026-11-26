# Notes: Preprocessing and Dimensionality Reduction

Scope: feature scaling, centering, classical PCA, retained variance, information loss, and the effect of preprocessing on distance geometry for graph-based clustering.

Project pipeline being reviewed: load data (Wisconsin Breast Cancer Diagnostic, 569 × 30, or scikit-learn moons/blobs) → scale → PCA to a few components → similarity graph → K-Means, spectral clustering, simulated annealing, QAOA. QAOA runs use subsamples of 8–16 points. Evaluation uses Silhouette, Davies–Bouldin and run time; diagnosis labels are used for evaluation only.

## Source comparison

| Source | Method studied | Assumptions | Evidence type | Limitations | Project relevance |
|---|---|---|---|---|---|
| `jolliffe2016` | Classical PCA: eigendecomposition of covariance/correlation matrix, SVD of centered data, variants (robust, sparse, functional PCA) | Linear structure; variance treated as the quantity of interest; centered data | Review and mathematical exposition | Not about clustering; no single rule for choosing the number of components | Defines the PCA step, the covariance versus correlation choice (equivalent to scaling or not), and component-selection rules |
| `milligan1988` | Several variable-standardization formulas (z-score, range-based and others) before cluster analysis | Simulated data with known cluster structure; Euclidean distance; clustering methods of that study | Monte Carlo simulation, recovery of known clusters | Simulated conditions from 1988; does not combine scaling with PCA; results depend on the error conditions simulated | Evidence that the scaling choice changes cluster recovery; motivates a scaling sensitivity check |
| `yeung2001` | Clustering on original variables versus on subsets of principal components | Gene expression data and synthetic data with external class labels; external agreement measured with the adjusted Rand index | Empirical comparison on real and synthetic datasets | Domain is gene expression (many variables, few classes); results are empirical, not a theorem | Direct evidence that clustering on the leading PCs can degrade cluster quality; supports keeping a no-PCA baseline |
| `chang1983` (supporting) | Principal components before separating a two-component normal mixture | Mixture of two multivariate normals with common covariance | Theoretical argument and simulated example | Specific model; two clusters only | Shows that the largest-eigenvalue components need not carry the separation between clusters; matches the two-cluster first milestone |
| `sklearn-*` (supporting) | `StandardScaler`, `PCA`, `VarianceThreshold` behaviour | Implementation of the methods used by the project | Official documentation | Describes behaviour, not whether a choice is good | Pins down implementation behaviour: zero-variance handling, PCA centers but does not scale |

## Reading notes by source

### `jolliffe2016`

- PCA finds uncorrelated linear combinations with maximal variance; the problem reduces to an eigenvalue/eigenvector problem for the covariance (or correlation) matrix, or equivalently the SVD of the centered data matrix.
- PCA on the covariance matrix is dominated by variables with large variances when units differ; PCA on the correlation matrix is PCA on standardized variables. This is the formal link between the scaling step and PCA.
- Choosing the number of components: cumulative proportion of variance thresholds, scree plots and other rules are heuristics; the review does not give a universal rule.
- Eigenvectors are defined only up to sign; equal eigenvalues give a non-unique basis for the eigenspace. This matters for recording and comparing components across runs.
- To check during full reading: exact section and page for the covariance versus correlation discussion and for the component-selection rules.

### `milligan1988`

- Compares several standardization formulas, including z-score `(x − mean) / sd` and range-based `x / range` and `(x − min) / range`.
- Reported finding (to confirm against the result tables during full reading): range-based standardization recovered the known cluster structure more consistently than z-score standardization under the simulated conditions.
- Caveat: the z-score divides by a standard deviation that itself includes the between-cluster spread, so features with strong cluster separation can be shrunk. This is the usual explanation given for the result; confirm that the paper states it before citing it as the authors' argument.
- Range-based scaling is sensitive to extreme values. The WDBC "worst" features are extreme values by construction, so outlier sensitivity needs checking for this dataset.

### `yeung2001`

- Compared clustering on the original variables with clustering on the first few PCs and on other PC subsets, measuring agreement with external classes using the adjusted Rand index.
- Main finding: clustering on PCs did not necessarily improve, and often degraded, cluster quality; the first few PCs were not reliably the best subset. The authors do not recommend PCA before clustering except in special circumstances.
- Uses external labels only for evaluation, which matches the project's use of the diagnosis labels.
- To check during full reading: which clustering algorithms, which datasets, and whether PCA was on covariance or correlation.

### `chang1983`

- Two-normal mixture: the direction that separates the two clusters can lie in a component with a small eigenvalue. Selecting components by largest eigenvalue can therefore discard the separating information.
- Cited later as a standard counter-example to selecting components by variance before clustering.

### scikit-learn documentation

- `StandardScaler`: if a feature has zero variance, unit variance cannot be achieved and the feature is left as-is (scale factor 1). The implementation treats near-zero scales the same way to avoid division by very small values.
- `PCA`: input is centered but not scaled for each feature before the SVD; scaling must be done explicitly.
- `VarianceThreshold`: removes features whose variance is below a threshold; the default threshold 0 removes constant features.

## Distance geometry notes

- Translation (centering) does not change Euclidean distances; scaling does.
- With all components kept, PCA is an orthogonal rotation of the centered data, so Euclidean distances are unchanged.
- Truncating to `k` components removes the residual part of each difference vector:
  `||x_i − x_j||² = ||V_kᵀ(x_i − x_j)||² + ||(I − V_k V_kᵀ)(x_i − x_j)||²`.
  Reduced-space distances are never larger than full-space distances.
- A Gaussian similarity `w_ij = exp(−d_ij² / (2σ²))` depends on both the distances and the bandwidth, so a bandwidth tuned in one space should not be reused unchanged in another. Graph construction details belong to the clustering and similarity graphs topic.

## Dataset-specific observations (to confirm from data, not literature)

- WDBC has 30 real-valued features: mean, standard error and "worst" (mean of the three largest values) of 10 cell-nucleus measurements. The features are on very different numeric ranges (for example area versus smoothness). Confirm ranges from a summary table before quoting numbers.
- Check for constant or near-constant features in the full dataset and in typical 8–16 point subsamples.
- With n ≤ 16 points and 30 features, the sample covariance of a subsample has rank at most n − 1, so at most n − 1 non-zero components exist and PCA estimates from the subsample alone are unstable.

## Shared technical assumptions with the QC PCA topic

- Input to any PCA (classical or quantum) is the scaled, centered data matrix produced by this preprocessing step, with zero-variance features already removed.
- The retained dimension `k` and the fitted transform are shared so that classical and quantum PCA results can be compared on the same subspace.
- Because eigenvector signs and bases of degenerate eigenspaces are not unique, comparisons should use principal subspaces (for example projector distance) rather than raw eigenvectors.

## Unresolved questions

1. Does z-score or range scaling better preserve the benign/malignant structure on WDBC? (Requires experiment.)
2. Which `k` values should be tested, and how sensitive are the similarity graph and the clustering results to `k`?
3. Should the QAOA subsample be drawn uniformly at random or stratified? Stratification uses labels, which are reserved for evaluation; agree with the evaluation topic.
4. In which space should Silhouette and Davies–Bouldin be computed when methods use different `k`? Agree with the evaluation topic.
5. Are any WDBC features heavy-tailed enough that robust scaling should be considered?
6. Not yet covered in course material: robust PCA and kernel PCA. Recorded as gaps, not reviewed here.
