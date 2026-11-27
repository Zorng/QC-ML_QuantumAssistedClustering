# Research notes: classical and quantum PCA

## Source comparison

| Source | Method and assumptions | Evidence and limitations | Project relevance |
|---|---|---|---|
| Jolliffe and Cadima [[1]](ref.md#jolliffe2016) | Review of classical PCA, covariance and scaled-variable representations. | Mathematical exposition and survey, not a quantum experiment. | Provides the classical reference for axes, scores, and the effect of scaling. |
| Lloyd, Mohseni, and Rebentrost [[2]](ref.md#lloyd2014-qpca) | Density-matrix exponentiation using repeated copies, followed by quantum phase estimation; data-access and low-rank assumptions matter. | Theoretical method returning eigenstate information in quantum form; loading classical data and full classical output are separate costs. | Motivates covariance encoding and explains why a classical-matrix QPE demonstration is a distinct approach. |
| Cleve *et al.* [[3]](ref.md#cleve1998-quantum-algorithms) | Phase estimation from controlled unitary powers and a prepared eigenstate. | Theoretical quantum algorithm; assumes controlled operations and does not provide a tabular PCA pipeline. | Supports the phase register and precision discussion; input overlap and implementation costs remain essential. |
| Li *et al.* [[4]](ref.md#li2021-resonant-qpca) | Resonance-based method with a probe and quantum register; experiment on a $4\times4$ density matrix. | Reports a small spin-system experiment with tomography; not a classical-data-to-graph comparison and not the proposed QPE circuit. | Illustrates a different experimental route and the role of eigenstate readout. |

## Project derivations and checks

For centered $X$, $\operatorname{tr}(S)=\|X\|_F^2/(n-1)$. Therefore $\rho=S/\operatorname{tr}(S)=X^\mathsf{T}X/\|X\|_F^2$. Writing nonzero rows as $x_i$, $\sum_i[\|x_i\|^2/\|X\|_F^2](x_i^\mathsf{T}x_i/\|x_i\|^2)=\rho$. Uniformly sampling normalized rows instead gives $\sum_i x_i^\mathsf{T}x_i/(m\|x_i\|^2)$ over $m$ nonzero rows, generally not $\rho$.

For a hand check, choose centered rows $(2,0)$, $(-2,0)$, $(0,1)$, $(0,-1)$. Then $X^\mathsf{T}X=\operatorname{diag}(8,2)$, $\|X\|_F^2=10$, and $\rho=\operatorname{diag}(4/5,1/5)$. Norm-weighted rows yield these same diagonal entries; equal sampling yields $\operatorname{diag}(1/2,1/2)$ and loses the variance difference. The nonzero eigenvalues of $G=XX^\mathsf{T}/3$ are $8/3$ and $2/3$, matching the feature covariance. The first sample-space eigenvector $(1,-1,0,0)^\mathsf{T}/\sqrt2$ recovers $(1,0)^\mathsf{T}$ by $X^\mathsf{T}u/\sqrt{(n-1)\lambda}$. This is an algebraic illustration only, not a reported experiment.

For the proposed demonstration $U=e^{i\pi\rho}$, QPE interprets phases modulo one as $r/2$, so $r=1$ maps to $1/2$, not zero. Finite-register leakage can still place outcomes near phase boundaries or mix nearby eigenstates; record a decoding convention and uncertainty. A maximally mixed one-qubit input can be prepared by choosing $|0\rangle$ or $|1\rangle$ with equal probability, without loading a classical eigenvector. Conditional tomography requires measurements in more than the computational basis, and finite phase resolution can produce mixed conditional states rather than perfect eigenstates. Reconstruct and validate the state rather than assuming its purity.

## Unresolved questions

- What fitting population, scaling policy, retained dimension, and feature handling will be shared with the ML review? The existing ML proposal to fit once on the full unlabeled dataset remains a proposal, and the quantum branch must have a feasible matching fit scope.
- Which quantum PCA method, circuit construction, state preparation, register sizes, and readout precision are feasible within the resource budget? How will padding and bit ordering be recorded?
- What eigengap and uncertainty thresholds make a component or retained subspace reliable, and how will an unresolved or zero-variance case be surfaced?
- How will shots, rejected outcomes, reconstruction work, classical construction and projection, and end-to-end timing be counted? How well do the resulting scores and graph weights agree on identical samples?
- How should the results be compared when a degenerate eigenvalue crosses the retained-dimension boundary? An individual top-$k$ subspace is then not uniquely determined.
