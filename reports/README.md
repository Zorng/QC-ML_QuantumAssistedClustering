# Report writing guide

Both reports follow the main section order of the previous year's QuCrypt report. The implementation, experiment run identifiers, and results are shared; the explanations emphasize the relevant course.

## Section order

| Folder | Report heading |
|---|---|
| `01_introduction` | I. Introduction |
| `02_literature_review` | II. Literature Review |
| `03_detail_concept` | III. Detail Concept |
| `04_implementation` | IV. Implementation |
| `05_conclusion` | V. Conclusion |
| `06_references` | References |
| `07_appendix` | Appendices |

The folder prefixes indicate assembly order. References and Appendices remain unnumbered report headings, as in the example. Add the cover page, table of contents, list of figures, and list of tables in the final report editor.

## Files within each section

- `content.md`: copy-ready prose, headings, equations, tables, figure links, captions, and citations for the final report. Keep drafting instructions out of this file.
- `notes.md`: raw research notes, outlines, technical questions, and evidence to develop into prose. Exclude task assignments and coordination notes.
- `ref.md`: sources cited in this section, including author, title, year, DOI or URL, and a stable citation key. Record relevant pages when available and verify references before using them.
- `NN_figures/`: figures appearing in this section, where `NN` matches its folder prefix, such as `03_detail_concept/03_figures/`. Use descriptive filenames and record captions and attribution in `content.md`.

These files start empty so contributors can write their own material. Existing content must be preserved.

Example relative figure link from a section's `content.md`:

```markdown
![Description of the clustering pipeline](03_figures/clustering_pipeline.png)

Figure [number assigned during assembly]: Clustering pipeline. Source: authors.
```

Use the appropriate figure directory for the section. Assign final figure, table, and citation numbers consistently during assembly; the example report numbers figures consecutively across sections.

## Subsection folders

For any section in either report, create topic or subsection folders when needed to organize distinct material or avoid concurrent edits to shared files. Reuse a suitable existing folder and avoid unnecessary nesting. Use numbered, descriptive topic names, never personal names or aliases.

Each subfolder follows the same structure: `content.md`, `notes.md`, `ref.md`, and `NN_figures/`. The figure prefix matches the parent report section number, not the subsection number. Keep topic material within its subfolder and use parent section files for later assembly and synthesis. Preserve existing content and update relative figure links when assembling or moving material.

## IEEE references during independent drafting

IEEE is required for both reports. Cite sources with square-bracket numbers before punctuation and provide IEEE-formatted bibliography entries. The format below is a project drafting convention; local numbers are provisional until report assembly.

Each topic uses its own `ref.md`. Number sources from `[1]` in first-citation order within that topic's `content.md`; repeat the same number for later citations of that source. Do not number sources by first appearance in reading notes. Sources read but not cited can remain in a separately marked reading-notes list and do not enter the final bibliography automatically.

Give every source a stable lowercase key such as `arthur2007-kmeanspp`, independent of its current citation number. Reuse a known key for the same publication where possible. In `content.md`, write:

```markdown
Initialization affects the result [[1]](ref.md#arthur2007-kmeanspp).
```

This displays a numbered citation while the link retains source identity. For multiple sources, use separate linked numbers. A locator can be included as `[[1, Sec. 2]](ref.md#arthur2007-kmeanspp)`.

In `ref.md`, use a key heading followed by the local IEEE entry and separate verification metadata:

```markdown
## arthur2007-kmeanspp

[1] D. Arthur and S. Vassilvitskii, “k-means++: The advantages of careful seeding,” in Proc. 18th Annu. ACM-SIAM Symp. Discrete Algorithms (SODA), 2007, pp. 1027–1035.

- Publication identity: title, authors, venue, and year above; add a DOI only after verification.
- Open full text: https://theory.stanford.edu/~sergei/papers/kMeansPP-soda.pdf
- Metadata record: https://research.google/pubs/k-means-the-advantages-of-careful-seeding/
- Access check: record the actual date and outcome; do not assume regional accessibility.
- Evidence locations: record the sections or pages supporting the cited claims, distinguishing PDF page indices from printed page numbers.
```

