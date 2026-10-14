---
title: 'Taxonomic profiling with nf-core/taxprofiler'
teaching: 45
exercises: 25
---

:::::::::::::::::::::::::::::::::::::: questions

- How do I configure and run nf-core/taxprofiler?
- How do sylph, mOTUs, Kraken2/Bracken and MetaPhlAn differ?
- How do I interpret the pipeline outputs?
- Which table do I need for downstream analysis in R?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Configure taxonomic profiling with nf-core/taxprofiler
- Explain the roles of sylph, mOTUs, Kraken2/Bracken and MetaPhlAn
- Interpret the MultiQC report, classification summaries and pipeline reports
- Identify the standardised abundance table needed for downstream analysis

::::::::::::::::::::::::::::::::::::::::::::::::

## The profilers

- Kraken2 and Bracken
- MetaPhlAn
- mOTUs
- sylph

## Running the pipeline

```bash
cd ~/mbite/run
./nextflow_launch.sh
```

or directly:

```bash
nextflow run nf-core/taxprofiler \
  -r 2.0.1 \
  -profile singularity \
  --input samplesheet.csv \
  --databases databases_taxprofiler.csv \
  --outdir ~/mbite/results/taxprofiler \
  --perform_shortread_qc \
  --save_analysis_ready_fastqs \
  --run_kraken2 --run_bracken \
  --run_metaphlan --run_motus --run_sylph \
  --run_profile_standardisation \
  --taxpasta_add_name --taxpasta_add_rank --taxpasta_add_lineage \
  --run_krona \
  -resume
```

## Output overview

```bash
ls ~/mbite/results/taxprofiler
```

```output
analysis_ready_fastqs  bracken  fastp  kraken2  krona  metaphlan  motus
multiqc  pipeline_info  sylph  taxpasta  ...
```

### MultiQC report

`multiqc/multiqc_report.html` contains the read QC (fastp) and classification summaries. Copy it to your laptop to open it:

```bash
# PENDING TO WRITE CODE
```

### Taxonomic classification summaries

### Standardised and merged tables (Taxpasta)

Taxpasta converts the output of each profiler into one merged table per profiler and database, with taxa in rows and samples in columns. The tables are in `taxpasta/`.

```bash
ls ~/mbite/results/taxprofiler/taxpasta/
head -n 5 ~/mbite/results/taxprofiler/taxpasta/bracken_k2_standard_08gb.tsv
```

We import these tables into R in the episode Importing and exploring microbiome data with R.

### Pipeline execution and resource reports

`pipeline_info/` contains the execution report, timeline, trace and software versions.

::::::::::::::::::::::::::::::::::::: keypoints

- nf-core/taxprofiler runs several profilers on the same pre-processed reads.
- The Taxpasta tables are the input for the analysis in R.
- `pipeline_info/` records resource usage and software versions.

::::::::::::::::::::::::::::::::::::::::::::::::
