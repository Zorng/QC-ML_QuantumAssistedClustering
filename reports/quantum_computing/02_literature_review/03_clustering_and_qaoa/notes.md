# Research notes: clustering formulation and QAOA

## Source comparison

| Source | Method and assumptions | Evidence and limitations | Relevance |
|---|---|---|---|
| Lucas [[1]](ref.md#lucas2014-ising), Sec. 2.2 | Review of Ising encodings; minimum unweighted cut with equal sizes and even vertex count, using a penalty. | Analytical formulation for adiabatic optimization; it is a review, not a clustering experiment or proof for weighted soft balance. | Motivates separating cut and balance costs; project weights and odd sizes require independent analysis. |
| Otterbach *et al.* [[2]](ref.md#otterbach2017-unsupervised), introduction, implementation, supplement II.3 and II.7 | QAOA and a classical loop for weighted MaxCut; distance-based clustering motivation. | Reports 19-qubit hardware experiments on weighted graph instances; the fully connected Euclidean clustering example is simulated. Different objective and sampling procedure; no evidence for this project's performance. | Shows a hybrid experimental setting and why weight meaning changes whether a cut should be maximized. |
| Farhi *et al.* [[3]](ref.md#farhi2014-qaoa), Secs. I–II and V | Alternating cost and $X$ mixer operations, uniform initial state, and parameterized expected objective, presented for maximization. | Theory including special regular-graph MaxCut cases; ideal analysis does not establish outcomes for this clustering model or a practical optimizer. | Defines the QAOA circuit and highlights the need to translate the optimization direction consistently. |

## Original derivation for the project objective

Assume symmetric nonnegative $w_{ij}$, set $w_{ij}=0$ for absent edges, and count each pair once. Let $S=\sum_i x_i$ and $d_i=\sum_{j\ne i}w_{ij}$. Since binary $x_i^2=x_i$, the cut is $\sum_i d_i x_i-2\sum_{i<j}w_{ij}x_ix_j$. The penalty expands as $\lambda[S+2\sum_{i<j}x_ix_j-nS+n^2/4]$. Therefore the upper-triangular QUBO has constant $c=\lambda n^2/4$, diagonal coefficients $a_i=d_i+\lambda(1-n)$, and pair coefficients $b_{ij}=2(\lambda-w_{ij})$. A matrix convention summing both $Q_{ij}x_ix_j$ and $Q_{ji}x_jx_i$ needs half these off-diagonal entries.

With $z_i=1-2x_i$, the cut for a pair becomes $w_{ij}(1-z_iz_j)/2$. Also $S-n/2=-\frac12\sum_i z_i$, whose square is $(n+2\sum_{i<j}z_iz_j)/4$. Thus $C=c_I+\sum_{i<j}J_{ij}z_iz_j$, where $c_I=\frac12\sum_{i<j}w_{ij}+\lambda n/4$ and $J_{ij}=(\lambda-w_{ij})/2$. Linear spin coefficients vanish. The balance term can introduce interactions even for pairs without graph edges. Complementing all labels changes every spin sign but leaves each product and cost unchanged.

## Hand-worked example

Take three vertices with $w_{12}=2$, $w_{13}=1$, $w_{23}=0$ and $\lambda=1$. The objective has QUBO constant $9/4$, linear coefficients $(1,0,-1)$, and pair coefficients $(-2,0,2)$ for $(12,13,23)$. The Ising constant is $9/4$ and pair coefficients are $(-1/2,0,1/2)$. For $x=(0,0,1)$, the cut is $1$ and the balance contribution is $1/4$, so $C=5/4$. QUBO gives $9/4-1=5/4$; $z=(1,1,-1)$ gives Ising value $9/4-1/2-1/2=5/4$. For $x=(0,0,0)$ the value is $9/4$, demonstrating that a one-group assignment is allowed. Here the closest-size penalty is $1/4$ because $n$ is odd.

## Mathematical verification and open questions

A temporary exact-rational enumeration compared the original cost, upper-triangular QUBO, and Ising expressions for every bit string with $n=1,2,3,4$, every pair weight drawn from $\{0,1,3/2\}$, and $\lambda\in\{0,1/2,2\}$. All 35,682 comparisons agreed, including odd $n$, absent edges, and zero penalty. This checks algebra on those finite cases; it is not an algorithm implementation or benchmark.

- How will the similarity graph and weight scale be chosen and aligned with the ML review? How sensitive are solutions to $\lambda$ and unequal group sizes?
- What explicit measured-string selection and tie-breaking policy should be agreed, and how will empty groups be reported?
- How will bit ordering, offsets, finite-shot expected cost, circuit parameters, and exact-enumeration references be logged?
- Which optimizers and noise models will the separate review justify? What evaluation budgets make comparisons meaningful?