Format each entry for its source type, including verified author initials, title, venue, year, volume/issue/pages where applicable, and DOI or URL. Include access dates for web sources where required. Never invent missing fields. Keep access notes and internal keys outside the final bibliography text. Open manuscript and publisher links can describe the same publication; materially different versions must be identified and reconciled rather than automatically treated as identical.

For assembly, each report has its own first-citation numbering sequence and one entry per cited source. Topic-local `[1]` values can refer to different sources. Conversely, the same paper can have different local numbers and still become one entry. Preserve local files; renumber the assembled copy using publication identity, update relative links, and produce the final bibliography in `06_references/content.md`. The same source may have different final numbers in the ML and QC reports. Include citations in captions and tables when determining order.

Stable keys and links are drafting aids; the final submission uses ordinary IEEE citations and its consolidated bibliography. Before delivery, check citation-to-entry correspondence, duplicate publications, ordering, metadata, and any unresolved tokens. No automatic citation assembler is implemented yet.

Style source: [IEEE Reference Guide, 2018 edition, IEEE-hosted copy](https://edu.ieee.org/mx-udgvalles/wp-content/uploads/sites/121/IEEE-Reference-Guide.pdf). This is a checked reference, not a claim that it is the newest edition; follow any more specific teacher requirements.

## References and shared results

Collect section sources in each `ref.md` using the IEEE convention above. Consolidate and deduplicate them into `06_references/content.md` for the final bibliography. In the references section itself, `notes.md` and `ref.md` can hold bibliography cleanup notes and source metadata; its figure folder may remain unused.

Keep authoritative experiment outputs in the shared `results/` directory. When placing an exported figure in a section folder, record its run identifier, configuration, and source artifact path in `notes.md`. Both reports must use the same values for the same run.

## Course emphasis

- Machine Learning: preprocessing, graph design, clustering baselines, evaluation, and stability.
- Quantum Computing: QUBO/Ising mapping, QAOA, quantum PCA assumptions, optimizer comparisons, noise, and circuit resources.

Use Introduction subsections from the example: Presentation of Project, Problematic, Project Goal, and Project Objectives. Conclusion can follow Completed Tasks, Experiences and Challenges, and Future Works. Adapt other subsections to this clustering project; do not copy BB84-specific topics. Include experimental results and evaluation within Implementation, with course-appropriate discussion. Never present planned work as completed results.

## Topic-based literature review sources

The QC literature review has three topic directories inside `quantum_computing/02_literature_review/`:

- `01_clustering_and_qaoa/`
- `02_classical_and_quantum_pca/`
- `03_optimizers_and_noise/`

The ML literature review has three topic directories inside `machine_learning/02_literature_review/`:

- `01_preprocessing_and_dimensionality_reduction/`: scaling, classical PCA, retained variance, information loss, and effects on distance geometry.
- `02_clustering_and_similarity_graphs/`: K-Means, spectral clustering, similarity graph construction, and balance assumptions.
- `03_evaluation_and_experimental_design/`: internal and external metrics, stability, sampling, and reproducible comparisons.

Sources and reading notes can be cross-referenced between reports where topics overlap. Keep each report's synthesis specific to its subject: representation and clustering effects for ML, quantum methods and resources for QC.

Each contains `content.md`, `notes.md`, `ref.md`, and `02_figures/`. The figure prefix refers to report section 2, not the topic number. Topic files are the source material for their respective subsections; section-level files remain available for the assembled review, synthesis, and consolidated references.

Link topic figures relatively, for example `02_figures/similarity_graph.png`. When assembling prose into the section-level `content.md`, update the link to include the topic directory. Figures can remain in their original topic folders. Use stable citation keys to track references, deduplicate by verified publication identity, and assign final citation and figure numbers during assembly. Assembly is manual; no automatic report builder is implemented.
