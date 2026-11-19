# Reading notes: first draft for review

These notes explain the direction and remaining decisions; the companion content is a proposed synthesis, not a finished experiment or approved graph design. Citation keys and open full-text links are in `ref.md`.

## Source comparison

| Source and kind | Method / assumptions | Evidence and limitations | Relevance here |
|---|---|---|---|
| [[1]](ref.md#arthur2007-kmeanspp), primary research | Squared-distance K-means objective; randomized distance-weighted seeding. | Proves an expected seeding bound and reports preliminary experiments; the bound does not promise a global optimum on each run. | Baseline objective and initialization sensitivity; does not establish a graph or balance setting. |
| [[2]](ref.md#ng2001-spectral-clustering), primary research | Normalized affinity eigenvectors, row normalization and K-means; assumes a meaningful affinity. | Gives an analysis under stated conditions and illustrative experiments; graph scale and embedding influence the partition. | Explains the spectral baseline and why an embedding can help with non-convex shapes. |
| [[3]](ref.md#vonluxburg2007-spectral-tutorial), tutorial, not primary experiment | Surveys similarity graphs, Laplacians, normalized cuts and practical issues. | Conceptual synthesis, not a project-specific benchmark; graph choices and connectivity matter. | Compare construction options and distinguish volume normalization from sample-count balance. |

## Teaching notes

- **Quick glossary:** A sample is one observation; a feature is a measured property; a centroid is a group's coordinate-wise mean; a graph vertex stands for a sample; an edge weight describes the strength of a relationship; an embedding assigns new coordinates to samples.

- **What is a good cluster?** There is no universal answer. Ask whether the representation and similarity reflect the question, whether groups remain understandable and stable, and whether an evaluation measure matches the expected shape. Compactness alone can misrepresent curved groups; labels are optional evaluation information, not input to this unsupervised graph.
- **Distance versus similarity:** a distance of zero means identical coordinates; an RBF similarity of one follows for distinct samples with identical coordinates. Large distances yield smaller positive weights. Scaling a feature changes its contribution to Euclidean distance; compare preprocessing consistently.
- **How the methods work:** See “K-means clustering” and “Spectral clustering” in `content.md` for the two methods.
- **Original hand-worked iteration:** Start points 0, 2, 8, 10 with centers 0 and 2. The first assignment gives {0} and {2, 8, 10}, with updated means 0 and $20/3$. The next assignment gives {0, 2} and {8, 10}, with means 1 and 9. A further assignment is unchanged, so this example stops. Its squared error is $(0-1)^2+(2-1)^2+(8-9)^2+(10-9)^2=4$. It is an illustration, not a benchmark.
- **Check your understanding:** Why can the same samples have different nearest neighbors after feature scaling? Why could two points on a curved chain be in the same group despite being far apart? What changes if the graph has an isolated vertex?
- **Graph choices:** See “Similarity graph construction” in `content.md`. Inspect isolated vertices and components before using inverse degrees; disconnection may reflect structure or excessive sparsification.
- **Balance is a preference, not validity:** if all bits coincide the cut is zero, but the penalty may or may not be large enough to prefer a nonempty split. At odd sample counts the most balanced split has penalty $\lambda/4$, not zero. The squared sum also creates pairwise interactions between all bits when expanded, even if the graph itself is sparse.

### Original small illustration of the penalty tradeoff

Take four vertices with every edge weight 1. A partition of sizes 1 and 3 has cut 3 and penalty $\lambda$, hence cost $3+\lambda$. A 2–2 split has cut 4 and zero penalty, hence cost 4. An all-in-one assignment has cost $4\lambda$. Comparing all possible assignments, a one-group assignment is optimal for $\lambda<1$; at $\lambda=1$, all three size patterns tie; and a 2–2 split is optimal for $\lambda>1$. Thus penalty strength changes the selected result, and in this example a 1–3 split never uniquely wins. This is an invented mathematical example, not empirical evidence or a recommended value.

## Proposals and questions for review

1. Consider an RBF-weighted graph as a starting candidate and compare a full graph with a documented symmetrized neighborhood graph on small examples. What kernel scale, neighbor rule and handling of ties should be justified? No parameter is selected yet.
2. How should scaling and PCA output be shared across methods? Compare on the same samples and document the representation used for graph building and evaluation.
3. How should $\lambda$ be chosen relative to the edge-weight scale, and how sensitive are partitions to it? Examine uneven cluster sizes, odd $n$, zero-weight graphs and one-group outcomes; report invalid outcomes explicitly.
4. How should disconnected or zero-degree vertices be handled by the spectral baseline? Should graph construction allow them, warn, or reject a configuration? This remains open.
5. On tiny instances, enumerate binary assignments to check the proposed objective and compare the solver's value with the exact minimum. This checks the objective, not whether its optimum is a meaningful clustering.
6. Compare baselines using the same samples, suitable shared-space metrics, multiple seeds and cluster-size reports. Which measures best reflect both compact blobs and curved moons? These are planned comparisons, not results.
