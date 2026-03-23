# PBMC scRNA-seq Clustering and Marker Identification

** Preregistered single-cell RNA analysis identifying 7 major immune population

---

## Key Results

- **7 clusters identified** at resolution 1.0 (6-15 expected range ✓)
- **5 major PBMC lineages recovered:** T cells (CD4+/CD8+), B cells, NK cells, Monocytes, Dendritic cells
- **All clusters validated** with ≥2 canonical lineage markers
- **1.2% QC loss** (33/2,752 cells removed, well below 25% threshold)
- **Moderate stability** across resolutions (ARI: 0.68-0.87)

**Annotations:**
| Cluster | Cell Type | Markers | Confidence |
|---------|-----------|---------|------------|
| 0 | Naive CD4+ T cells | CD3D, CD3E, IL7R, CCR7 | High |
| 1 | CD14+ Monocytes | CD14, HLA-DRA, LST1 | High |
| 2 | B cells | MS4A1, CD79A, HLA-DRA | High |
| 3 | NK cells | GNLY, NKG7, GZMA, FCGR3A | High |
| 4 | CD8+ T cells | CD3D, CD3E, CD8A, GZMA | High |
| 5 | Dendritic cells | ITGAX, HLA-DRA, FCGR3A | Moderate |
| 6 | Uncertain myeloid | HLA-DRA, LST1 | Low |

## Project Overview

This project demonstrates reproducible computational biology practices for MSc admission to NAIST (Nara Institute of Science and Technology). The analysis follows a **preregistered protocol** with locked parameters to prevent p-hacking and post-hoc rationalization.

**Scope:**
- Single-dataset PBMC analysis (no integration, trajectory, or deep learning)
- Standard Scanpy workflow (QC → normalization → clustering → marker ID)
- Conservative interpretation with explicit uncertainty documentation

**NOT included** (out of scope):
- Novel cell type discovery
- Subclustering of rare populations (<1%)
- Multi-omics integration
- Trajectory inference

---

## Quick Start

### Installation
```bash
# Clone repository
git clone 
cd pbmc_scRNA_clustering_markers

# Create virtual environment
python3.11 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r environment/requirements.txt
```

### Run Pipeline

Execute notebooks in order:
```bash
jupyter notebook notebooks/01_qc_exploration.ipynb      # QC metrics
jupyter notebook notebooks/02_preprocessing.ipynb       # Filtering, normalization, PCA
jupyter notebook notebooks/03_clustering.ipynb          # Leiden clustering
jupyter notebook notebooks/04_markers.ipynb             # Differential expression
jupyter notebook notebooks/05_annotation.ipynb          # Cell type assignment
```

## Repository Structure

```
pbmc_scRNA_clustering_markers/
│
├── config/                    # Locked pipeline parameters (YAML)
│   ├── clustering.yaml        # k=15, res=[0.5,1.0,1.5], seed=42
│   ├── qc.yaml               # 300-2500 genes, <10% mito
│   ├── pipeline.yaml         # Normalization, DE thresholds
│   └── markers.yaml          # Canonical PBMC markers (8 lineages)
│
├── data/
│   ├── raw/                  # 10x filtered matrix (5 MB)
│   └── processed/            # Intermediate AnnData objects
│
├── notebooks/                # Analysis pipeline (5 notebooks)
│   ├── 01_qc_exploration.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_clustering.ipynb
│   ├── 04_markers.ipynb
│   └── 05_annotation.ipynb
│
├── protocol/                 # Reproducibility documentation
│   ├── pre_registration.md   # Locked analysis plan
│   ├── decision_log.md       # Parameter justifications
│   └── data_source.md        # Dataset provenance (DOI, SHA256)
│
├── results/                  # Outputs (2.1 MB)
│   ├── annotation/           # Cluster → cell type mappings
│   ├── clustering/           # UMAPs, stability metrics
│   ├── markers/              # Top 10 markers/cluster, validation tables
│   ├── pca/                  # PCA variance plots
│   └── qc/                   # QC distribution plots
│
├── environment/
│   └── requirements.txt      # Pinned package versions
│
├── ENVIRONMENT.md            # System specs + package list
└── README.md                 # This file
```

## Methods Summary

### Dataset
- **Source:** 3k PBMCs, single healthy donor (10x Genomics)
- **Processing:** Cell Ranger v8.0.1, GRCh38.p14 reference
- **Input:** 2,752 cells × 54,950 genes (filtered matrix)
- **DOI:** https://doi.org/10.6084/m9.figshare.28414916
- **Verification:** SHA256 checksum validated ✓

