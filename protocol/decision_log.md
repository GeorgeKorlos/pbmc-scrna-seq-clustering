# Single-Cell RNA-seq Clustering & Marker Identification

## QC Threshold Justification

### Decision: Minimum genes per cell

**Choice**: 300
**Reason**: The default threshold (200) was considered too permissive. The 10th percentile of genes per cell in this dataset is 685; setting the cutoff at 300 removes low-complexity outliers while remaining conservative and avoiding excessive cell loss.
**Alternatives considered**: 200 (too permissive), 500 (risk of over-filtering).

### Decision: Maximum genes per cell

**Choice**: 2,500
**Reason**: The dataset’s median genes per cell is 1,049 and the 90th percentile is 1,477. A ceiling of 2,500 (~2.4× median) acts as an upper complexity filter while avoiding arbitrary aggressive trimming. This threshold is not presented as definitive doublet detection.
**Alternatives considered**: 3,000 (too permissive relative to distribution), Scrublet as primary (added pipeline complexity).

### Decision: Maximum mitochondrial fraction

**Choice**: 10%
**Reason**: Literature and 10x best practices suggest typical healthy PBMC mito fractions fall below 10–12%. A 10% ceiling is conservative and avoids adaptive tightening based on clustering results.
**Alternatives considered**: 15% (too permissive), adaptive thresholding (rejected due to preregistration discipline).

### Decision: Gene filtering threshold

**Choice**: Remove genes expressed in <3 cells
**Reason**: Standard practice to reduce sparsity noise without affecting biologically relevant genes in a ~3k cell dataset.

## Normalization Choice

### Decision: Log-normalization (target_sum=1e4, log1p)

**Choice**: Library size normalization to 10,000 counts per cell followed by log1p transformation
**Reason**: Log-normalization is widely used, reproducible, and sufficient for PBMC clustering at this scale. More complex methods (e.g., SCTransform) were not adopted to avoid overfitting or introducing additional modeling assumptions in a single-donor dataset.
**Alternatives considered**: SCTransform (higher complexity; unnecessary for methodological demonstration).

## HVG Count

### Decision: 2,000 HVGs (Seurat flavor)

**Choice**: 2,000
**Reason**: This is a community-standard default appropriate for ~3k cells. It balances signal capture with avoidance of noise inflation.
**Alternatives considered**: 1,000 (risk of under-capturing diversity), 3,000+ (risk of incorporating noise).

## PC Count

### Decision: 40 principal components

**Choice**: 40 PCs retained
**Reason**: Forty PCs were fixed as a conservative upper bound commonly used in Scanpy PBMC workflows (e.g., Scanpy PBMC tutorial uses n_pcs=40). This prevents post-hoc elbow-based tuning.
**Alternatives considered**: Elbow-based adaptive selection (rejected to prevent parameter tuning bias).

## Clustering Parameters

### Decision: n_neighbors = 15

**Choice**: 15
**Reason**: Scanpy default and appropriate for ~3k cells; balances local vs global structure without extreme fragmentation.
**Alternatives considered**: 10 (more local), 20 (more global smoothing).

### Decision: Leiden resolution = 1.0 (primary)

**Choice**: 1.0
**Reason**: Scanpy default; typically yields 8–12 clusters in PBMC datasets of this scale.
**Alternatives considered**: Lower resolution (0.5) and higher resolution (1.5) used strictly for robustness assessment, not selection.

### Decision: Resolution sweep (0.5, 1.0, 1.5)

**Choice**: Fixed sweep values
**Reason**: Provides limited robustness check without creating parameter search flexibility. Stability assessed via ARI and lineage proportion consistency.

## Differential Expression

### Decision: Wilcoxon rank-sum test

**Choice**: Wilcoxon
**Reason**: Wilcoxon rank-sum test selected based on benchmarking studies (e.g., Soneson & Robinson, 2018) demonstrating robust marker detection performance in single-cell RNA-seq.
**Alternatives considered**: t-test (assumes normality), logistic regression (higher complexity).

### Decision: Multiple testing correction

**Choice**: Benjamini–Hochberg (FDR)
**Reason**: Controls false discovery rate while retaining statistical power; standard in single-cell workflows.
**Alternatives considered**: Bonferroni (overly conservative).

### Decision: DE thresholds

**Choice**: log2FC > 0.25, adj p < 0.05, ≥25% expression in cluster
**Reason**: Balances statistical significance and biological relevance; prevents single-gene low-frequency artifacts from defining clusters.
**Alternatives considered**: Higher logFC threshold (0.5) rejected to avoid missing subtle but valid markers.

## Regression Choice

### Decision: Regress pct_counts_mt only

**Choice**: Regress mitochondrial fraction during scaling; do not regress total_counts.
**Reason**:
Mitochondrial percentage primarily reflects cell stress or low-quality cells and is widely treated as a technical confounder. In contrast, total RNA content can reflect genuine biological differences across PBMC lineages (e.g., monocytes vs lymphocytes). Since library size normalization to 10,000 counts per cell is already performed, additional regression of total_counts was deemed unnecessary and potentially over-corrective.
**Alternatives considered**:
Regressing both total_counts and pct_counts_mt (rejected due to risk of removing biologically meaningful variation); no regression (rejected due to risk of mitochondrial-driven clustering artifacts).

## Deviations from Preregistration

### Decision: Upgrading library versions

**Original plan**: AnnData: 0.10.8, SciPy: 1.11.4
**Revised versions**: AnnData: 0.12.10, SciPy: 1.16.3

### Decision: Computed ribosomal percentage counts for exploratory purposes

### Decision: Maximum genes per cell

**Original plan**: 3,000
**Revised value**: 2,500
**Reason for deviation**: The initial ceiling of 3,000 was inconsistent with the dataset distribution (90th percentile = 1,477). A threshold of 2,500 (~1.7× 90th percentile) was selected prior to clustering to better reflect the empirical distribution while remaining conservative.
**Timing**: Adjustment made before clustering and differential expression analysis.
**Impact on interpretation**: Expected to remove only extreme upper outliers; does not alter core analysis strategy.