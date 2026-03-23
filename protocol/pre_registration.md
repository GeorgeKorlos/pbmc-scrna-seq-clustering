# Single-Cell RNA-seq Clustering & Marker Identification

## Objective

To evaluate whether unsupervised clustering of a single-donor 10x Genomics PBMC scRNA-seq dataset (n=2,752 filtered cells) recovers major canonical immune cell populations using predefined QC, normalization, and clustering parameters without post-hoc parameter tuning.

This study does not aim to discover novel cell types or infer biological mechanisms, but to assess methodological robustness and reproducibility of standard single-cell analysis practices.

## Software Environment

* **Python**: 3.11.14
* **Scanpy**: 1.10.2
* **AnnData**: 0.10.8
* **Numpy**: 1.26.4
* **SciPy**: 1.11.4
* **Matplotlib**: 3.8.4
* **Seaborn**: 0.13.2
* **OS**: Ubuntu (WSL)
* **Random seed**: 42

## QC Thresholds

### Gene filtering

* Remove genes expressed in < 3 cells.

### Cell filtering

* Minimum genes per cell: 300
    - (Conservative, above 200 default, below 10th percentile 685)
* Maximum genes per cell: 2,500
    (~2.4× median; upper complexity filter)
* Maximum mitochondrial fraction: 10%
    - No adaptive tightening post hoc.

### Doublets

* No Scrublet in primary analysis.
* Doublet detection (Scrublet default) allowed only as sensitivity analysis.

## Normalization & Feature Selection

* Library size normalization to 10,000 counts per cell
* ```log1p``` transformation
* **HVG method**: Seurat flavor
* **HVG count**: 2,000

## Scaling & Regression

* Regress out:
    - pct_counts_mt
* do_not_regress: Cell cycle scores

This regression may partially remove biologically meaningful variation associated with RNA content differences across immune lineages.

## Dimensionality Reduction

* PCA on 2,000 HVGs
* Retain 40 PCs
    Forty principal components are retained as a fixed precommitment, consistent with common Scanpy workflows for ~3k PBMC datasets. PC count will not be adjusted based on elbow plot inspection.
* Graph constructed in PCA space (40 PCs)

## Graph & Clustering

* n_neighbors (k): 15
* Metric: Euclidean
    Euclidean distance in PCA space is used intentionally for consistency with standard Scanpy workflows.
* Clustering: Leiden
* Primary resolution: 1.0

Primary clustering resolution is fixed at 1.0.

Resolutions 0.5 and 1.5 will be computed as robustness checks only.

Resolution 1.0 results will be reported regardless of apparent biological interpretability, unless it produces <6 or >15 clusters, in which case this will be documented as methodological failure rather than adjusted.

Stability across resolutions will be assessed by:

* Consistency of major lineage marker enrichment.
* Adjusted Rand Index (ARI) between clusterings.
    - ARI will be computed on cluster assignments of the same filtered cell set using sklearn.metrics.adjusted_rand_score without manual relabeling.
    - ARI ≥ 0.7 between resolution 1.0 and adjacent resolutions (0.5 and 1.5) will be considered acceptable stability.
* Major lineage proportion change <25% across resolutions.
    - Proportion change will be calculated relative to resolution 1.0 cluster proportions.
    - Absolute deviation >25% will be considered instability.


## Differential Expression (Locked)

* Test: Wilcoxon
* Correction: Benjamini–Hochberg (FDR)
* Adjusted p < 0.05
* log2FC > 0.25
* Expressed in ≥ 25% of cells in cluster

Top 10 markers per cluster reported.

Differential expression will be performed on log-normalized counts prior to scaling and regression.


## Canonical Marker List

### Canonical PBMC Marker List 

#### T cells
* CD3D
* CD3E

#### CD4 T
* IL7R
* CCR7

#### CD8 T
* CD8A
* GZMA

#### NK
* NKG7
* GNLY

#### B cells
* MS4A1
* CD79A

#### Monocytes
* CD14
* FCGR3A
* LST1

#### Dendritic cells
* HLA-DRA
* ITGAX

#### Platelets
* PPBP
* PF4

Annotation rule:
≥2 markers from this preregistered list.

No expanding the list post hoc.

## Annotation Strategy

Major PBMC lineages only.

Assignment rule:
    * ≥ 2 canonical markers
    * adj p < 0.05
    * log2FC > 0.25
    * Expressed in ≥ 25% cells 

Ambiguous clusters:
    * Labeled "Putative X" or "Uncertain"
    * No forced labeling

## Success Criteria

* 6–15 biologically interpretable clusters
* Recovery of ≥4 of the following major lineages: T cells, B cells, NK cells, monocytes, dendritic cells.
* No cluster defined by mitochondrial genes
* Stability of major clusters across resolution sweep
* <25% change in major lineage size across resolutions

## Failure Criteria

* Failure if >25% of filtered input cells are removed during QC.
* Clusters primarily driven by QC metrics
* Canonical markers not enriched
* Severe over-fragmentation (>20 clusters)
* Extreme instability across resolutions