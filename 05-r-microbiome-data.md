---
title: 'Importing and exploring microbiome data with R'
---

:::::::::::::::::::::::::::::::::::::: questions

- Which R data structures are used for microbiome data?
- How do I import Taxpasta and HUMAnN tables and sample metadata into R?
- How do I filter, aggregate and transform microbiome data?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Identify the phyloseq and TreeSummarizedExperiment object types and explain their principal differences
- Import taxonomic and functional tables and sample metadata
- Build a TreeSummarizedExperiment object
- Filter low-prevalence features, aggregate taxa at a selected rank and calculate relative abundance

::::::::::::::::::::::::::::::::::::::::::::::::



## Loading the libraries

Install the packages first (see Setup), then load them:


``` r
library(readr)
library(dplyr)
library(tidyr)
library(ggplot2)
library(SummarizedExperiment)
library(TreeSummarizedExperiment)
library(mia)
library(miaViz)
```

Check the versions you are using:


``` r
packageVersion("mia")
```

``` output
[1] '1.20.0'
```

``` r
packageVersion("miaViz")
```

``` output
[1] '1.20.0'
```

``` r
packageVersion("TreeSummarizedExperiment")
```

``` output
[1] '2.20.0'
```

## phyloseq and TreeSummarizedExperiment

- phyloseq: `otu_table`, `tax_table`, `sample_data`, `phy_tree`
- TreeSummarizedExperiment: `assay` (one or more), `rowData`, `colData`, `rowTree`
- Convert between them with `mia::convertToPhyloseq()` and `mia::convertFromPhyloseq()`


## Importing the data

Copy the results from Nectar to your laptop (run on your laptop):

```bash
# PENDING TO WRITE CODE
```


### Sample metadata


``` r
meta <- read_tsv(meta_file, show_col_types = FALSE)
meta
```

### Taxonomic table (Taxpasta)


``` r
tax_tbl <- read_tsv(tax_file, show_col_types = FALSE)
head(tax_tbl)
```

## Building a TreeSummarizedExperiment


``` r
annot_cols <- c("taxonomy_id", "name", "rank", "lineage")
sample_cols <- setdiff(colnames(tax_tbl), annot_cols)

counts <- as.matrix(tax_tbl[, sample_cols])
rownames(counts) <- tax_tbl$taxonomy_id

row_df <- tax_tbl |>
  select(any_of(annot_cols)) |>
  separate_wider_delim(lineage, delim = ";",
                       names = c("Kingdom", "Phylum", "Class", "Order",
                                 "Family", "Genus", "Species"),
                       too_few = "align_start", too_many = "drop") |>
  as.data.frame()
rownames(row_df) <- tax_tbl$taxonomy_id

col_df <- meta |>
  filter(sample %in% sample_cols) |>
  as.data.frame()
rownames(col_df) <- col_df$sample
col_df <- col_df[sample_cols, ]

tse <- TreeSummarizedExperiment(
  assays  = list(counts = counts),
  rowData = row_df,
  colData = col_df
)
tse
```


## Sequencing depth


``` r
depth <- data.frame(sample = colnames(tse),
                    reads  = colSums(assay(tse, "counts")))
ggplot(depth, aes(x = sample, y = reads)) +
  geom_col() +
  labs(x = "Sample", y = "Classified reads")
```

## Filtering, aggregating and transforming


``` r
tse_species <- agglomerateByRank(tse, rank = "Species")
tse_species <- subsetByPrevalent(tse_species,
                                 assay.type = "counts",
                                 detection = 10,
                                 prevalence = 0.2)
tse_species <- transformAssay(tse_species,
                              assay.type = "counts",
                              method = "relabundance")
tse_species
```

## Visualising composition


``` r
tse_phylum <- agglomerateByRank(tse, rank = "Phylum")
tse_phylum <- transformAssay(tse_phylum, assay.type = "counts",
                             method = "relabundance")
plotAbundance(tse_phylum, assay.type = "relabundance")
```


``` r
plotPrevalence(tse_species, assay.type = "relabundance")
```


::::::::::::::::::::::::::::::::::::: keypoints

- Load the packages with `library()` at the start of the session.
- A TreeSummarizedExperiment holds assays, feature data (`rowData`) and sample data (`colData`).
- Taxpasta tables are read with `readr` and assembled into a TreeSummarizedExperiment.
- `agglomerateByRank()`, `subsetByPrevalent()` and `transformAssay()` aggregate, filter and transform the data.

::::::::::::::::::::::::::::::::::::::::::::::::
