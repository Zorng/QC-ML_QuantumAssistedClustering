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
- `notes.md`: raw notes, outlines, questions, task assignments, and evidence to develop into prose.
- `ref.md`: sources cited in this section, including author, title, year, DOI or URL, and a stable citation key. Record relevant pages when available and verify references before using them.
- `NN_figures/`: figures appearing in this section, where `NN` matches its folder prefix, such as `03_detail_concept/03_figures/`. Use descriptive filenames and record captions and attribution in `content.md`.

These files start empty so contributors can write their own material. Existing content must be preserved.

Example relative figure link from a section's `content.md`:

```markdown
![Description of the clustering pipeline](03_figures/clustering_pipeline.png)

Figure [number assigned during assembly]: Clustering pipeline. Source: authors.
```

Use the appropriate figure directory for the section. Assign final figure, table, and citation numbers consistently during assembly; the example report numbers figures consecutively across sections.

## References and shared results

Collect section sources in each `ref.md`. Consolidate and deduplicate them into `06_references/content.md` for the final bibliography. In the references section itself, `notes.md` and `ref.md` can hold bibliography cleanup notes and source metadata; its figure folder may remain unused.

Keep authoritative experiment outputs in the shared `results/` directory. When placing an exported figure in a section folder, record its run identifier, configuration, and source artifact path in `notes.md`. Both reports must use the same values for the same run.

## Course emphasis

- Machine Learning: preprocessing, graph design, clustering baselines, evaluation, and stability.
- Quantum Computing: QUBO/Ising mapping, QAOA, quantum PCA assumptions, optimizer comparisons, noise, and circuit resources.

Use Introduction subsections from the example: Presentation of Project, Problematic, Project Goal, and Project Objectives. Conclusion can follow Completed Tasks, Experiences and Challenges, and Future Works. Adapt other subsections to this clustering project; do not copy BB84-specific topics. Include experimental results and evaluation within Implementation, with course-appropriate discussion. Never present planned work as completed results.
