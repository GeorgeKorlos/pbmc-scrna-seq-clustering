## Dataset Identity

* **Dataset name**: 3k PBMCs from a healthy donor
* **DOI**: https://doi.org/10.6084/m9.figshare.28414916
* **Date downloaded**: 2026-02-13

## File

* **File name**: filtered_feature_bc_matrix.h5
* **File size**: 5019 kB
* **File format**: 10x Genomics HDF5 feature-barcode matrix (.h5)

## File Integrity Verification

* **Verification date**: 2026-02-15
* **Command used**: `sha256sum filtered_feature_bc_matrix.h5`
* **Environment**: Ubuntu (WSL)
* **Result**: MATCH (SHA256 = 64fa7e6c002fbcc25a656061bd19fe4b432b3a81fddb5bba35554f3d56932570)

## Upstream Processing (as reported by data provider)

* **Processing pipeline**: Cell Ranger v8.0.1 (repository-reported)
* **Reference genome**: GENCODE Release 47 (GRCh38.p14)
* **Sequencing platform**: 10x Genomics Chromium
* **Undocumented parameters**: chemistry version, `--expect-cells`, `force-cells`, intron mode (pre-mRNA), and Cell Ranger web_summary QC metrics were not provided in the repository.

## Dataset Characteristics

* **Biological source**: Peripheral Blood Mononuclear Cells (PBMC)
* **Donor status**: Reported as healthy (no clinical metadata available)
* **Reported cell count (source page)**: 3,000 (nominal)
* **Filtered barcode count (observed in file)**: 2,752

## Observed Matrix Characteristics (empirically derived)

* **Feature count (observed)**: 54,950 (features defined by the reference annotation; not “expressed genes”)
* **Total non-zero entries**: 7,431,557

**UMIs per cell**
* Median: 2,536
* Percentiles: 10th = 1,368 | 50th = 2,536 | 90th = 3,993

**Genes detected per cell**
* Median: 1,049
* Percentiles: 10th = 685 | 50th = 1,049 | 90th = 1,477