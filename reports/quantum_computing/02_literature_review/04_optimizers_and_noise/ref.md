# References and Verification

Local IEEE numbers follow first appearance in this topic's `content.md`. Source keys are independent of those numbers. Access-check dates are recorded per source: the original optimizer sources were checked on 2026-11-22, the added mitigation sources on 2026-11-24, and the selected metric documentation on 2026-11-25. Checks were made from the assistant's environment and do not independently verify availability from Cambodia. Supplied PDFs were read locally. The three PennyLane noise/measurement pages were also reported accessible by the user during the earlier ML review; that report does not establish current or country-wide availability. Documentation pages are mutable: observed versions below describe the pages checked, not selected project dependencies.

## farhi2014-qaoa

[1] E. Farhi, J. Goldstone, and S. Gutmann, “A quantum approximate optimization algorithm,” arXiv:1411.4028v1 [quant-ph], Nov. 2014. [Online]. Available: https://arxiv.org/abs/1411.4028v1

- **Identity/version:** Original preprint, v1, submitted Nov. 14, 2014; title, all three authors, identifier, and version checked against its first page and arXiv record. No journal publication or DOI asserted.
- **Public full text:** [Versioned PDF](https://arxiv.org/pdf/1411.4028v1).
- **Access check:** 2026-11-22: arXiv abstract and 16-page PDF opened; supplied `1411.4028v1.pdf` read for the targeted passages. No geographic access test performed.
- **Supporting locations:** Introduction, PDF pp. 2–3, Eqs. (1)–(10): maximized diagonal objective, alternating circuit, $2p$ angles, expectation, sampling, and integer-spectrum angle-period assumption. Beginning of Section II, p. 4: MaxCut construction.
- **Reading boundary:** This review uses those definitions, not the later approximation proofs or an asserted noisy-device guarantee.

## pellowjarman2024-qaoa-optimizers-noise

[2] A. Pellow-Jarman, S. McFarthing, I. Sinayskiy, D. K. Park, A. Pillay, and F. Petruccione, “The effect of classical optimizers and Ansatz depth on QAOA performance in noisy devices,” *Scientific Reports*, vol. 14, Art. no. 16011, 2024, doi: [10.1038/s41598-024-66625-6](https://doi.org/10.1038/s41598-024-66625-6).

- **Verified metadata:** Author list, title, journal, year, volume, article number, and DOI checked directly in the supplied published PDF, `s41598-024-66625-6.pdf`, pp. 1 and 11. The article number is not a page range.
- **Public full text:** [Author preprint, arXiv v2](https://arxiv.org/pdf/2307.10149v2); [readable HTML v2](https://arxiv.org/html/2307.10149v2). [Version record](https://arxiv.org/abs/2307.10149v2): originally submitted July 19, 2023, revised July 11, 2024. This is an alternate version of the same publication, not an additional independent source.
- **Access check:** 2026-11-22: supplied 11-page published PDF read; arXiv v2 HTML and 13-page PDF opened. The publisher/DOI request redirected to a Nature authentication endpoint that the browsing tool could not access; successful publisher-web access is not claimed. The local publication and public preprint support the cited text.
- **Supporting locations, published pagination:** pp. 2–3: formulation and expectation; p. 4, “Classical optimizers” and “Classical optimizer comparison,” Table 1: software, gradient method, graph set, repeats, shots, evaluation cap, and device-derived noise model; pp. 5–6, “Comparison of classical optimizers,” Fig. 5: condition-dependent rankings; pp. 7–8: depth behavior and discussion.
- **Qualification:** Abstract and Results emphasize different leaders in the shot-noise condition. This discrepancy is retained in the notes and synthesis. The published approximation-ratio display is not adopted as a project definition.

## scipy-cobyla

[3] SciPy developers, “minimize(method='COBYLA'),” *SciPy Documentation*. Accessed: Nov. 22, 2026. [Online]. Available: https://docs.scipy.org/doc/scipy/reference/optimize.minimize-cobyla.html

- **Type/version:** Official method/API documentation, observed SciPy v1.18.0. No publication date or DOI asserted for this page.
- **Public full text:** [Method/options](https://docs.scipy.org/doc/scipy/reference/optimize.minimize-cobyla.html); supplementary [fmin_cobyla algorithm explanation](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.fmin_cobyla.html).
- **Access check:** 2026-11-22: both pages opened and relevant sections read. Cambodia access has not been checked for these pages.
- **Supporting locations:** Method Options: `rhobeg`, `tol`, `maxiter`, `catol`; note “Changed in version 1.16.0.” Supplementary page, Notes: interpolation at $d+1$ points and trust-region behavior. It cites Powell's original publication; that bibliography is not treated as a substitute for reading Powell.

## spall1992-spsa

[4] J. C. Spall, “Multivariate stochastic approximation using a simultaneous perturbation gradient approximation,” *IEEE Transactions on Automatic Control*, vol. 37, no. 3, pp. 332–341, Mar. 1992, doi: [10.1109/9.119632](https://doi.org/10.1109/9.119632).

- **Verified metadata:** Author, title, journal, volume, issue, month/year, and page range checked in the supplied scanned PDF. The DOI resolves to IEEE document 119632; the publisher presents a JavaScript/robot-check page, so publisher full-text access is not claimed. Publication identity also matches the [author's institutional reference list](https://www.jhuapl.edu/spsa/Pages/References-List_Ref.htm).
- **Public full text:** [Author/institution-hosted PDF](https://www.jhuapl.edu/spsa/pdf-spsa/spall_tac92.pdf).
- **Access check:** 2026-11-22: institutional PDF opened as a ten-page PDF. Its scanned text was not extracted by the browser; the supplied `spall_tac92.pdf` was inspected as page images. No Cambodia access verification claimed.
- **Supporting locations:** Sections I–II, pp. 332–333, Eqs. (2.1)–(2.3): update, paired noisy observations, two-call construction, $2q$ averaging, and finite-difference contrast. Section III, pp. 334–337: smoothness, moments, gains, stability/attraction and convergence qualifications. Section IV, pp. 337–338: conditional efficiency comparison. Section V, pp. 338–340, Table I: numerical statistical objective, matched measurement counts, dimensionality, gains, and finite-sample cautions. Section VI, p. 340: conclusions and open issues.
- **Reading boundary:** The proofs were reviewed for assumptions, not independently rederived. No quantum performance finding is attributed to this paper.

## pennylane-spsa-optimizer

[5] PennyLane developers, “SPSAOptimizer,” *PennyLane Documentation*. Accessed: Nov. 22, 2026. [Online]. Available: https://docs.pennylane.ai/en/stable/code/api/pennylane.SPSAOptimizer.html

- **Type/version:** Official API documentation, observed PennyLane 0.45.1; no page publication date or DOI asserted.
- **Public full text:** [API](https://docs.pennylane.ai/en/stable/code/api/pennylane.SPSAOptimizer.html); [linked implementation source](https://docs.pennylane.ai/en/stable/_modules/pennylane/optimize/spsa.html).
- **Access check:** 2026-11-22: both pages opened; relevant API and source methods read. Cambodia access has not been checked for these particular optimizer pages.
- **Supporting locations:** Class definition, update equations, Parameters and Note; source `__init__`: algebraic initialization of gain constants; `compute_grad`: perturbed objective evaluations; `step`: update without forward cost; `step_and_cost`: additional forward evaluation and pre-update cost return.
- **Qualification:** The generic term-count note does not establish this project's circuit grouping or total shot count. Those require the eventual measurement plan.

## pennylane-measurements

[6] PennyLane developers, “Measurements,” *PennyLane Documentation*. Accessed: Nov. 22, 2026. [Online]. Available: https://docs.pennylane.ai/en/stable/introduction/measurements.html

- **Type/version:** Official guide, observed PennyLane 0.45.1; title and URL checked. No publication date or DOI asserted. Key reused from the ML evaluation draft for the same page.
- **Public full text:** [Measurements](https://docs.pennylane.ai/en/stable/introduction/measurements.html).
- **Access check:** 2026-11-22: relevant guide text opened. The user previously reported this page accessible from their connection; no independent geographic verification or new user access check claimed.
- **Supporting location:** “Changing the number of shots”: finite-shot simulation, exact simulator expectation with `shots=None`, and sample-mean convergence.

## arrazola2021-noisy-circuits

[7] J. M. Arrazola, “Noisy circuits,” *PennyLane Demos*, Feb. 21, 2021, updated Jun. 19, 2026. Accessed: Nov. 22, 2026. [Online]. Available: https://pennylane.ai/demos/tutorial_noisy_circuits/

- **Type/metadata:** Official tutorial; author, title, publication/update dates, and URL checked on the page. No DOI asserted. Key reused from the ML evaluation draft.
- **Public full text:** [Noisy circuits](https://pennylane.ai/demos/tutorial_noisy_circuits/).
- **Access check:** 2026-11-22: relevant tutorial text opened; the user previously reported this page accessible. No independent Cambodia test claimed.
- **Supporting location:** “Noisy operations”: mixed-state channel description, bit-flip example, and depolarizing Kraus operators with total nonidentity-Pauli probability $u$ (the tutorial uses $p$).
- **Boundary:** Compiled-gate placement, independent local errors after two-qubit gates, and the campaign design are project proposals, not experiments reported by the tutorial.

## pennylane-default-mixed

[8] PennyLane developers, “DefaultMixed,” *PennyLane Documentation*. Accessed: Nov. 22, 2026. [Online]. Available: https://docs.pennylane.ai/en/stable/code/api/pennylane.devices.DefaultMixed.html

- **Type/version:** Official API, observed PennyLane 0.45.1; class identity and URL checked. No publication date or DOI asserted. Key reused from the ML evaluation draft.
- **Public full text:** [DefaultMixed](https://docs.pennylane.ai/en/stable/code/api/pennylane.devices.DefaultMixed.html).
- **Access check:** 2026-11-22: class description and parameter text opened. The user previously reported the page accessible; no independent Cambodia test claimed.
- **Supporting location:** Class Parameters, `readout_prob`: a probability of readout error for qubit measurements.
- **Boundary:** The independent symmetric assignment matrix in the content is an explicit project-model proposal; it is not a measured property of a physical backend.

## cai2023-quantum-error-mitigation

[9] Z. Cai et al., “Quantum error mitigation,” *Reviews of Modern Physics*, vol. 95, no. 4, Art. no. 045005, Dec. 2023, doi: [10.1103/RevModPhys.95.045005](https://doi.org/10.1103/RevModPhys.95.045005).

- **Verified metadata:** Title, authors, journal, volume, issue, article number, publication date (Dec. 13, 2023), and DOI checked on the [APS publication record](https://journals.aps.org/rmp/abstract/10.1103/RevModPhys.95.045005). Full author list: Zhenyu Cai, Ryan Babbush, Simon C. Benjamin, Suguru Endo, William J. Huggins, Ying Li, Jarrod R. McClean, and Thomas E. O'Brien.
- **Version read:** Supplied `error-mitigation.pdf` is the 43-page author manuscript arXiv:2210.00921v3, revised Dec. 28, 2023; its title page carries a Jan. 1, 2024 document date. That internal date is not the journal's publication year. The manuscript and journal article represent the same review publication and are not counted as separate evidence.
- **Public full text:** [Versioned arXiv PDF](https://arxiv.org/pdf/2210.00921v3); [version/metadata record](https://arxiv.org/abs/2210.00921v3).
- **Access check:** 2026-11-24: local PDF extracted and targeted passages read; public 43-page arXiv PDF and record opened. APS metadata was readable, but the page requests authorization for full article text; publisher full-text access is not claimed. No Cambodia access test performed.
- **Supporting locations, manuscript pagination:** Sections II.A–B, pp. 3–4, Eqs. (1)–(5): ideal expectation, finite shots, bias/variance, MSE and sampling overhead. Section III.A, pp. 6–7, Eqs. (9)–(11): ZNE, linear/Richardson extrapolation, approximation bias, sampling cost, and noise-amplification assumptions/gate-insertion limits. Section IV.A, pp. 21–22: calibration versus response measurement and use-case-specific comparisons.
- **Reading/type boundary:** Review article and targeted reading, not an additional primary QAOA experiment or a claim that all 43 pages were comprehensively reviewed. Other mitigation techniques in the survey are not adopted here.

## mitiq-zne-guide

[10] Mitiq developers, “Zero-noise extrapolation,” *Mitiq Documentation*. Accessed: Nov. 24, 2026. [Online]. Available: https://mitiq.readthedocs.io/en/stable/guide/zne.html

- **Type/version:** Official user guide, observed Mitiq 1.1.0. Title, functions, and options below checked in the documentation. No page publication date or DOI asserted; this is supporting software documentation, not primary experimental evidence.
- **Public full text:** [Overview](https://mitiq.readthedocs.io/en/stable/guide/zne.html), [introduction](https://mitiq.readthedocs.io/en/stable/guide/zne-1-intro.html), [options](https://mitiq.readthedocs.io/en/stable/guide/zne-3-options.html), [theory](https://mitiq.readthedocs.io/en/stable/guide/zne-5-theory.html), [use-case limitations](https://mitiq.readthedocs.io/en/stable/guide/zne-2-use-case.html), and [noise-scaling example](https://mitiq.readthedocs.io/en/stable/examples/scaling.html).
- **Access check:** 2026-11-24: these official pages opened and relevant text read. Access from the user's connection or Cambodia has not been verified.
- **Supporting locations:** Options, “Unitary Folding,” “The special case of odd integer scale factors,” and “Extrapolation methods: Factory objects”: `fold_global`, ideal-equivalent insertion, and `LinearFactory(scale_factors=[1, 3])`. Theory, Steps 1–2: noise amplification and extrapolated observable. Use-case page, Disadvantages: bias/model mismatch. Noise-scaling example, Warning: inverse gates can have different error models and produce nonlinear scaling. The options examples include exact density-matrix calculations; those are not finite-shot project runs.
- **Boundary:** The frozen-parameter role, gate-only comparison, equal-shot allocation, variance/resource calculations, and diagnostic count are project proposals. The documentation does not establish integration with this repository or mitigation of its clustering outputs.

## scikit-learn-silhouette-score

[11] scikit-learn developers, “silhouette_score,” *scikit-learn Documentation*. Accessed: Nov. 25, 2026. [Online]. Available: https://scikit-learn.org/stable/modules/generated/sklearn.metrics.silhouette_score.html

- **Type/version:** Official API documentation, observed scikit-learn 1.9.1. No page publication date or DOI asserted. The stable key is reused from the ML evaluation draft for the same page.
- **Public full text:** [silhouette_score](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.silhouette_score.html).
- **Access check:** 2026-11-25: page opened and relevant definition/parameter text read in the assistant's browsing environment. Access from Cambodia has not been verified.
- **Supporting locations:** Definition: mean coefficient, interpretation, and $2\leq k\leq n-1$ condition. Parameters: feature/distance input and predicted labels. Shared original-space scoring and exclusion from tuning are project protocol choices.

## scikit-learn-adjusted-rand-score

[12] scikit-learn developers, “adjusted_rand_score,” *scikit-learn Documentation*. Accessed: Nov. 25, 2026. [Online]. Available: https://scikit-learn.org/stable/modules/generated/sklearn.metrics.adjusted_rand_score.html

- **Type/version:** Official API documentation, observed scikit-learn 1.9.1. No page publication date or DOI asserted.
- **Public full text:** [adjusted_rand_score](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.adjusted_rand_score.html).
- **Access check:** 2026-11-25: page opened and relevant definition, parameter, and label-permutation text read in the assistant's browsing environment. Access from Cambodia has not been verified.
- **Supporting locations:** Definition: chance-adjusted partition agreement and invariance under cluster-label permutation. Parameters: reference and predicted label arrays. The choice of diagnosis labels as the project's evaluation reference is a protocol decision; this page does not identify or validate the project's dataset.

## Citation-to-Claim Map

| Local citation | Supported literature or software claim in `content.md` |
|---|---|
| [1] | QAOA state/parameter definitions, maximized expectation, diagonal measurement context, and integer-spectrum angle-period qualification |
| [2] | Empirical study setup, condition-dependent optimizer findings, abstract/Results qualification, and limited transfer to clustering |
| [3] | COBYLA's local interpolation/trust-region description, official support, evaluation-cap semantics, and implementation change |
| [4] | SPSA equations, two/$2q$ calls, central-difference contrast, theoretical assumptions, and nonquantum evidence limits |
| [5] | PennyLane schedule support, forward-cost overhead/return semantics, and inspected initialization behavior |
| [6] | Finite-shot sampling versus exact supported simulator expectations |
| [7] | Noisy-state channels and the chosen depolarizing probability convention |
| [8] | Documented readout-error configuration support |
| [9] | Error-mitigated expectations, bias/variance tradeoff, ZNE assumptions, amplification limits, calibration costs, and use-case-specific evidence |
| [10] | Official folding/linear-fit support, relative scale-factor interpretation, extrapolation workflow, and documented failure mechanisms |
| [11] | Silhouette inputs, interpretation, and partition-validity condition |
| [12] | ARI inputs, chance-adjusted reference agreement, and label-permutation invariance |

The common-loss interface, sampling-variance calculation, budget equations, initialization/instance controls, return policy, synthetic channel placement, readout matrix, ZNE diagnostic design, two-point intercept and variance formulas, matched-resource accounting, selected headline outputs, runtime boundary, reporting rules, and open agreements are explicitly proposed choices or derived calculations. The breast cancer dataset choice and availability of diagnosis labels come from the project specification; its exact variant, metadata, and coding remain to be recorded. These choices are not published findings or measured project results. No figures are used, so no figure attribution or relative-image links are required.
