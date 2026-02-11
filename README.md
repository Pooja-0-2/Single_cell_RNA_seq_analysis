# Single cell RNA sequence analysis
## Single cell RNA sequence analysis pipeline in R of kidney fibrosis dataset including quality control, normalization, clustering and visualization

This repository presents a complete end-to-end single-cell RNA sequencing (scRNA-seq) analysis of the publicly available dataset GSE183276.

## The objective of this project are:

1. Establish scRNA-seq analysis pipeline
2. Investigate fibrosis-associated gene expression patterns
3. Identify cell types that are mostly affected
4. Compare Diabetic Kidney Disease (DKD) vs Healthy controls
5. Determine molecular pathways that drive the disease

## PART 1: DATA & DEPENDENCIES - COMPLETE REQUIREMENTS
### DATASET: GSE183276 - Diabetic Kidney Disease scRNA-seq :- GEO Accession Page
 wget https://ftp.ncbi.nlm.nih.gov/geo/series/GSE183nnn/GSE183276/suppl/GSE183276_Kidney_Healthy-Injury_Cell_Atlas_scCv3_Counts_03282022.RDS.gz
 https://ftp.ncbi.nlm.nih.gov/geo/series/GSE183nnn/GSE183276/suppl/GSE183276_Kidney_Healthy-Injury_Cell_Atlas_scCv3_Counts_03282022.RDS.gz
| File / Dataset                                                            | Description                                               | Link / Source                                                                 | Notes                                                        |
| ------------------------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------ |
| `GSE183276_Kidney_Healthy-Injury_Cell_Atlas_scCv3_Counts_03282022.RDS` | Raw single-cell counts for healthy and DKD kidney samples | [GEO GSE183276](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE183276) | Used to create Seurat object; include metadata if available. |
| Metadata (if separate)                                                    | Sample information (condition, batch, donor)              | Often in `GSE183276_series_matrix.txt`                                        | Needed for batch correction and DEG analysis.                |
| Protein-coding genes                                      | To filter for protein-coding genes                        | [GENCODE](https://www.gencodegenes.org/human/)                                | Optional, but improves QC and downstream analysis.           |
| CellTypist models                                                         | Pretrained human cell type references                     | Installed via Python: `celltypist.download_models()`                          | Required for automated annotation.                           |
| Optional: Processed Seurat/Scanpy objects                                 | Preprocessed `.h5ad` or `.h5seurat` files                 | From your own intermediate steps                                              | Saves computation time; useful for checkpoints.              |


### R Dependencies for scRNA-seq Workflow
#### CRAN packages
install.packages(c("Seurat", "SeuratDisk", "dplyr", "R.utils", "ggplot2", "ggExtra",
                   "RColorBrewer", "openxlsx", "scales", "HGNChelper", "dittoSeq", "harmony"))

#### Bioconductor packages
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install(c("batchelor", "zellkonverter", "SingleCellExperiment"))


| Package                | Purpose / Use                                                                                                                  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Seurat`               | Core single-cell RNA-seq analysis (QC, normalization, clustering, UMAP/tSNE).                                                  |
| `SeuratDisk`           | Convert Seurat objects to/from `.h5ad` / `.h5seurat` for interoperability with Python (Scanpy) or saving intermediate results. |
| `dplyr`                | Data manipulation, filtering, and summarization of metadata or gene-level data.                                                |
| `R.utils`              | Utility functions, e.g., for handling compressed files (.gz).                                                                  |
| `ggplot2`              | High-quality plotting (violin plots, scatter plots, heatmaps).                                                                 |
| `ggExtra`              | Enhances ggplot2 plots with marginal histograms or density plots.                                                              |
| `RColorBrewer`         | Color palettes for plots (clusters, conditions).                                                                               |
| `openxlsx`             | Export tables and results to Excel files (e.g., DEG results).                                                                  |
| `scales`               | Scales and formatting for ggplot2 axes and legends.                                                                            |
| `HGNChelper`           | Corrects gene symbols to HGNC-approved names. Useful for DEG/pathway analysis.                                                 |
| `dittoSeq`             | Simplified plotting for scRNA-seq data (boxplots, dotplots, UMAPs, heatmaps).                                                  |
| `harmony`              | Batch correction to integrate multiple samples or donors.                                                                      |
| `batchelor`            | Alternative batch correction for SingleCellExperiment objects.                                                                 |
| `zellkonverter`        | Read/write between `SingleCellExperiment` and `.h5ad` (Scanpy) formats.                                                        |
| `SingleCellExperiment` | Core S4 class for single-cell data in R (used by `batchelor` and other Bioconductor packages).                                 |

