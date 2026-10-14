---
title: 'Functional profiling with nf-core/funcprofiler'
---

:::::::::::::::::::::::::::::::::::::: questions

- How do I configure and run nf-core/funcprofiler?
- What do HUMAnN and eggNOG-mapper measure?
- What is the difference between gene-family abundance, pathway abundance and pathway coverage?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Configure functional profiling with nf-core/funcprofiler
- Explain the roles of HUMAnN and eggNOG-mapper
- Distinguish gene-family abundance, pathway abundance and pathway coverage
- Identify the standardised function tables needed for downstream analysis

::::::::::::::::::::::::::::::::::::::::::::::::

## HUMAnN and eggNOG-mapper

- HUMAnN
- eggNOG-mapper


## Input: pre-processed reads

nf-core/funcprofiler does not perform read QC or host removal. We use the reads saved by taxprofiler with `--save_analysis_ready_fastqs`.

```bash
cd ~/mbite/run
ls ~/mbite/results/taxprofiler/analysis_ready_fastqs/
```

```bash
READS=~/mbite/results/taxprofiler/analysis_ready_fastqs
echo "sample,run_accession,instrument_platform,fastq_1,fastq_2,fasta" > samplesheet_func.csv
for i in 1 2 3 4 5; do
  r1=$(ls $READS/gut${i}_*_1.fastq.gz)
  r2=$(ls $READS/gut${i}_*_2.fastq.gz)
  echo "gut${i},run1,ILLUMINA,${r1},${r2}," >> samplesheet_func.csv
done
cat samplesheet_func.csv
```

## Running the pipeline

```bash
nextflow run nf-core/funcprofiler \
  -r <version> \
  -profile singularity \
  --input samplesheet_func.csv \
  --databases databases_funcprofiler.csv \
  --outdir ~/mbite/results/funcprofiler \
  --run_humann_v3 \
  --run_eggnogmapper \
  -resume
```

## Outputs

```bash
ls ~/mbite/results/funcprofiler
ls ~/mbite/results/funcprofiler/humann_v3/gut1
```

<!-- TODO: paste real output -->

- MultiQC report: `multiqc/multiqc_report.html`
- Gene family abundance: `humann_v3/<sample>/*_genefamilies.tsv`
- Pathway abundance: `humann_v3/<sample>/*_pathabundance.tsv`
- Pathway coverage: `humann_v3/<sample>/*_pathcoverage.tsv`
- Species functional profiles: stratified rows in the HUMAnN tables
- eggNOG-mapper annotations: `eggnogmapper/<db_name>/*.emapper.annotations`
- Execution and resource reports: `pipeline_info/`

```bash
head -n 5 ~/mbite/results/funcprofiler/humann_v3/gut1/*_pathabundance.tsv
```

::::::::::::::::::::::::::::::::::::: keypoints

- nf-core/funcprofiler expects reads that have already been through QC.
- HUMAnN reports gene families, pathway abundance and pathway coverage, overall and per species.

::::::::::::::::::::::::::::::::::::::::::::::::