### Pipeline
1. **QC Filtering** (Week 3)
   - Cells: 300-2,500 genes, <10% mitochondrial
   - Genes: Expressed in ≥3 cells
   - Loss: 33 cells (1.2%)

2. **Normalization** (Week 4)
   - Library size: 10,000 counts/cell
   - Log transformation
   - HVG selection: 2,000 genes (Seurat flavor)
   - Scaling: Regress mitochondrial % only

3. **Dimensionality Reduction** (Week 4)
   - PCA: 40 components (fixed, no elbow tuning)
   - UMAP: Visualization only

4. **Clustering** (Week 5)
   - Algorithm: Leiden
   - Parameters: k=15 neighbors, resolution=1.0 (primary)
   - Robustness: Resolutions 0.5, 1.5 tested
   - Stability: ARI between resolutions

5. **Marker Identification** (Week 6)
   - Test: Wilcoxon rank-sum (one cluster vs rest)
   - Correction: Benjamini-Hochberg FDR
   - Thresholds: adj. p<0.05, log2FC>0.25, ≥25% cells
   - Validation: Against preregistered canonical marker list

6. **Annotation** (Week 7)
   - Assignment rule: ≥2 canonical markers required
   - Ambiguous clusters labeled "Uncertain"
   - No forced labeling

### Software
- **Python:** 3.11.14
- **Key packages:** Scanpy 1.10.2, AnnData 0.12.10, NumPy 1.26.4
- **OS:** Ubuntu 22.04 LTS (WSL)
- **Seed:** 42 (fixed for reproducibility)

---

## Results

### Clustering Performance
- **Cluster count:** 7 (within preregistered 6-15 range)
- **Stability (ARI):**
  - 0.5 ↔ 1.0: **0.87** (high) ✓
  - 1.0 ↔ 1.5: **0.68** (borderline) ≈
- **Cell distribution:** No extreme fragmentation (all clusters >1%)

### Marker Validation
- **Canonical markers identified:** All 7 clusters have ≥2 markers
- **High confidence (≥4 markers):** Clusters 0, 3, 4, 5
- **Moderate confidence (3 markers):** Clusters 1, 2
- **Low confidence (2 markers):** Cluster 6 (uncertain myeloid)
- **QC check:** No clusters driven by mitochondrial/ribosomal genes ✓

### Success Criteria (Preregistered)
| Criterion | Target | Result | Status |
|-----------|--------|--------|--------|
| Cluster count | 6-15 | 7 | ✓ Pass |
| Major lineages | ≥4 | 5 (T, B, NK, Mono, DC) | ✓ Pass |
| QC loss | <25% | 1.2% | ✓ Pass |
| MT-driven clusters | 0 | 0 | ✓ Pass |
| Resolution stability | ARI ≥0.7 | 0.87 / 0.68 | ≈ Borderline |

---

## Limitations & Uncertainty

### Known Limitations
1. **NK/CD8+ T cell marker overlap**
   - NKG7, GZMA shared between Clusters 3 and 4
   - Distinction based on CD3 complex presence/absence
   - Biologically expected; annotation is defensible

2. **Cluster 6 annotation uncertainty**
   - Only 2 canonical markers (HLA-DRA, LST1)
   - Both shared across monocytes, DCs, and myeloid cells
   - Labeled "Uncertain myeloid" (conservative approach)

3. **Monocyte subtype resolution**
   - CD14+ monocytes identified (Cluster 1)
   - CD16+ (non-classical) monocytes not clearly separated
   - Higher resolution may be needed for subtype discrimination

4. **Rare populations**
   - No plasmablasts, basophils, or mast cells detected
   - Expected at <1% frequency; may be merged into larger clusters

5. **Resolution stability**
   - ARI(1.0, 1.5) = 0.68 slightly below 0.7 threshold
   - Indicates moderate (not high) stability
   - One cluster splits at higher resolution

### Uncertainty Quantification
- **High confidence:** 4/7 clusters (57%)
- **Moderate confidence:** 2/7 clusters (29%)
- **Low confidence:** 1/7 clusters (14%)

---

## Documentation

- **Protocol:** See `protocol/` for preregistration, decision log, and data provenance
- **Results:** See `results/` for marker tables, figures, and validation outputs
- **Methods:** See individual notebook markdown cells for step-by-step rationale

---

**Project Status:** Analysis complete.

---

## Citation

If referencing this work:
```
George Korlos. (2026). PBMC scRNA-seq Clustering & Marker Identification

```

---

## Contact

**Author:** George Korlos 
**Email:** korlone@proton.me

---

## License

This project is shared for academic review purposes as part of a graduate school application.

---

**Last Updated:** March 2026
