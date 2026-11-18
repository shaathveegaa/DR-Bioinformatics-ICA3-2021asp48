# Transcriptomic Analysis of Human Diabetic Retinopathy (GSE160306)

A bioinformatics project investigating gene-expression changes associated with diabetic retinopathy (DR) using human post-mortem retinal RNA-sequencing data from the **GSE160306** dataset.

The repository contains the analysis scripts, data-processing workflow, visualizations, result tables, and rendered reports used to explore retinal gene-expression patterns and compare non-proliferative diabetic retinopathy (NPDR) with healthy controls.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Aim and Objectives](#aim-and-objectives)
- [Dataset](#dataset)
- [Dataset Exploration and Quality Control](#dataset-exploration-and-quality-control)
- [Analysis Workflow](#analysis-workflow)
- [Results and Visualizations](#results-and-visualizations)
- [Differential Expression Results](#differential-expression-results)
- [Gene Ontology Enrichment](#gene-ontology-enrichment)
- [Repository Contents](#repository-contents)
- [Tools and Packages](#tools-and-packages)
- [How to Run the Project](#how-to-run-the-project)
- [Key Findings](#key-findings)
- [Limitations](#limitations)
- [Author](#author)
- [References](#references)

---

## Project Overview

Diabetic retinopathy is a diabetes-related eye disease that affects the retinal blood vessels and can lead to vision loss. Understanding changes in gene expression can help reveal biological processes associated with disease development.

This project analyses RNA-sequencing count data from human retinal samples in GEO dataset **GSE160306**. The workflow includes data exploration, quality-control checks, sample visualisation, differential gene-expression analysis, and Gene Ontology (GO) biological-process enrichment.

## Aim and Objectives

### Aim

To investigate transcriptomic changes associated with diabetic retinopathy using comparative analysis of human retinal RNA-sequencing data from GSE160306.

### Objectives

1. Obtain and inspect the GSE160306 raw gene-count dataset.
2. assess the structure and quality of the count matrix.
3. Organise sample metadata, including disease group and retinal site.
4. Explore sample-level variation using library-size analysis, principal component analysis (PCA), and heatmaps.
5. Identify differentially expressed genes in the NPDR-versus-Control comparison.
6. Explore the biological processes represented by the differentially expressed genes using GO enrichment analysis.

---

## Dataset

| Property | Description |
|---|---|
| GEO accession | GSE160306 |
| Database | NCBI Gene Expression Omnibus (GEO) |
| Organism | *Homo sapiens* |
| Data type | RNA sequencing (RNA-seq) |
| Tissue | Human post-mortem retinal samples |
| Input data | Raw gene-count matrix |
| Gene identifier | Ensembl gene ID |
| Number of samples | 79 |
| Number of gene features | 58,051 |

**Dataset file:** `GSE160306_human_retina_DR_totalRNA_counts.txt`

**Source:** [GSE160306 – NCBI Gene Expression Omnibus](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE160306)

The raw dataset contains one Ensembl gene-ID column and 79 sample-count columns. If the raw data file is not included in this repository, download it from the GEO source and place it in the project's `data/` directory.

---

## Dataset Exploration and Quality Control

The imported raw count matrix was checked for its dimensions, missing values, duplicate gene identifiers, and count range.

| Metric | Result | Description |
|---|---:|---|
| Ensembl gene IDs | 58,051 | Number of gene features in the original matrix |
| Biological samples | 79 | Number of retinal samples |
| Total matrix columns | 80 | One gene-ID column plus 79 sample columns |
| Missing values | 0 | No missing entries detected |
| Duplicate gene IDs | 0 | No duplicated Ensembl identifiers detected |
| Minimum raw count | 0 | Lowest observed count |
| Maximum raw count | 2,983,141 | Highest observed count |
| Expressed genes retained after filtering | 27,457 | Genes with at least 10 counts in at least 2 samples |

Low-expression filtering retained 27,457 genes for downstream statistical modelling.

### Sample distribution

The 79 retinal samples were grouped by disease category and retinal site:

| Disease group | Retinal site | Sample count |
|---|---|---:|
| Control | Macula | 10 |
| Control | Periphery | 10 |
| Diabetic (no retinopathy) | Macula | 10 |
| Diabetic (no retinopathy) | Periphery | 10 |
| DME / PDR | Macula | 10 |
| DME / PDR | Periphery | 10 |
| NPDR | Macula | 9 |
| NPDR | Periphery | 10 |
| **Total** | **All groups** | **79** |

The dataset represents 43 donor eyes. Sample metadata and group assignments are used to interpret the visualisations and comparisons.

---

## Analysis Workflow

The project follows these main stages:

1. **Data acquisition:** Obtain the GSE160306 RNA-seq count matrix from NCBI GEO.
2. **Data import:** Load the raw counts into R and identify the Ensembl gene-ID column and sample columns.
3. **Data exploration:** Check matrix dimensions, missing values, duplicated identifiers, and count ranges.
4. **Metadata preparation:** Organise sample identifiers, disease groups, and retinal sites.
5. **Quality control and visualisation:** Examine library sizes, sample distribution, PCA, and gene-expression patterns.
6. **Gene filtering and transformation:** Remove low-expression genes and prepare data for exploratory analysis.
7. **Differential expression:** Use DESeq2 to compare NPDR samples with healthy Control samples.
8. **Functional interpretation:** Perform GO Biological Process enrichment analysis on the selected differentially expressed genes.

---

## Results and Visualizations

The plots below are included from the project's `results/plots/` directory. GitHub will display them in this README when the image files are committed at the paths shown.

### 1. Distribution of retinal samples across groups

This plot summarises the number of samples in each disease-group and retinal-site combination.

![Distribution of retinal samples across groups](results/plots/03_sample_count_plot.png)

### 2. Sequencing library sizes

This plot compares the mapped sequencing read counts across the 79 retinal samples. The reported library sizes range from approximately 16.7 million to 40.1 million reads, with an average of approximately 26.4 million reads.

![Sequencing library sizes across retinal samples](results/plots/library_sizes.png)

### 3. Principal Component Analysis (PCA)

PCA is used to explore similarities and differences between samples based on their gene-expression profiles. The first two principal components account for 51.97% of the total variance (PC1: 31.47%; PC2: 20.50%).

![PCA scatter plot of retinal gene-expression profiles](results/plots/02_PCA_scatter_plot.png)

### 4. Annotated heatmap of the top 30 most variable genes

The heatmap displays expression patterns for the 30 most variable genes across the retinal samples, together with sample annotations.

![Annotated heatmap of the top 30 most variable genes](results/plots/01_annotated_heatmap.png)

### 5. Volcano plot: NPDR versus Control

The volcano plot displays the estimated log2 fold change and adjusted statistical significance for genes in the NPDR-versus-Control comparison.

![Volcano plot of differential expression in NPDR versus Control](results/plots/volcano_NPDR_Control.png)

---

## Differential Expression Results

Differential expression analysis was performed for **NPDR versus healthy Control** using DESeq2.

The reported analysis identified **46 statistically significant differentially expressed genes (DEGs)** using the criteria:

- Adjusted p-value (FDR) < 0.05
- Absolute log2 fold change > 1

| Expression direction | Number of genes |
|---|---:|
| Upregulated | 10 |
| Downregulated | 36 |
| **Total significant DEGs** | **46** |

In this comparison:

- **Upregulated** means the gene has higher estimated expression in NPDR than in Control.
- **Downregulated** means the gene has lower estimated expression in NPDR than in Control.
- A positive log2 fold change indicates higher expression in NPDR for this contrast.
- A negative log2 fold change indicates lower expression in NPDR for this contrast.

### Examples of identified genes

| Gene symbol | log2 fold change | Adjusted p-value | Direction |
|---|---:|---:|---|
| *PLA2G4C* | +2.83 | 3.18 × 10⁻⁴ | Upregulated |
| *SLC38A11* | +2.78 | 6.44 × 10⁻³ | Upregulated |
| *ACACB* | +1.86 | 1.22 × 10⁻² | Upregulated |
| *RGS5* | −1.03 | 3.18 × 10⁻⁴ | Downregulated |
| *ADAMTS4* | −1.61 | 3.18 × 10⁻⁴ | Downregulated |
| *INHBA* | −1.92 | 1.16 × 10⁻² | Downregulated |
| *EDNRA* | −1.08 | 8.91 × 10⁻³ | Downregulated |
| *ADAMTS9* | −1.16 | 3.49 × 10⁻² | Downregulated |

These are examples from the reported NPDR-versus-Control results; consult the result tables for the complete list.

---

## Gene Ontology Enrichment

GO Biological Process over-representation analysis identified **8 statistically significant terms** using an adjusted p-value threshold of less than 0.05.

One of the highlighted results was:

| GO term ID | Biological process | Adjusted p-value | Associated genes |
|---|---|---:|---|
| `GO:0030198` | Extracellular matrix organization | 0.0375 | *CYP1B1*, *ADAMTS4*, *ADAMTS9*, *COL6A6* |

Other enriched terms in the reported results included extracellular structure organisation and responses to gonadotropin-related stimuli.

The enrichment results suggest that extracellular matrix organisation and related biological processes may be relevant to the NPDR-versus-Control gene-expression differences observed in this dataset.

---

## Key Findings

- The raw count matrix contained 58,051 gene features and 79 retinal samples.
- Initial quality checks found no missing values and no duplicate Ensembl gene identifiers.
- Filtering retained 27,457 expressed genes for downstream modelling.
- The first two PCA components explained 51.97% of the total variance.
- The NPDR-versus-Control comparison identified 46 significant DEGs: 10 upregulated and 36 downregulated.
- GO analysis reported 8 significant Biological Process terms, including extracellular matrix organisation.

These findings describe the analysed dataset and the reported comparisons. They should be interpreted in the context of the study design, sample metadata, and limitations of post-mortem tissue data.

---

## Repository Contents

The following is the intended organisation of the main project files. Actual filenames and folders in the repository should be kept consistent with this structure.

```text
DR-Bioinformatics-ICA3-2021asp48/
│
├── code/
│   ├── 01_*.R
│   ├── 02_*.R
│   ├── 03_*.R
│   ├── 04_*.R
│   ├── 05_*.R
│   ├── 06_*.R
│   ├── 07_*.R
│   ├── 08_*.R
│   ├── 09_*.R
│   └── visualization scripts
│
├── data/
│   └── GSE160306_human_retina_DR_totalRNA_counts.txt
│
├── results/
│   ├── plots/
│   │   ├── 01_annotated_heatmap.png
│   │   ├── 02_PCA_scatter_plot.png
│   │   ├── 03_sample_count_plot.png
│   │   ├── library_sizes.png
│   │   └── volcano_NPDR_Control.png
│   │
│   └── tables/
│
├── report/
│   ├── diabetic_retinopathy_transcriptomics_report.qmd
│   ├── diabetic_retinopathy_transcriptomics_report.html
│   └── diabetic_retinopathy_transcriptomics_report.pdf
│
├── .gitignore
└── README.md
```

### Deliverables

- **Rendered PDF report:** [`report/diabetic_retinopathy_transcriptomics_report.pdf`](report/diabetic_retinopathy_transcriptomics_report.pdf)
- **Rendered HTML report:** [`report/diabetic_retinopathy_transcriptomics_report.html`](report/diabetic_retinopathy_transcriptomics_report.html)
- **Quarto source notebook:** [`report/diabetic_retinopathy_transcriptomics_report.qmd`](report/diabetic_retinopathy_transcriptomics_report.qmd)
- **Analysis scripts:** [`code/`](code/)
- **Plots:** [`results/plots/`](results/plots/)
- **Result tables:** [`results/tables/`](results/tables/)

---

## Tools and Packages

### Tools

- R
- RStudio
- Quarto
- Git
- GitHub

### R packages

The workflow uses packages for data manipulation, statistical analysis, annotation, enrichment, and visualisation, including:

- `DESeq2`
- `ggplot2`
- `dplyr`
- `tidyr`
- `pheatmap`
- `AnnotationDbi`
- `org.Hs.eg.db`
- `clusterProfiler`

Install any missing packages before running the scripts. The exact packages required depend on the script being executed.

---

## How to Run the Project

1. Clone the repository:

   ```bash
   git clone https://github.com/shaathveegaa/DR-Bioinformatics-ICA3-2021asp48.git
   ```

2. Open the cloned project folder in RStudio.

3. If the raw count matrix is not included, download `GSE160306_human_retina_DR_totalRNA_counts.txt` from [NCBI GEO GSE160306](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE160306).

4. Place the dataset in the `data/` directory, following the path expected by the scripts.

5. Install the required R packages.

6. Run the numbered scripts in `code/` in the intended order. Check each script's input and output paths before running it.

7. To regenerate the report, open the Quarto file:

   `report/diabetic_retinopathy_transcriptomics_report.qmd`

   Render it using Quarto in RStudio or from the terminal:

   ```bash
   quarto render report/diabetic_retinopathy_transcriptomics_report.qmd
   ```

---

## Limitations

- The analysis depends on the quality and availability of the public GSE160306 dataset.
- Correct sample metadata and disease-group assignments are essential for valid comparisons.
- Retinal site and donor-related biological variation can influence gene-expression patterns and should be considered when interpreting results.
- Differential expression results represent statistical associations and do not, by themselves, establish causation.
- Findings from this dataset require further validation using independent datasets or experimental studies before clinical conclusions can be made.

---

## Author

**Shaathveegaa Rajeshkannan**  
BSc (Hons) in Computer Science  
University of Vavuniya, Sri Lanka

## Repository

[DR-Bioinformatics-ICA3-2021asp48](https://github.com/shaathveegaa/DR-Bioinformatics-ICA3-2021asp48)

## References

1. NCBI Gene Expression Omnibus. **GSE160306**. https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE160306
2. Love, M. I., Huber, W., & Anders, S. (2014). Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2. *Genome Biology*, 15, 550. https://doi.org/10.1186/s13059-014-0550-8
3. R Project for Statistical Computing. https://www.r-project.org/
4. Bioconductor. https://www.bioconductor.org/
