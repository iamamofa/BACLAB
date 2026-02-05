# BACLAB
### BACLAB Bacterial WGS Pipeline (Google Colab • Conda/Mamba)

This project provides a **Google Colab notebook** that runs a lightweight, reproducible bacterial WGS pipeline:

**Trimming (Trimmomatic) → QC (FastQC/MultiQC) → Assembly**
- **Paired-end (PE):** Unicycler (SPAdes backend)
- **Single-end (SE):** SPAdes (`--careful`)

It also includes an **optional “LIGHT database downloader”** (small-by-default) for common CGE databases.

---

## Notebook

- `BACLAB_Bacterial_Pipeline_Colab_LIGHTDB.ipynb`  
  Main Colab notebook with:
  1) Conda + env installs (first)  
  2) LIGHT DB downloads (optional)  
  3) Input selection (single sample or samplesheet)  
  4) Run pipeline + export ZIP  

---

## What gets installed (Conda environment)

The notebook creates a conda env (default: `baclab_wgs`) containing:

- `trimmomatic` — read trimming
- `fastqc` — QC on FASTQ files
- `multiqc` — summarizes QC into one report
- `unicycler` — PE assembly (uses SPAdes)
- `spades` — SE assembly and Unicycler backend
- `openjdk` — required for Trimmomatic
- `pandas` — samplesheet parsing

---

## Input modes (choose ONE)

### Mode A — `SINGLE_SAMPLE`
Run one sample by providing full FASTQ paths.

**Paired-end (PE):**
- `R1_PATH` = read 1 FASTQ
- `R2_PATH` = read 2 FASTQ

**Single-end (SE):**
- `SE_PATH` = single FASTQ

---

### Mode B — `SAMPLESHEET` + `DATA_DIR` (batch run)
Run multiple samples using a samplesheet (CSV/TSV) and a folder containing FASTQs.

- `DATA_DIR` = folder where FASTQs are stored  
- `SAMPLESHEET_PATH` = path to `samplesheet.csv` (or `.tsv`)

Filenames in the samplesheet can be:
- **relative** to `DATA_DIR` (recommended), or
- **absolute paths**.

---

## Samplesheet format

**Required columns (case-insensitive):**
- `sample`
- `read_type` (`PE` or `SE`)

**For PE samples:**
- `r1`
- `r2`

**For SE samples:**
- `r1` *(or `se`)*

### Example (CSV)
```csv
sample,read_type,r1,r2
EC001,PE,EC001_R1_001.fastq.gz,EC001_R2_001.fastq.gz
EC002,PE,EC002_R1_001.fastq.gz,EC002_R2_001.fastq.gz
EC003,SE,EC003.fastq.gz,
