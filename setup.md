---
title: Setup
---

Please complete these steps before the workshop. We use two computing environments:

- A Nectar cloud instance (provided by the organisers) to run the nf-core pipelines.
- Your laptop, to connect to Nectar via SSH and to analyse the outputs in R/RStudio.

Software on the Nectar instance is pre-installed for workshop attendees.

## Software overview

Laptop:

- SSH client
- R and RStudio Desktop
- CRAN packages: readr, dplyr, tidyr, ggplot2, BiocManager
- Bioconductor packages: SummarizedExperiment, TreeSummarizedExperiment, mia, miaViz, phyloseq

Nectar instance:

- Java
- Nextflow
- Apptainer/Singularity or Docker
- nf-core tools (optional)
- nf-core/taxprofiler
- nf-core/funcprofiler

## R and RStudio (laptop)

- Install R from CRAN.
- Install RStudio Desktop.

### R packages

Run in the RStudio console:

```r
# CRAN packages
install.packages(c("readr", "dplyr", "tidyr", "ggplot2", "BiocManager"))

# Bioconductor packages
BiocManager::install(c(
  "SummarizedExperiment",
  "TreeSummarizedExperiment",
  "mia",
  "miaViz",
  "phyloseq"
))
```

## Data

- 5 simulated human gut metagenomes, Illumina paired-end, from Sun, Huang et al. (2021) Nature Methods: https://www.nature.com/articles/s41592-021-01141-3
- Source: figshare project 79916, dataset Gut_sample: https://doi.org/10.6084/m9.figshare.12234947.v1
- 10 FASTQ files (`Gut_sampleX.fq.gz`) and 2 ground-truth tables (`5_gut_taxonomic_abd.txt`, `5_gut_sequence_abd.txt`)
- Total size about 6.5 GB

On the workshop instances the data are already in `~/mbite/data/raw/`.

## Reference databases

- Kraken2 Standard-8
- MetaPhlAn mpa_vJun23_CHOCOPhlAnSGB_202403
- mOTUs database
- sylph GTDB database
- HUMAnN: ChocoPhlAn, UniRef90, utility mapping
- eggNOG v5
