<img src="https://raw.githubusercontent.com/iamamofa/BACLAB/refs/heads/main/baclab.png" height="450">

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

```

## LIGHT database downloader (optional)

Colab storage is limited, so the notebook defaults to downloading only **small/light databases**.

### Default LIGHT set (ON)
- **`resfinder_db`** — AMR genes  
- **`mlst_db`** — MLST schemes  

### Optional (OFF by default; can be larger)
- **`pointfinder_db`** — AMR point mutations (species-specific)  
- **`plasmidfinder_db`** — plasmid replicons  
- **`virulencefinder_db`** — virulence genes  

### Database location
Databases are stored under:
- **`DB_ROOT`** *(recommended to point to Google Drive for persistence)*

### How to download
To download databases, set:
- **`RUN_DB_DOWNLOAD = True`**

---

## Output structure

All results are written to:

`/content/bacterial_pipeline_run/`

### Key folders
- **`trimmed/`**  
  Trimmed FASTQs (paired/unpaired for PE; single trimmed for SE)

- **`qc/`**  
  FastQC outputs + `multiqc_report.html`

- **`assembly_runs/`**  
  Full output per sample (Unicycler or SPAdes)

- **`assembled_files/`**  
  Final assemblies saved as:  
  - `SAMPLE.fasta`

- **`logs/`**  
  Per-sample logs for trimming and assembly

### Final export
At the end, the notebook creates and downloads a ZIP:

`baclab_bacterial_pipeline_results_<timestamp>.zip`

---

## How to run (quick steps)

1. Open `BACLAB_Bacterial_Pipeline_Colab_LIGHTDB.ipynb` in **Google Colab**
2. Run the installation cells (**Conda → env → tools**)
3. *(Optional)* Mount Drive and run **LIGHT database download**
4. Choose input mode:
   - `SINGLE_SAMPLE` **or** `SAMPLESHEET`
5. Run:
   - Trimming → FastQC/MultiQC → Assembly
6. Download the final ZIP

---

## Notes / Tips

- **Paired-end recommended:** Unicycler performs best with PE short reads.
- **Colab memory limits:** If assembly crashes, reduce `THREADS_ASSEMBLY` and run fewer samples per session.
- **Adapters:** Trimmomatic adapters are taken from the conda install. Choose the correct adapter set for your library prep (e.g., Nextera vs TruSeq).

---

## Troubleshooting

### No samples detected
- Ensure `MODE` is set correctly
- Check file paths exist
- Confirm samplesheet columns are correct (`sample`, `read_type`, etc.)

### File not found for a sample
- Confirm `DATA_DIR` is correct
- Ensure filenames in samplesheet match exactly (case-sensitive)
- Use absolute paths if needed

### Unicycler/SPAdes fails
- Reduce `THREADS_ASSEMBLY`
- Try running fewer samples per session
- Confirm input FASTQs are not corrupted

---

## Acknowledgements

This notebook uses standard open-source bioinformatics tools:
- Trimmomatic, FastQC, MultiQC, Unicycler, SPAdes  
and optional CGE database repositories for downstream analysis.
