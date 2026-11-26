# References: Preprocessing and Dimensionality Reduction

Citation keys are stable and used as `[@key]` in `content.md`. Final numbering is assigned during assembly in `06_references/`.

## Primary sources

### `jolliffe2016`

- Authors: Ian T. Jolliffe, Jorge Cadima
- Title: Principal component analysis: a review and recent developments
- Venue: *Philosophical Transactions of the Royal Society A: Mathematical, Physical and Engineering Sciences*, 374(2065), article 20150202
- Year: 2016 (published 13 April 2016)
- DOI: [10.1098/rsta.2015.0202](https://doi.org/10.1098/rsta.2015.0202)
- Open access: [PMC4792409](https://pmc.ncbi.nlm.nih.gov/articles/PMC4792409/)
- Relevant pages/sections: to record after full reading (definition and SVD formulation; covariance versus correlation matrix PCA; choosing the number of components)
- Verification: DOI, volume, issue and article number checked against PubMed Central and NASA ADS records.

### `milligan1988`

- Authors: Glenn W. Milligan, Martha C. Cooper
- Title: A study of standardization of variables in cluster analysis
- Venue: *Journal of Classification*, 5(2), 181–204
- Year: 1988
- DOI: [10.1007/BF01897163](https://doi.org/10.1007/BF01897163)
- Relevant pages: to record after full reading (standardization formulas and recovery results)
- Verification: DOI, volume, issue and pages checked against the RePEc/EconPapers record. Full text is behind a Springer paywall.

### `yeung2001`

- Authors: Ka Yee Yeung, Walter L. Ruzzo
- Title: Principal component analysis for clustering gene expression data
- Venue: *Bioinformatics*, 17(9), 763–774
- Year: 2001
- DOI: [10.1093/bioinformatics/17.9.763](https://doi.org/10.1093/bioinformatics/17.9.763)
- Author copy: [University of Washington](https://homes.cs.washington.edu/~ruzzo/papers/pca-bioinf.html)
- Relevant pages: to record after full reading (experimental design; results comparing clustering on original variables and on principal components)
- Verification: DOI, volume, issue and pages checked against DBLP and the University of Washington Tacoma repository.

## Supporting sources

### `chang1983`

- Author: Wei-Chien Chang
- Title: On using principal components before separating a mixture of two multivariate normal distributions
- Venue: *Journal of the Royal Statistical Society, Series C (Applied Statistics)*, 32(3), 267–275
- Year: 1983
- DOI: [10.2307/2347949](https://doi.org/10.2307/2347949)
- Relevant pages: to record after full reading
- Verification: DOI, volume, issue and pages checked against the RePEc/IDEAS record.

### `sklearn-scaler`

- Author: scikit-learn developers
- Title: `sklearn.preprocessing.StandardScaler` (API documentation)
- URL: <https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html>
- Accessed: 2026-11-18
- Note: states that a zero-variance feature is left as-is, with a scaling factor of 1.

### `sklearn-pca`

- Author: scikit-learn developers
- Title: `sklearn.decomposition.PCA` (API documentation)
- URL: <https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html>
- Accessed: 2026-11-18
- Note: input is centered but not scaled before the SVD.

### `sklearn-variance-threshold`

- Author: scikit-learn developers
- Title: `sklearn.feature_selection.VarianceThreshold` (API documentation)
- URL: <https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.VarianceThreshold.html>
- Accessed: 2026-11-18

## Dataset

### `wolberg1993`

- Authors: William Wolberg, Olvi Mangasarian, Nick Street, W. Street
- Title: Breast Cancer Wisconsin (Diagnostic) [Dataset]
- Publisher: UCI Machine Learning Repository, licence CC BY 4.0
- Year: 1993
- DOI: [10.24432/C5DW2B](https://doi.org/10.24432/C5DW2B)
- Note: same entry as the project proposal reference list.
