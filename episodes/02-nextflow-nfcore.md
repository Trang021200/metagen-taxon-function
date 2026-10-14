---
title: 'Reproducible workflows with Nextflow and nf-core'
---

:::::::::::::::::::::::::::::::::::::: questions

- Why is reproducibility a challenge in bioinformatics?
- What are Nextflow and nf-core, and how do they help?
- How do I connect to a Nectar instance and check that the software is available?
- How do I prepare the input files needed by nf-core/taxprofiler and nf-core/funcprofiler?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Understand reproducibility in bioinformatics
- Explain what Nextflow is and why it is useful
- Navigate the nf-core website and find suitable pipelines
- Connect to a Nectar instance
- Load and check the software used in the workshop
- Construct reproducible commands for both pipelines
- Prepare and validate samplesheets and database sheets

::::::::::::::::::::::::::::::::::::::::::::::::

## Reproducibility in bioinformatics

## Nextflow and nf-core

- Nextflow: workflow manager.
- nf-core: community-curated Nextflow pipelines.

Pipelines are listed on the nf-core website. In this workshop we use:

- nf-core/taxprofiler
- nf-core/funcprofiler

## Connecting to Nectar

```bash
# PENDING TO WRITE CODE
```

## Loading the software

Check that each tool is available:

```bash
java -version
nextflow -version
apptainer --version
nf-core --version
```

## Preparing the samplesheet

Both pipelines use the same samplesheet columns: `sample,run_accession,instrument_platform,fastq_1,fastq_2,fasta`. Paths must be absolute.

```bash
mkdir -p ~/mbite/run && cd ~/mbite/run

echo "sample,run_accession,instrument_platform,fastq_1,fastq_2,fasta" > samplesheet.csv
for i in 1 2 3 4 5; do
  echo "gut${i},run1,ILLUMINA,$HOME/mbite/data/raw/Gut_sample${i}.left.fq.gz,$HOME/mbite/data/raw/Gut_sample${i}.right.fq.gz," >> samplesheet.csv
done
cat samplesheet.csv
```

```output
sample,run_accession,instrument_platform,fastq_1,fastq_2,fasta
gut1,run1,ILLUMINA,/home/ubuntu/mbite/data/raw/Gut_sample1.left.fq.gz,/home/ubuntu/mbite/data/raw/Gut_sample1.right.fq.gz,
gut2,run1,ILLUMINA,/home/ubuntu/mbite/data/raw/Gut_sample2.left.fq.gz,/home/ubuntu/mbite/data/raw/Gut_sample2.right.fq.gz,
gut3,run1,ILLUMINA,/home/ubuntu/mbite/data/raw/Gut_sample3.left.fq.gz,/home/ubuntu/mbite/data/raw/Gut_sample3.right.fq.gz,
gut4,run1,ILLUMINA,/home/ubuntu/mbite/data/raw/Gut_sample4.left.fq.gz,/home/ubuntu/mbite/data/raw/Gut_sample4.right.fq.gz,
gut5,run1,ILLUMINA,/home/ubuntu/mbite/data/raw/Gut_sample5.left.fq.gz,/home/ubuntu/mbite/data/raw/Gut_sample5.right.fq.gz,
```

Check that every file listed exists:

```bash
tail -n +2 samplesheet.csv | cut -d, -f4,5 | tr ',' '\n' | xargs ls -l
```

## Preparing the database sheets

### taxprofiler: `databases_taxprofiler.csv`

Columns are `tool,db_name,db_params,db_type,db_path`.

```bash
cat > databases_taxprofiler.csv << 'EOF'
tool,db_name,db_params,db_type,db_path
kraken2,k2_standard_08gb,,short,/data/databases/kraken2/k2_standard_08gb
bracken,k2_standard_08gb,;-r 150,short,/data/databases/kraken2/k2_standard_08gb
metaphlan,mpa_vJun23,,short,/data/databases/metaphlan/mpa_vJun23_CHOCOPhlAnSGB_202403
motus,motus_db,,short,/data/databases/motus/db_mOTU
sylph,gtdb_r220,,short,/data/databases/sylph/gtdb-r220-c200-dbv1.syldb
EOF
```


### funcprofiler: `databases_funcprofiler.csv`

Columns are `tool,db_name,db_entity,db_params,db_path`. HUMAnN and eggNOG-mapper need one row per database component.

```bash
cat > databases_funcprofiler.csv << 'EOF'
tool,db_name,db_entity,db_params,db_path
humann_v3,humann_v3_uniref90,humann_metaphlan,,/data/databases/humann/metaphlan_vOct22
humann_v3,humann_v3_uniref90,humann_nucleotide,,/data/databases/humann/chocophlan
humann_v3,humann_v3_uniref90,humann_protein,,/data/databases/humann/uniref90_diamond
humann_v3,humann_v3_uniref90,humann_utility,,/data/databases/humann/utility_mapping
eggnogmapper,eggnog_v5,eggnogmapper_db,diamond,/data/databases/eggnog/eggnog_proteins.dmnd
eggnogmapper,eggnog_v5,eggnogmapper_data_dir,,/data/databases/eggnog/data
EOF
```

## Reproducible launch commands

Parameters go in a `params.json` file and the command in a launch script.

```bash
cat > params_taxprofiler.json << EOF
{
  "input": "$HOME/mbite/run/samplesheet.csv",
  "databases": "$HOME/mbite/run/databases_taxprofiler.csv",
  "outdir": "$HOME/mbite/results/taxprofiler",
  "perform_shortread_qc": true,
  "save_analysis_ready_fastqs": true,
  "run_kraken2": true,
  "run_bracken": true,
  "run_metaphlan": true,
  "run_motus": true,
  "run_sylph": true,
  "run_profile_standardisation": true,
  "run_krona": true,
  "taxpasta_add_name": true,
  "taxpasta_add_rank": true,
  "taxpasta_add_lineage": true
}
EOF
```

```bash
cat > nextflow_launch.sh << 'EOF'
#!/usr/bin/env bash
set -euo pipefail
nextflow run nf-core/taxprofiler \
  -r <version> \
  -profile singularity \
  -params-file params_taxprofiler.json \
  -resume
EOF
chmod +x nextflow_launch.sh
```

Alternatively, the nf-core tools launcher writes a `nf-params.json` interactively:

```bash
nf-core pipelines launch nf-core/taxprofiler -r <version>
```

::::::::::::::::::::::::::::::::::::: keypoints

- Pin pipeline versions with `-r`.
- Check software with `nextflow -version` and `apptainer --version` before launching.
- Both pipelines need a samplesheet with absolute paths and a database sheet.
- Keep parameters in `params.json` and the command in a launch script.

::::::::::::::::::::::::::::::::::::::::::::::::
