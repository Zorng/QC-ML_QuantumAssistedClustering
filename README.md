# Quantum Assisted Clustering

A shared Machine Learning and Quantum Computing course project using graph-based clustering with QAOA. One implementation and set of experiment results supports two separate reports.

## Structure

```text
src/quantum_clustering/     Core algorithms and shared experiment runner
app/                       Interactive application
configs/                   Experiment configurations
data/                     Small datasets and provenance notes
tests/                    Correctness and integration tests
results/                  Generated run artifacts, figures, and tables
docs/                     Mathematical design, literature review, and decisions
reports/
   machine_learning/       ML report sources
   quantum_computing/      Quantum Computing report sources
```

Both reports use the same artifacts from `results/`. Keep application code separate from the core algorithms. Add modules within the package as they are implemented rather than creating empty modules in advance.

## Current status

Folder structure only; dependencies and algorithms are not implemented yet. The first working milestone is classical PCA, graph construction, and a small QAOA clustering demonstration. Quantum PCA requires a documented design, and experimental optimizer choices remain open pending literature review. Simulated annealing is future work.
