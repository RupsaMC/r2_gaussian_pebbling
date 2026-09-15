# r2_gaussian_pebbling
# Adaptive Pebbling for Graph-Laplacian Regularization

This repository contains experimental code for studying localized **pebbling** strategies for graph-Laplacian regularization on 3D Gaussian graphs.

The work was developed in the context of the R²-Gaussian / synthetic X-3D reconstruction setting.

## Research question

Can global graph-Laplacian regularization be approximated more efficiently by applying updates only to selected local graph regions ("pebbles")?

A pebble is formed by selecting a graph node (a pebble center) and expanding to its local KNN neighbourhood. The experiments study how different rules for selecting pebble centers affect optimization quality at a fixed computational/edge budget.
## Graph formulation

The experiments use a symmetrized KNN graph over 3D Gaussian splats.

Each graph edge is assigned a distance-based weight using the 3D distance
between the corresponding Gaussian splats. The weighting parameter is k=6.

The graph regularization penalizes differences in Gaussian density between
connected nodes, weighted by their spatial proximity.

The main experiments use a quadratic optimization surrogate to study how
different pebble-selection strategies affect localized graph regularization.

## Experimental progression

The experiments proceed from simple to more adaptive pebbling strategies:

1. **Toy pebbling**
   - Initial proof-of-concept for localized graph updates.

2. **Boundary-aware weighted pebbling**
   - Uses the actual weighted KNN graph.
   - Includes edges crossing the boundary of a selected pebble region.

3. **Adaptive disagreement-based pebbling**
   - Pebble centers are selected according to local graph disagreement.
   - High-disagreement nodes correspond to regions where neighbouring Gaussian
     densities differ strongly.
4. **Selection-policy comparison**
   - Most-dissimilar regions
   - Least-dissimilar regions
   - Mixed high/low-disagreement regions
   - Middle-disagreement regions
   - Random pebbling

5. **Spatially distributed disagreement-aware pebbling**
   - High-disagreement candidates are selected while encouraging spatial
     separation between pebble centers.

6. **High-disagreement + farthest-point pebbling**
   - Candidate centers are restricted to high-disagreement nodes.
   - Farthest-point sampling is then used to distribute the selected pebbles
     across the 3D Gaussian field.

## Main observations so far

The experiments suggest that:

* Pure high-disagreement selection is substantially better than selecting low-disagreement regions.
* Random pebbling can outperform pure disagreement-based selection at matched edge budgets.
* This suggests that broad spatial coverage may be an important component of effective pebbling.
* Adding an explicit spatial-separation constraint to high-disagreement selection substantially improves performance.
* The strongest current hypothesis is that useful pebble selection depends on both **local disagreement** and **spatial coverage**, rather than disagreement alone.

These results are currently established on the graph/optimization component rather than through a complete end-to-end R²-Gaussian training run.

## Latest experiment

The current comparison is:

**High-disagreement + farthest-point spatial selection**

versus

**Random pebbling**

at matched approximate edge budgets of:

* 16,000 edges
* 50,000 edges
* 75,000 edges

The experiments also record the spatial separation of selected pebble centers.

## Repository structure

```text
r2-gaussian-pebbling/
├── README.md
├── experiments/
│   ├── pebble_toy_real_graph.py
│   ├── pebble_weighted_boundary.py
│   ├── multi_pebble_weighted_boundary.py
│   ├── pebble_vs_random.py
│   ├── energy_vs_edge_budget.py
│   ├── pebble_optimization.py
│   ├── pebble_optimization_matched_budget.py
│   ├── pebble_selection_comparison.py
│   ├── pebble_spread_vs_random.py
│   └── pebble_highD_farthest_vs_random.py
├── results/
│   ├── figures/
│   └── tables/
└── docs/
    └── experimental_findings.pdf
```text

## Dataset and environment

Experiments were performed using the synthetic X-3D dataset and the R²-Gaussian software environment.

The full dataset is **not included** in this repository.

The computational environment used:

* Python 3.9
* PyTorch 2.1.2 + CUDA 11.8
* NVIDIA RTX 3050 6GB Laptop GPU
* PyG / KNN graph processing
* R²-Gaussian CUDA extensions
* TIGRE

## Limitations

The current study focuses on the graph-regularization and pebbling components.

A complete end-to-end evaluation inside the full R²-Gaussian reconstruction pipeline remains future work because of GPU memory constraints on the available hardware.

## Status

This repository contains exploratory research code and experimental results. The methods and hypotheses are still under investigation.

