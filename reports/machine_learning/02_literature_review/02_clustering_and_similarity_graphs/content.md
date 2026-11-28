### Clustering and similarity graphs

#### Introduction to clustering

Clustering is a way to explore data by grouping similar observations without giving the algorithm the correct group for each one. Each observation is a **sample** and each measured property is a **feature**. For a hypothetical example, a sample might be described by two numerical features, producing a point such as $(1,2)$; another sample could be $(2,2)$. The input is a collection of such points, and the output assigns each sample to a group. The group numbers are arbitrary labels: exchanging 0 and 1 does not change the partition.

A useful clustering depends on the question and on how similarity is defined. Nearby points may belong together, but some meaningful groups are curved or have different sizes. Features on larger numerical scales can dominate a distance, so scaling and any dimensionality reduction affect the grouping. Known labels, when available, can be reserved for evaluating the outcome rather than constructing it.

#### K-means clustering

K-means requires a specified number of clusters $k$ before running the method. It describes each group by a **centroid**, the arithmetic mean of its points, and seeks to minimize the sum of squared distances from samples to their assigned centroids. Given starting centers, the usual procedure assigns each sample to its nearest center, recomputes each center as the mean of its assigned samples, and repeats these steps until the assignments stop changing. This is a local search: different starting centers can lead to different final groupings. K-means++ uses a distance-based random procedure to choose initial centers; its expected initial cost has a theoretical bound, but a particular run is not guaranteed to find the global optimum [[1]](ref.md#arthur2007-kmeanspp).

For an original hand-worked example, suppose four one-dimensional points are 0, 2, 8 and 10. If their two groups are {0, 2} and {8, 10}, their centroids are 1 and 9. Each point is one unit from its centroid, so the sum of squared distances is $1+1+1+1=4$. This illustrates a possible grouping, not a measured project result.

K-means minimizes the within-cluster sum of squares: the total squared Euclidean distance between each sample and its assigned centroid. For $n$ samples, let $z_i$ be the feature vector of sample $i$, $c_i$ its assigned group, and $\mu_{c_i}$ that group's centroid. The objective is $J=\sum_{i=1}^{n}\|z_i-\mu_{c_i}\|^2$, where $\|z_i-\mu_{c_i}\|$ is the Euclidean distance to the centroid. This objective favors compact groups around centers; it does not directly minimize a graph cut. For curved groups, two points can belong to the same shape even when a centroid-based boundary separates them.

#### Similarity graph construction

A graph offers another way to describe the same samples. Each **vertex** represents a sample; an **edge** represents a relationship between two samples. Its weight expresses their similarity: a larger weight means a stronger connection. For example, nearby points along a curved chain can have strong local connections even if points at opposite ends of the chain are far apart. Such relationships give a clustering method information about how points are connected, not just their distance to a center.

Distance and similarity have opposite interpretations: for points $z_i$ and $z_j$, the Euclidean distance $d_{ij}=\|z_i-z_j\|$ is small when they are close, while their similarity should generally be large. A candidate radial basis function (RBF) converts the distance into a weight $w_{ij}=\exp\!\left(-\frac{d_{ij}^{2}}{2\sigma^{2}}\right)$ for $i\ne j$; self-edge weights are $w_{ii}=0$.

Here $w_{ij}$ is the similarity between distinct samples $i$ and $j$, and $\sigma>0$ is the kernel width. Distinct samples with identical coordinates have weight 1; as distance grows, weight approaches 0. Increasing $\sigma$ makes similarity decrease more slowly, so more distant points retain stronger weights. This choice and its scale must be assessed for the data; no value has been selected.

A full RBF graph keeps a positive edge between every pair. A sparse graph retains selected relationships, for example only nearby neighbors. This can emphasize local structure, but removing too many edges can disconnect the graph or leave a vertex with no neighbors. Such a disconnection could reflect a true separation or a construction choice; it must be inspected.

#### Spectral clustering

Spectral clustering uses the graph to create a new representation of the samples before assigning groups. Its mathematical tools include **eigenvectors**: lists of numbers, one value per vertex, that describe patterns associated with a graph-derived matrix. Intuitively, suitable low-variation patterns tend to give similar values to strongly connected vertices. This intuition depends on the graph and normalization; it does not mean that any eigenvector alone reveals the correct groups.

In one method described by Ng, Jordan and Weiss, several eigenvectors provide new coordinates for each sample. The method normalizes each sample's new coordinate row and then applies K-means to those rows [[2]](ref.md#ng2001-spectral-clustering). The two baselines therefore use K-means on different inputs: direct K-means uses the original or preprocessed feature coordinates; this spectral method uses coordinates derived from relationships between samples. The paper illustrates why the latter can help with a non-convex shape. It is one spectral algorithm, not a guarantee for every graph or dataset.

Formally, let $W$ contain the weights $w_{ij}$, and let the diagonal degree matrix $D$ have entries $D_{ii}=\sum_j w_{ij}$, the total weight attached to vertex $i$. Assuming positive degrees, the selected method uses the $k$ eigenvectors with largest eigenvalues of $D^{-1/2}WD^{-1/2}$. Equivalently, it uses the eigenvectors with smallest eigenvalues of $I-D^{-1/2}WD^{-1/2}$, the symmetric normalized graph Laplacian. Other spectral methods use different graph matrices and steps. Threshold and nearest-neighbor graph choices, including whether neighbor links are mutual, affect the graph; isolated vertices require explicit handling [[3]](ref.md#vonluxburg2007-spectral-tutorial).

Spectral embedding should also be distinguished from principal component analysis (PCA). PCA finds directions of variation in feature data; spectral embedding uses a graph made from relationships among samples. PCA can change the coordinates used to build the graph, while spectral clustering then works with the resulting graph. The two steps therefore answer different questions.

#### Relevance to the quantum-assisted clustering framework

The project proposes a two-group graph objective to be searched using QAOA. For each of the $n$ samples, a binary variable $x_i$ indicates group 0 or 1; $w_{ij}\ge 0$ is the similarity weight between samples $i$ and $j$. The proposed cost is $C(x)=\sum_{i<j}w_{ij}(x_i-x_j)^2+\lambda\left(\sum_i x_i-\frac n2\right)^2$, where $x_i\in\{0,1\}$ and $\lambda\ge0$.

The first term is a **cut**: an edge contributes its weight when its endpoints receive different group labels and contributes zero otherwise. A low cut avoids separating strongly connected samples. However, assigning all samples to the same group also gives a zero cut. The second term adds a soft preference for similar group sizes, with $\lambda$ controlling its strength. It does not guarantee two nonempty groups, and too much emphasis on balance can distort naturally unequal clusters. For odd $n$, the most even split leaves a penalty of $\lambda/4$.

The proposed cost differs from normalized cut, which divides cut contributions by graph volumes, where a group's volume is the sum of its vertex degrees [[3]](ref.md#vonluxburg2007-spectral-tutorial). Balancing sample counts and accounting for weighted degrees are different operations. Direct K-means and spectral clustering are therefore comparison methods, not optimizers of this same cost. A full or sparsified RBF graph and a balance strength remain candidates to study, not approved settings. Future comparisons should report cluster sizes, one-group outcomes, stability and suitable quality measures alongside objective values; no experiment is claimed here.
