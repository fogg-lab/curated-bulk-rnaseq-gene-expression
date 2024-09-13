# curated-bulk-rnaseq-gene-expression

Created for the [GENE Platform](https://github.com/fogg-lab/gene-platform).

This repo hosts the data retrieval script to download RNAseq raw counts + coldata from [61 GDC projects](https://docgl1or94tw4.cloudfront.net/curated-bulk-rnaseq-gene-expression/GDC/index.json), [13,105 GEO human series](https://docgl1or94tw4.cloudfront.net/curated-bulk-rnaseq-gene-expression/GEO-human/index.json), and [13,079 GEO mouse series](https://docgl1or94tw4.cloudfront.net/curated-bulk-rnaseq-gene-expression/GEO-mouse/index.json).

The data is filtered to include only protein-coding and lncRNA genes, and additionally exclude genes with zero count for all samples within a project/series.

**Total size**: 31.55 GB (29.38 GiB)

You need about 32 GB disk space to download the whole collection.

## Data Sources

- **GDC**: We used the GDC Transfer tool to download raw counts and clinical data for the samples from GDC prior to preprocessing.
- **GEO**: We pulled preprocessed GEO data from ARCHS<sup>4</sup> and further processed the sample-specific metadata to organize the information into distinct characteristics and phenotypes for each sample[1].

## Prerequisites
- [Python 3.9 or higher](https://www.python.org/downloads/)
- [git](https://git-scm.com/)
- [requests](https://pypi.org/project/requests/)

## Instructions

1. Clone this repo and navigate to its directory.
    ```
    git clone https://github.com/fogg-lab/curated-bulk-rnaseq-gene-expression.git
    cd curated-bulk-rnaseq-gene-expression
    ```

2. Run the download script.
    ```
    python download_dataset.py
    ```

    To download to a different location than the current working directory, specify a directory like so:
    ```
    python download_dataset.py "C:\Users\me\datasets"
    ```

## Dataset Directory Structure

```
.
├── GEO/
|   ├── all_genes.csv.gz
|   ├── index.json
│   ├── coldata/
│   ├── expression/
│   └── genes/
├── GDC-human/
|   ├── all_genes.csv.gz
|   ├── index.json
│   ├── coldata/
│   ├── expression/
│   └── genes/
├── GDC-mouse/
    ├── all_genes.csv.gz
    ├── index.json
    ├── coldata/
    ├── expression/
    └── genes/
```

## Index

Each corpus ([GDC](https://docgl1or94tw4.cloudfront.net/curated-bulk-rnaseq-gene-expression/GDC/index.json), [GEO-mouse](https://docgl1or94tw4.cloudfront.net/curated-bulk-rnaseq-gene-expression/GEO-mouse/index.json), [GEO-human](https://docgl1or94tw4.cloudfront.net/curated-bulk-rnaseq-gene-expression/GEO-human/index.json)) contains an index.json file containing the ID, description, and number of samples for each project or series.

## Gene info

Each corpus also contains a file all_genes.csv.gz containing Ensembl gene IDs, HUGO symbols, Entrez IDs, and biotypes (either protein coding or lncRNA) for all genes in the dataset for that corpus.

## Subdirectories

Each top-level directory (GEO and GDC) contains three subdirectories:

### 1. coldata/

Contains sample-specific metadata for each dataset.

- **File naming**: `DATASET.csv.gz`
- **Format**: Gzip-compressed CSV
- **Content**: All samples and traits for a dataset
- **Examples**: 
  - `GDC/coldata/TCGA-CESC.csv.gz`
  - `GEO-human/coldata/GSE26284.csv.gz`
- **Note**: The first column is always "sample_id"

### 2. expression/

Contains filtered raw counts for gene expression.

- **File naming**: `DATASET.npy.gz`
- **Format**: Gzip-compressed NPY (.npy.gz)
- **Content**: 2D array of signed 32-bit integers
  - Rows: Samples (in the same order as in coldata)
  - Columns: Genes (order matches the corresponding file in the genes directory)
- **Note**: Does not contain labels for rows or columns

### 3. genes/

Contains information about the genes retained after filtering low counts.

- **File naming**: `DATASET.csv.gz` (same as coldata)
- **Format**: Gzip-compressed CSV
- **Columns**:
  - ensembl_id
  - symbol
  - entrezgene
  - biotype (either protein_coding or lnc_rna)
- **Examples**:
  - `gdc/genes/TCGA-CESC.csv.gz`
  - `geo/genes/GSE26284.csv.gz`

## Usage Notes

1. To match samples with their metadata, refer to the corresponding file in the `coldata/` directory.
2. For gene information, use the files in the `genes/` directory, which correspond to the columns in the expression data.
3. The order of samples in the expression data matches the order in the coldata files.
4. The order of genes in the expression data matches the order in the genes files.
5. Both `coldata/` and `genes/` directories use the same file naming convention: `DATASET.csv.gz`.

## References

[1] Lachmann A, Torre D, Keenan AB, Jagodnik KM, Lee HJ, Wang L, Silverstein MC, Ma'ayan A. Massive mining of publicly available RNA-seq data from human and mouse. Nature Communications 9. Article number: 1366 (2018), doi:10.1038/s41467-018-03751-6
