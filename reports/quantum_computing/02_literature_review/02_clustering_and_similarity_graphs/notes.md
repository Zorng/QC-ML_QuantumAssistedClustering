# Research outline: clustering and similarity graphs

- Adapt the ML review's clustering and similarity-graph foundations to explain the inputs and classical baselines relevant to the quantum clustering formulation; avoid copying its prose.
- Examine how representation, distances, graph construction, edge weights, and balance assumptions affect the partitioning problem and its interpretation.
- Connect these foundations to the QAOA topic, leaving the QUBO/Ising mapping and quantum algorithm discussion there. Verify sources and record topic-local references before drafting.

## Source-to-claim mapping and adaptation

- Arthur and Vassilvitskii [[1]](ref.md#arthur2007-kmeanspp), introduction and Sec. 2.2: centroid objective, iterative method, and randomized distance-based seeding. The paper's expected bound concerns the K-Means cost; it does not prove a best result for each run or for graph clustering.
- Von Luxburg [[2]](ref.md#vonluxburg2007-spectral-tutorial), Secs. 2–3: weighted similarity graphs, RBF and neighborhood constructions, and graph connectivity. This is a tutorial, not a primary experimental study. The implications for choosing and validating this project's graph are project interpretation.
- Ng, Jordan, and Weiss [[3]](ref.md#ng2001-spectral-clustering), Secs. 1–2: normalized affinity, leading eigenvectors, row normalization, and clustering in the embedding. Their method uses a graph matrix; classical PCA's feature covariance has a different role.

The synthesis adapts the ML topic's distinctions between distance and similarity, centroid and graph objectives, neighborhood symmetrization, and soft balance. The ML draft is contextual material, not a primary citation; the exposition and project implications are written for the quantum report. Detailed PCA and QUBO/Ising derivations remain in the adjacent topics.

## Unresolved questions

- Which scaling and retained PCA representation, RBF width, neighbor count, tie rule, and symmetrization should be used? No numerical graph settings are selected here.
- How should disconnected graphs and isolated vertices be detected, reported, and handled by the normalized spectral baseline and graph objective?
- How sensitive are results to naturally unequal group sizes and the relative scale of weights and the soft-balance penalty? How should one-group outcomes be surfaced?
- Which shared-space metrics, repetitions, seeds, and budgets provide a fair comparison of methods that optimize different objectives?
