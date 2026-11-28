This chapter introduces the study of quantum-assisted clustering and the reasons for comparing it with classical approaches. It outlines the central research problem, the practical constraints that shape the investigation, and the project goal and objectives that guide the work.

## Presentation of Project

Quantum Assisted Clustering investigates how quantum computing techniques can contribute to a clustering pipeline. Clustering is an unsupervised machine learning task: it groups samples according to their features without using known class labels to form the groups. The core project studies two-group assignments using a similarity graph, in which each sample is a vertex and weighted connections express how similar pairs of samples are. The implementation and experimental results are shared by the Machine Learning and Quantum Computing reports; this report emphasizes the quantum representations, algorithms, and resources.

The planned quantum-assisted approach expresses a graph partition as a binary optimization problem. Its objective penalizes separating similar samples and adds a soft preference for balanced group sizes. This objective can be represented as a quadratic unconstrained binary optimization (QUBO) problem and mapped to an Ising cost Hamiltonian, whose low-energy states correspond to low-cost assignments. The Quantum Approximate Optimization Algorithm (QAOA) uses alternating parameterized quantum operations to search for approximate solutions, with classical computation used to select the parameters [[1]](ref.md#farhi2014-qaoa). A measured bit string represents a candidate grouping.

## Problematic

The project must determine whether a quantum-assisted pipeline can produce meaningful clustering results under clearly stated practical constraints. A low graph objective alone does not establish good clusters: the result depends on feature scaling, the similarity graph, and the balance penalty. A soft penalty can also allow a single-group assignment, which must be reported as invalid for two-cluster evaluation.

Classical principal component analysis (PCA) reduces the number of feature dimensions by projecting data onto directions of high variation. Quantum PCA approaches seek related spectral information using quantum states. The method proposed by Lloyd, Mohseni, and Rebentrost assumes access to copies of a quantum state and produces information about its principal directions in quantum form [[2]](ref.md#lloyd2014-qpca). Applying such ideas to classical data also requires accounting for state preparation and obtaining usable classical coordinates for graph construction. A phase-estimation circuit alone does not complete that pipeline, and the costs of preparation, measurement, and reconstruction may be substantial.

QAOA results also depend on circuit depth, parameter optimization, and measurement. A finite number of measurements introduces sampling variability even in an ideal simulator; simulated gate or readout noise is a separate effect. Optimizing an expected cost does not guarantee a globally optimal assignment, and the measured assignment returned by a selection policy has its own cost. Simulation results must be identified as such and cannot be presented as measurements from quantum hardware.

## Project Goal

The goal is to develop and evaluate a reproducible experimental system for graph-based clustering that compares classical methods with quantum-assisted approaches. The study will examine solution quality, computational resources, and the effects of preprocessing and execution settings under comparable conditions. It is an investigation of feasibility and behavior, with no assumed practical quantum advantage.

A planned interactive application will use a FastAPI backend and React frontend, with prepared experiments and defaults for ordinary use and advanced controls for detailed studies. It will support inspection of configurations and results, while run identifiers, configurations, seeds, and development versions provide traceability. Reusable algorithms will be maintained separately from the interface in the development repository. A secondary semi-supervised binary classification experiment belongs to a separate application mode and does not replace the core clustering study.

## Project Objectives

The project objectives and their expected outputs are summarized in Table X.

Table X. Project objectives and expected outputs

| Objective | Expected output |
| --- | --- |
| Formulate the similarity-graph objective as QUBO and Ising representations. | Equivalent objective costs verified on small assignments. |
| Compare classical and quantum PCA on common data. | Comparison of projected representations and accuracy, with inputs, outputs, precision, preparation, readout, and resource costs documented. |
| Evaluate QAOA with literature-informed classical optimizers. | Optimizer comparisons recording expected cost, returned assignment cost, sampling, and resource budgets. |
| Assess clustering against K-Means and spectral clustering baselines and study simulation conditions. | Comparisons under shared evaluation conditions, including ideal finite-shot and parameterized noisy simulations. |
| Provide an interactive and reproducible experiment workflow. | Application controls and result views, with traceable exports for both reports. |
