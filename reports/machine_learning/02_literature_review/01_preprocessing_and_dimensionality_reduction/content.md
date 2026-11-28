## Preprocessing and Dimensionality Reduction

Every method in this project, from K-Means and spectral clustering to simulated annealing and QAOA, sees the data only through the distances and similarities computed after preprocessing. Scaling and dimensionality reduction therefore define the geometry on which all methods are compared. This subsection reviews how feature scaling, centering and classical principal component analysis (PCA) change that geometry, why retained variance does not establish clustering quality, and which preprocessing decisions the project proposes to adopt and validate.

### Feature scaling and centering

Most clustering methods used here depend on Euclidean distance,

$$
d(\mathbf{x}_i, \mathbf{x}_j) = \sqrt{\sum_{l=1}^{p} (x_{il} - x_{jl})^2},
$$

so a feature with a large numeric range dominates the distance regardless of its relevance. Two common scalings are standardization (z-score) and range scaling:

$$
z_{il} = \frac{x_{il} - \bar{x}_l}{s_l}, \qquad
r_{il} = \frac{x_{il} - \min_i x_{il}}{\max_i x_{il} - \min_i x_{il}},
$$

where $\bar{x}_l$ and $s_l$ are the mean and standard deviation of feature $l$. Centering alone translates the data and leaves Euclidean distances unchanged; it is the division by a per-feature scale that changes the geometry.

The choice of scale matters for cluster recovery. In a simulation study of variable standardization before cluster analysis, Milligan and Cooper compared several standardization formulas on data with known cluster structure and found that range-based standardization recovered the true clusters more consistently than z-score standardization under their simulated conditions [@milligan1988]. Their study did not combine scaling with PCA, and its conditions differ from this project's data, so it motivates a sensitivity check rather than a fixed choice.

The Wisconsin Breast Cancer (Diagnostic) dataset contains 30 real-valued features derived from 10 cell-nucleus measurements, each summarized as a mean, a standard error and a "worst" value [@wolberg1993]. These features are on very different numeric ranges, so without scaling the distances, and any PCA computed from them, would be dominated by the largest-range measurements.

### Classical principal component analysis

Let $\mathbf{X}_c \in \mathbb{R}^{n \times p}$ be the centered (and, if chosen, scaled) data matrix. PCA finds orthonormal directions of maximal variance from the eigendecomposition of the sample covariance matrix,

$$
\mathbf{S} = \frac{1}{n-1}\mathbf{X}_c^{\top}\mathbf{X}_c, \qquad
\mathbf{S}\mathbf{v}_k = \lambda_k \mathbf{v}_k, \qquad
\lambda_1 \ge \lambda_2 \ge \dots \ge \lambda_p \ge 0,
$$

or equivalently from the singular value decomposition of $\mathbf{X}_c$ [@jolliffe2016]. Keeping the first $k$ eigenvectors $\mathbf{V}_k = [\mathbf{v}_1, \dots, \mathbf{v}_k]$ gives the reduced coordinates $\mathbf{Z} = \mathbf{X}_c \mathbf{V}_k$, and the proportion of variance retained is

$$
\text{retained variance}(k) = \frac{\sum_{j=1}^{k} \lambda_j}{\sum_{j=1}^{p} \lambda_j}.
$$

PCA on the covariance matrix of unscaled data is dominated by high-variance variables, while PCA on the correlation matrix is the same as PCA on standardized variables [@jolliffe2016]. The scaling step is therefore part of the PCA definition, not a separate detail. The scikit-learn implementation centers the input but does not scale it, so scaling must be applied explicitly before PCA [@sklearn-pca].

Common rules for choosing $k$ include a cumulative retained-variance threshold and inspection of the scree plot of eigenvalues. Jolliffe and Cadima present these as heuristics rather than a universal rule [@jolliffe2016]. Principal components are also defined only up to sign, and components with equal eigenvalues span a subspace with no unique basis. Comparisons across runs or methods should therefore compare the principal subspaces rather than individual eigenvectors.

### Effect on distance geometry

When all $p$ components are kept, PCA is an orthogonal rotation of the centered data and preserves all pairwise Euclidean distances. Truncation to $k$ components removes the part of each difference vector that lies outside the retained subspace:

$$
\lVert \mathbf{x}_i - \mathbf{x}_j \rVert^2
= \lVert \mathbf{V}_k^{\top}(\mathbf{x}_i - \mathbf{x}_j) \rVert^2
+ \lVert (\mathbf{I} - \mathbf{V}_k\mathbf{V}_k^{\top})(\mathbf{x}_i - \mathbf{x}_j) \rVert^2 .
$$

