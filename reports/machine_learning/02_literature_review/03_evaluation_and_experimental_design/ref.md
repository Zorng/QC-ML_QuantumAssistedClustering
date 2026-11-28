# Local References and Verification

Numbers follow first appearance in `content.md`; keys identify sources independently of those numbers. Linked citation labels retain literal square brackets in rendered Markdown, for example `[[1]](ref.md#rousseeuw1987-silhouettes)`. No report-wide numbering is assigned here.

## rousseeuw1987-silhouettes

[1] P. J. Rousseeuw, “Silhouettes: A graphical aid to the interpretation and validation of cluster analysis,” *Journal of Computational and Applied Mathematics*, vol. 20, pp. 53–65, Nov. 1987, doi: 10.1016/0377-0427(87)90125-7.

- **Publication identity:** Published journal article; the university-hosted PDF reproduces the article. No preprint is cited as a separate publication.
- **DOI:** [10.1016/0377-0427(87)90125-7](https://doi.org/10.1016/0377-0427(87)90125-7).
- **Publisher record:** [ScienceDirect article](https://www.sciencedirect.com/science/article/pii/0377042787901257).
- **Publicly readable full text:** [KU Leuven PDF](https://wis.kuleuven.be/statdatascience/robust/papers/publications-1987/rousseeuw-silhouettes-jcam-sciencedirectopenarchiv.pdf).
- **Metadata verification:** Author, title, journal, volume, year, and pages checked against the PDF. November publication date and DOI cross-checked against the publisher's indexed search record. Direct DOI and publisher-page requests returned HTTP 403 in the browsing environment; successful resolution of those pages is not claimed.
- **Access check:** 2026-11-18. The public PDF opened and its text was readable (13 pages). Full text was read during the review. Checks were made from the assistant's browsing environment; access from Cambodia has not been verified.
- **Claim locations:** Algorithm independence and aggregation: Sec. 2, p. 59. Assumptions and singleton convention: pp. 55–56. Plot: pp. 57–59. Outlier caution: Sec. 3, pp. 60–61.

## scikit-learn-silhouette-score

[2] scikit-learn developers, “silhouette_score,” *scikit-learn documentation*. Accessed: Nov. 18, 2026. [Online]. Available: https://scikit-learn.org/stable/modules/generated/sklearn.metrics.silhouette_score.html

- **Source type:** Official API documentation, supplementary to the method paper; not a primary research publication.
- **Publication metadata:** Page title and function identity checked on the official website. No publication date or DOI is asserted. The `/stable/` page is mutable; the eventual software version remains to be selected.
- **Publicly readable full text:** [Official API reference](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.silhouette_score.html).
- **Access check:** 2026-11-18. HTML opened and the definition and parameter descriptions were readable from the assistant's browsing environment. Access from Cambodia has not been verified.
- **Claim locations:** Opening definition: equation, interpretation, mean aggregation, and valid label-count range. Parameters: `metric`, `sample_size`, and `random_state`.

## davies1979-cluster-separation

[3] D. L. Davies and D. W. Bouldin, “A cluster separation measure,” *IEEE Transactions on Pattern Analysis and Machine Intelligence*, vol. PAMI-1, no. 2, pp. 224–227, Apr. 1979, doi: 10.1109/TPAMI.1979.4766909.

- **Publication identity:** Published journal article. The downloaded scan and publicly readable PDF reproduce the article; no separate preprint version is asserted.
- **DOI:** [10.1109/TPAMI.1979.4766909](https://doi.org/10.1109/TPAMI.1979.4766909).
- **Publisher record:** [IEEE Xplore, document 4766909](https://ieeexplore.ieee.org/document/4766909/).
- **Publicly readable full text:** [PDF hosted under Don Bouldin's ResearchGate profile](https://www.researchgate.net/profile/Don_Bouldin/publication/224377470_A_Cluster_Separation_Measure/links/02e7e52d57df1ad121000000.pdf).
- **Local copy inspected:** `D:\ML-QC-Clustering\cluster-pami.pdf` (four pages). This location is recorded as verification context, not used as a portable report link. The preceding article's references at the top of printed p. 224 are not part of this paper's bibliography.
- **Metadata verification:** Authors, title, journal, volume, issue, month, year, and printed page range checked in the local scan. DOI checked by requesting the DOI URL, which redirected to IEEE document 4766909. The publisher endpoint displayed a JavaScript/robot-verification page; publisher full-text access is not claimed.
- **Access check:** 2026-11-18. The local PDF was read, and rendered page images were inspected to confirm scanned content and equations. The ResearchGate PDF also opened as a readable four-page document in the assistant's browsing environment. Access from Cambodia has not been verified.
- **Claim locations:** Center-based assumptions: abstract and theoretical formulation, p. 224. Ratio and aggregation: Definitions 4–5, p. 225. Euclidean and dispersion choices: p. 225. Degeneracies and singleton policy: pp. 225–226. Four-point example: Fig. 1, p. 225. ISODATA and hierarchy: Figs. 2–3, p. 226. Iris: Fig. 5 and discussion, p. 227.

## hubert1985-comparing-partitions

[4] L. Hubert and P. Arabie, “Comparing partitions,” *Journal of Classification*, vol. 2, pp. 193–218, Dec. 1985, doi: 10.1007/BF01908075.

- **Publication identity:** Published journal article. The local scan and publicly readable ResearchGate PDF reproduce the article; later matrix and triple-based extensions within the paper are not separate ARI versions.
- **DOI:** [10.1007/BF01908075](https://doi.org/10.1007/BF01908075).
- **Publisher record:** [Springer article](https://link.springer.com/article/10.1007/BF01908075).
- **Publicly readable full text:** [PDF hosted under Lawrence Hubert's ResearchGate profile](https://www.researchgate.net/profile/Lawrence-Hubert/publication/24056046_Comparing_partitions/links/6883dac107869379845496e7/Comparing-partitions.pdf).
- **Local copy inspected:** `D:\ML-QC-Clustering\comparing_partitions.pdf` (26 pages). This is verification context rather than a portable report link.
- **Metadata verification:** Authors, title, journal, volume, year, and printed page range checked against the scanned first page. Phipps Arabie's name was checked visually because OCR misreads it as “Arabic.” DOI and December publication date checked against the publisher record; the DOI request redirected to that record. The publisher page offers subscription/purchase access; its abstract and metadata were readable, but publisher full-text access is not claimed.
- **Access check:** 2026-11-18. The local PDF was readable; the title page, Eq. (5), and Table 3 were checked visually. The ResearchGate link also opened as a readable 26-page PDF from the assistant's browsing environment. Access from Cambodia has not been verified.
- **Reading scope:** Introduction and Sections 2–2.2, printed pp. 193–201, read for this synthesis. Later extensions were skimmed; the review does not claim a complete technical reading of those extensions.
- **Claim locations:** Same observation set, contingency table, and pairwise agreement: Section 2, pp. 194–196. Fixed-size null model: Section 2.1, p. 197. Expected RI and correction: Eqs. (3)–(5), p. 198. Normalization and alternative models: pp. 199–200. Worked comparison of corrections: Table 3 and discussion, p. 201. Label-permutation invariance and symmetry follow from these pair-count and contingency-table formulas.

## benhur2002-stability

[5] A. Ben-Hur, A. Elisseeff, and I. Guyon, “A stability based method for discovering structure in clustered data,” in *Pacific Symposium on Biocomputing*, vol. 7, 2002, pp. 6–17. [Online]. Available: https://psb.stanford.edu/psb-online/proceedings/psb02/benhur.pdf

- **Publication identity:** Conference proceedings paper. The local file and official proceedings PDF contain the 12-page article; no distinct preprint publication is cited.
- **Verified publication metadata:** Authors, title, proceedings name, volume 7, year 2002, and pages 6–17 checked against the [official PSB 2002 contents](https://psb.stanford.edu/psb-online/proceedings/psb02/). The article text also identifies the authors and title. No DOI is asserted because one has not been independently verified for this entry.
- **Publicly readable full text:** [Official proceedings PDF hosted by Stanford](https://psb.stanford.edu/psb-online/proceedings/psb02/benhur.pdf).
- **Local copy inspected:** `D:\ML-QC-Clustering\benhur.pdf` (12 pages). This is verification context rather than a portable report link.
- **Access check:** 2026-11-18. The local text was read, including the procedure, experimental discussion, figure captions, and conclusion. The official full-text PDF and proceedings contents also opened and were readable in the assistant's browsing environment. Access from Cambodia has not been verified.
- **Claim locations (PDF page numbers):** Distribution rather than mean alone: introduction, pp. 1–2. Similarity measures: Section 2, pp. 2–4. Paired subsamples and shared observations: Section 3 and Fig. 2, pp. 4–5. Trivial one-cluster case and sampling warning: Remark 3.1, p. 5. Experimental algorithm and outlier heuristic: Section 4, pp. 5–6. Gaussian example: Fig. 3 and discussion, p. 6. Yeast experiment: pp. 6–8. Uniform example and comparisons: Fig. 7 and Table 1, pp. 9–10. Cost and illustrative evidence limits: Sections 5–6, pp. 10–11.
- **Adaptation boundary:** The deferred extension's between-run ARI uses the partition-agreement definition from [4], not the similarity score reported in this paper. Any future overlap thresholds, pair budgets, and seed controls are project choices, not published findings. Resampling-stability experiments are not scheduled in the initial study.

## pennylane-measurements

[6] PennyLane developers, “Measurements,” *PennyLane Documentation*. Accessed: Nov. 18, 2026. [Online]. Available: https://docs.pennylane.ai/en/stable/introduction/measurements.html

- **Type and metadata:** Official guide; title and URL checked. No publication date or DOI asserted. The stable page is mutable; no dependency is selected.
- **Publicly readable full text:** [Measurements](https://docs.pennylane.ai/en/stable/introduction/measurements.html).
- **Access check:** 2026-11-18. Relevant text opened in the assistant's browsing environment. The user separately confirmed this page opens from their connection in Cambodia; this is user-reported access, not an independent geographic test.
- **Supporting locations:** Measurement definitions and stochastic-results note; Counts; Changing the number of shots.

## arrazola2021-noisy-circuits

[7] J. M. Arrazola, “Noisy circuits,” *PennyLane Demos*, Feb. 21, 2021, updated Jun. 19, 2026. Accessed: Nov. 18, 2026. [Online]. Available: https://pennylane.ai/demos/tutorial_noisy_circuits/

- **Type and metadata:** Official tutorial; author, title, publication/update dates, and URL checked on the page. No DOI asserted.
- **Publicly readable full text:** [Noisy circuits](https://pennylane.ai/demos/tutorial_noisy_circuits/).
- **Access check:** 2026-11-18. Relevant text opened in the assistant's browsing environment. The user confirmed this page opens from their connection in Cambodia; no independent geographic test is claimed.
- **Supporting location:** Noisy operations, including imperfect gates and channel examples.

## pennylane-default-mixed

[8] PennyLane developers, “DefaultMixed,” *PennyLane Documentation*. Accessed: Nov. 18, 2026. [Online]. Available: https://docs.pennylane.ai/en/stable/code/api/pennylane.devices.DefaultMixed.html

- **Type and metadata:** Official API reference; class identity and URL checked. No publication date or DOI asserted.
- **Publicly readable full text:** [DefaultMixed](https://docs.pennylane.ai/en/stable/code/api/pennylane.devices.DefaultMixed.html).
- **Access check:** 2026-11-18. Relevant text opened in the assistant's browsing environment. The user confirmed this page opens from their connection in Cambodia; no independent geographic test is claimed.
- **Supporting location:** Class Parameters, specifically `readout_prob` for measurement errors.

## Access-related exclusions

IBM documentation is excluded from the numbered references because the user reported being unable to access it from their connection in Cambodia on 2026-11-18. This records a source-access constraint, not a claim that country-wide availability was independently tested. All three replacement pages above were subsequently confirmed accessible by the user.

## Citation-to-Claim Check

| Local citation | Material in `content.md` | Supporting location |
|---|---|---|
| [1] | Construction context, display, assumptions, and caution | Locations listed under `rousseeuw1987-silhouettes` |
| [2] | Formula, interpretation, API validity, and metric subsampling | Definition and Parameters listed under `scikit-learn-silhouette-score` |
| [3] | DBI construction and specialization, equal cluster weighting, degeneracies, and published Iris example | Definitions 4–5 and following discussion, p. 225; Iris discussion, p. 227 |
| [4] | Pairwise agreement, fixed-size chance correction, interpretation, symmetry, and label-permutation invariance | Section 2, pp. 194–196; Section 2.1 and Eqs. (3)–(5), pp. 197–199 |
| [5] | Resampling stability, shared-observation comparison, distribution diagnostics, experimental scope, triviality and sampling limits, repeated-run cost | Sections 2–3, PDF pp. 2–5; Section 4, pp. 5–10; discussion, pp. 10–11 |
| [6] | Stochastic samples/counts and finite-shot simulation | Measurement definitions, stochastic-results note, Counts, and Changing the number of shots |
| [7] | Imperfect operations modeled by error channels | Noisy operations |
| [8] | A separate readout-error condition | Class Parameters: `readout_prob` |

The protocol tables and paragraphs introduced as proposals are project choices, not published experimental findings. The inference about curved or irregular groups is identified as an implication of the chosen geometry. Published metric and stability examples retain their source locations; the four-object ARI examples and similarity lists in `notes.md` remain constructed arithmetic illustrations, not project measurements. The selected initial outputs are Silhouette, external ARI, and runtime on the project's labeled breast cancer dataset. The dataset choice and diagnosis-label availability come from the project specification; exact identity/coding remain to be recorded. DBI and resampling stability retain their literature coverage and citations but are deferred from initial experiments. Timing boundaries, repetition budgets, seed organization, aggregation, resource accounting, and separate diagnostic execution are protocol choices. The sampling-versus-error interpretation is an inference from the documented mechanisms. No new source-access verification date or measured project result is asserted by this scope update.
