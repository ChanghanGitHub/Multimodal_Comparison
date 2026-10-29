# Code for Fig. 1c

This folder contains the code for the MOFA / MultiVI comparison in Fig. 1c. The comparison asks how genes rank in multimodal (joint RNA + ATAC) models versus single-modality models.

The data are not included. The analysis uses paired snRNA-seq and snATAC-seq data from PBMCs of two individuals (PID 9 and 12), restricted to NK and CD8⁺ T cells.

## Scripts (run in this order)

| Script | Language | Purpose |
|---|---|---|
| `00_build_paired_Seurat_object.Rmd` | R | Combine RNA and ArchR gene scores into one Seurat object (`Seurat_RNA_ATAC_CD8_NK.rds`) |
| `01_export_multiome_for_MultiVI.Rmd` | R | Export raw RNA counts and ArchR peak counts for MultiVI |
| `02_run_MultiVI.ipynb` | Python | MultiVI: multimodal gene scores, rankings, top-10,000 gene lists |
| `03_scVI_single_modality.Rmd` | R (reticulate) | scVI single-modality scores (RNA counts; ArchR gene scores), merged with the MultiVI rankings |
| `04_MOFA2_analysis.Rmd` | R | MOFA+: multimodal and single-modality gene scores, rankings, top-10,000 gene lists |
| `05_Fig1c_RankingPlot.Rmd` | R | Fig. 1c ranking plot |

Script `04` is independent of `01`–`03`. Script `05` uses the outputs of `03` and `04`. It also needs the Incytr gene ranks (`RNA_ATAC_3score_comparison.csv`), which come from the Incytr analysis (He et al., 2026) and are not included here.

## Inputs (not included)

- `Seurat_RNA_CD8_NK.rds` and `Seurat_ATAC_CD8_NK.rds`: snRNA-seq and ArchR gene scores of NK and CD8⁺ T cells, with `PID` and `predicted.celltype.l1` in the metadata.
- ArchR projects for NK and CD8⁺ T cells (`NK/`, `CD8_T/`), with a `PeakMatrix` in the Arrow files.

Set the path variables at the top of each script (`data_dir`, `BASE`, etc.) to your local folders.

## Environment

- **Python** (MultiVI, scVI): see `environment/python_requirements.txt`. The analysis was run with scvi-tools 1.5.1, Python 3.12 and PyTorch 2.11.0 (CUDA 12.8) on an NVIDIA RTX 5080. A GPU is recommended; CPU also works but is slower.
- **R**: Seurat, Signac, ArchR, MOFA2, reticulate, dplyr, data.table, ggplot2. Each Rmd ends with `sessionInfo()`, which records the package versions.

## Methods summary

- **MultiVI** follows the scvi-tools MultiVI tutorial: https://docs.scvi-tools.org/en/1.5.1/tutorials/notebooks/multimodal/MultiVI_tutorial.html
- **scVI** follows "Integrating datasets with scVI in R": https://docs.scvi-tools.org/en/1.5.1/tutorials/notebooks/r/scvi_in_R.html
- **MOFA** uses 15 factors and default MOFA2 options.
- **Gene scores:**
  - MultiVI and scVI: mean model-normalized expression per cell type.
  - MOFA: Σ_k W(g, k) · (mean factor value of the cell type), computed per view; the combined score is the mean of the two views.
- **Rankings:** genes are ranked within each cell type. The top 10,000 genes are used as Incytr input, and rank 10,000 is the threshold line in Fig. 1c.

Details are given in the Supplementary Materials.
