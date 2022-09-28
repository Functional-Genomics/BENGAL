## BENGAL: BENchmarking inteGration strAtegies for cross-species anaLysis of single-cell transcriptomics data ##


Author: Yuyao Song <ysong@ebi.ac.uk>

A Nextflow DSL2 pipeline to perform cross-species single-cell RNA-seq data integration and assessment of integration results. 

This repo includes:

1) the Nextflow scripts
2) conda environment configurations and containers
3) an example config file for running the Nextflow pipeline

### Inputs:

A tab-seperated metadata file. Mapping species to the paths of raw count AnnData objects, in the form of .h5ad files. See example:    `example_metadata_nf.tsv`.

#### Input Requirements:

The AnnData objects need to have the following row or column annotations:

Note that the exact column name of each key is specified in the config file.

a) a species_key in adata.obs to store species identity. Naming should be in line with the short name in ENSEMBL, such as hsapiens; mmusculus; drerio etc.
b) a cluster_key in adata.obs to store cell types. If assessment is performed, this column will be used to match homologous cell types across species. Preferably, use Cell Ontology annotation. 
c) mean_counts in adata.var computed by sc.pp.calculate_qc_metrics from scanpy

### Outputs:

1) Concatenated raw count AnnData objects containing cells from all species, in form of .h5ad files. Objects are concatenated by matching genes between species using gene homology from ENSEMBL.  
2) Integration result from different algorithms including: fastMNN, harmony, LIGER, LIGER UINMF, scanorama, scVI, SeuratV4CCA and SeuratV4RPCA, in the form of AnnData (.h5ad) or Seurat (.h5seurat) objects and UMAP visualization.
3) Assessment metrics for each integrated results. There are 4 batch correction metrics and 6 biology conservation metrics. Plots associated for the metrics are also generated for visual inspection. 
4) Cross-species cell type annotation transfer results with SCCAF.

### Run steps:

In a bash shell:

1) `nextflow -C config/example.config run concat_by_homology_multiple_species.nf`
2) Convert concatenated files from .h5ad to .h5seurat using SeuratDisk (keep .h5ad files too). This is for R-based methods that requires .h5seurat input to run itegration. The `envs/h5ad_h5seurat_convert.yml` is a compatible environment that will work.
3) `nextflow -C config/example.config run cross_species_integration_multiple_species.nf`
4) Convert methods that output .h5seurat files to .h5ad files (keep .h5seurat files too), this is for calculating metrics.
5) `nextflow -C config/example.config run cross_species_assessment_multiple_species.nf`



LICENSE: MIT license