Distances in the reduced space are never larger than in the full space, and pairs that differ mainly along discarded directions move closer together. A similarity graph built from these distances, for example with a Gaussian weight $w_{ij} = \exp\!\left(-d_{ij}^2 / 2\sigma^2\right)$, inherits these changes. The bandwidth $\sigma$ must also be chosen in the space where the graph is built. Since the QAOA formulation uses one binary variable per data point, the number of components does not change the qubit count. Its effect on the quantum solver is entirely through the graph weights.

### Retained variance is not clustering quality

A high retained variance shows that the reduced coordinates reconstruct the data well. It does not show that the reduced coordinates preserve the cluster structure. Chang showed, for a mixture of two multivariate normal distributions, that the components with the largest eigenvalues need not carry the information that separates the two groups. The separating direction can lie in a low-variance component that a variance-based rule would discard [@chang1983]. Empirically, Yeung and Ruzzo compared clustering on original variables with clustering on subsets of principal components, using external class labels for evaluation. They found that clustering on the components did not necessarily improve cluster quality and often degraded it, and that the first few components were not reliably the best choice. They did not recommend PCA before clustering except in special circumstances [@yeung2001].

For this project, retained variance is reported as a description of compression only. The choice of $k$ is judged by its effect on clustering outcomes, and a scaled but unreduced baseline is kept for comparison.

### Zero-variance features and comparable sample subsets

A constant feature carries no distance information, and its z-score is undefined because $s_l = 0$. scikit-learn's `StandardScaler` leaves such a feature unscaled, with scale factor 1, so after centering it becomes identically zero [@sklearn-scaler]. Such a feature contributes nothing to distances and produces a zero eigenvalue in PCA. Constant features can instead be removed explicitly with a variance threshold of zero [@sklearn-variance-threshold]. Near-constant features are the more serious risk: standardization divides by a very small $s_l$, which can magnify measurement noise to the same scale as informative features.

Sample size raises a second issue. QAOA runs use subsamples of 8 to 16 points, while the classical methods can use all 569 samples. If the scaler and PCA were fitted separately on each subsample, every run would use a different mean, scale and set of components, so coordinates and distances would not be comparable across runs or methods. With $n \le 16$ points and 30 features, the subsample covariance has rank at most $n-1$, so a PCA fitted on the subsample alone is poorly determined. A feature can also be constant within a small subsample even when it varies in the full dataset. Fitting the transform once and applying it unchanged to every subsample avoids these problems.

### Proposed preprocessing decisions

The following choices are proposed for the first implementation milestone. They are design decisions supported by the literature above, not experimental results.

| Decision | Proposal | Rationale | Needs validation |
|---|---|---|---|
| Constant features | Remove zero-variance features before scaling; record which were removed | Avoids undefined scaling and zero-eigenvalue components [@sklearn-scaler; @sklearn-variance-threshold] | Whether near-constant features also need removal |
| Scaling | z-score standardization as the default; range scaling as a sensitivity variant | Puts features on comparable scales and makes PCA equivalent to correlation-matrix PCA [@jolliffe2016]; range scaling was more reliable in simulation [@milligan1988] | Which scaling better preserves the known structure on WDBC |
| Fit scope | Fit the scaler and PCA once on the full dataset without labels, then apply the same transform to all subsamples | Keeps coordinates and distances comparable across methods and runs | Effect of subsample selection on results |
| Number of components | Evaluate a small set of $k$ values together with a scaled, unreduced baseline; report retained variance for each | Retained variance does not establish cluster quality [@chang1983; @yeung2001] | Sensitivity of graphs and clustering metrics to $k$ |
| Transform recording | Store removed features, scaling parameters, PCA components, eigenvalues, $k$, random seeds, subsample indices and library versions with each run | Components have sign and basis ambiguity, so refitting may not reproduce the same coordinates | None; this is a reproducibility requirement |

### Open questions

- Which scaling, z-score or range-based, better preserves the benign/malignant structure in the Wisconsin data when that structure is used only for evaluation?
- How sensitive are the similarity graph, the bandwidth choice and the clustering results to the number of retained components?
- In which space should internal metrics such as Silhouette and Davies–Bouldin be computed when methods are run with different $k$?
- Should the small QAOA subsamples be drawn uniformly at random or stratified, given that stratification would use labels reserved for evaluation?
- Do heavy-tailed "worst" features call for robust scaling instead of z-score or range scaling?
