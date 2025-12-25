# Immune State Geometry: Critical Transitions in TCR-Driven Immune Systems

**PNAS Submission | Preprint Repository**

Geometric constraints on immune state organization and critical transitions in TCR-driven immune systems.

---

## Overview

This repository contains the complete reproducible analysis pipeline, data processing scripts, and publication figures for investigating how network modularity and spectral properties constrain immune state organization. We reveal non-monotonic relationships between modularity (Q) and spectral energy that define permissible state spaces in healthy and disease states.

**Key Findings:**
- U-shaped geometry in immune state space (modularity vs. spectral energy)
- Opposite correlations in Discovery (CLARITY) vs. Validation (STEPHENSON) cohorts signal regime-like transitions
- Spectral fingerprints differ systematically between healthy and disease states
- Coupling law prevents certain structural configurations at the network level

---

## Repository Structure

```
.
├── code/                          # Analysis and visualization scripts
│   ├── audit_datasets.py         # Data quality assessment and H5AD validation
│   ├── audit_sparse.py           # Sparse matrix auditing (CSR/CSC)
│   ├── generate_publication_figures.py  # Main figure generation pipeline
│   ├── generate_fig_s2.py        # Supplementary figure (sampling robustness)
│   └── generate_supplementary_figures.py # Additional figures (density, lineage)
│
├── data/                          # Public data references and metadata
│   ├── DATA_SOURCES.md           # Complete data provenance and accession numbers
│   └── metadata_template.csv     # Expected column structure for input data
│
├── results/                       # Output directory (generated during pipeline)
│   ├── metrics/                  # Computed Q, lambda_max, spectral energy
│   ├── spectra/                  # Eigenvalue distributions
│   ├── publication_figures/      # High-resolution publication-ready figures
│   └── audit/                    # Data quality audit reports
│
├── docs/                          # Documentation
│   ├── REPRODUCIBILITY.md        # Complete reproducibility protocol
│   ├── DATA_MANIFEST.md          # Detailed data specifications
│   ├── METHODS.md                # Computational methods and parameters
│   └── INSTALLATION.md           # Environment setup and dependencies
│
├── requirements.txt               # Python dependencies (version-locked)
├── environment.yml                # Conda environment specification
├── .gitignore                     # Git ignore rules (excludes large data files)
└── README.md                      # This file
```

---

## Quick Start

### Prerequisites
- Python 3.9+
- 8GB RAM minimum (16GB recommended for full dataset processing)
- ~20GB disk space for data + intermediate results

### Installation

```bash
# Clone repository
git clone https://github.com/elkinnavarro-glitch/immune-state-geometry-pnas.git
cd immune-state-geometry-pnas

# Create environment
conda env create -f environment.yml
conda activate immune-geometry

# Verify installation
python -c "import scanpy; import pandas; print('Dependencies installed')"
```

### Running the Pipeline

```bash
# 1. Audit input H5AD files (quality control)
python code/audit_datasets.py

# 2. Audit sparse matrix structure
python code/audit_sparse.py --file data/input.h5ad

# 3. Generate main publication figures
python code/generate_publication_figures.py

# 4. Generate supplementary figures
python code/generate_fig_s2.py
python code/generate_supplementary_figures.py
```

Figures are output to: `results/publication_figures/`

---

## Data Sources

### Public Datasets Used

| Dataset | Accession | Description | n Donors | Source |
|---------|-----------|-------------|----------|--------|
| **CLARITY** (Discovery) | GEO (Discovery CLARITY) | Acute viral infection (COVID-19) | 14-18 | [Link pending] |
| **STEPHENSON** (Validation) | GEO/SRA | Chronic viral dynamics (LCMV) | 12-16 | [Link pending] |
| **CellxGENE** | Census stable release | Integrated immune cell atlas | Public | [cellxgene.cziscience.com](https://cellxgene.cziscience.com) |

### Data Access

All data are **publicly available** from:
1. **GEO (Gene Expression Omnibus)**: Gene expression and TCR sequencing
2. **CellxGENE Census**: Single-cell data integration platform
3. **VDJdb/IEDB**: TCR repertoire reference databases

**Data reproducibility note:** Complete data download instructions and checksums provided in `docs/DATA_MANIFEST.md`.

---

## Key Scripts

### `audit_datasets.py`
Comprehensive H5AD file validation:
- Metadata completeness (donor, celltype, condition, density)
- Sparse matrix format detection (CSR vs. CSC)
- MD5 checksums for file integrity
- Safety limits enforcement (max density, max file size)

**Usage:**
```bash
python code/audit_datasets.py
```

### `generate_publication_figures.py`
Main analysis pipeline:
- **Fig 1**: Modularity and spectral metrics overview (Cliffs d effect size, rank-sum tests)
- **Fig 2**: U-shaped geometry with binned medians and permutation-based correlations
- **Fig 3**: Spectral fingerprints (KDE density, rank distribution)

**Output:** Publication-ready PDFs and PNGs (300 DPI)

### `audit_sparse.py`
Sparse matrix inspection:
- Density computation (NNZ / total elements)
- Format verification (CSR/CSC)
- Safe loading limits (prevents OOM errors on Windows)

---

## Reproducibility Protocol

### Environment Reproducibility

```bash
# Conda environment (recommended)
conda env create -f environment.yml
conda activate immune-geometry

# Or pip with frozen requirements
pip install -r requirements.txt --no-deps
```

### Computational Reproducibility

**Random seeds:** Fixed in all scripts
```python
np.random.seed(42)
rng = np.random.default_rng(seed=42)
```

**Data versioning:** MD5 checksums stored for all input files
```python
from audit_sparse import getfile_md5
md5 = getfile_md5('data/input.h5ad')
```

**Numerical precision:**
- Default float64 (double precision)
- Bootstrap resampling: n_boot = 2000 (95% CI)
- Permutation tests: n_perm = 5000

### Full Reproducibility Checklist

- [ ] Environment created from environment.yml or requirements.txt
- [ ] All dependencies installed (run `pip list --format=freeze`)
- [ ] Input data downloaded and checksummed (see DATA_MANIFEST.md)
- [ ] `code/audit_datasets.py` passes all checks
- [ ] `code/generate_publication_figures.py` runs without errors
- [ ] Output figures match reference images (visual inspection)
- [ ] Computational time < 30 minutes on standard hardware

See **`docs/REPRODUCIBILITY.md`** for detailed reproducibility guide.

---

## Methods Summary

### Network Construction
- Gene expression data from H5AD (AnnData) objects
- Cell-type specific subsampling (500-800 cells per configuration)
- Correlation thresholding for network binarization

### Metrics

**Modularity (Q):** Newman's modularity with Louvain community detection
**Spectral Properties:**
- λ_max: Leading eigenvalue
- λ_sum_out: Sum of eigenvalues outside empirical null (95th percentile)

**Null Models:**
- Configuration model (degree-preserving random graphs)
- Empirical null distribution for spectral bounds

### Statistical Tests

- **Rank-sum tests** (Mann-Whitney U) for group comparisons
- **Permutation-based Spearman correlation** with empirical p-values
- **Cliffs delta** with 95% bootstrap confidence intervals
- **LOWESS regression** for non-parametric trend fitting

---

## Figures

### Main Figures

**Figure 1 - Overview**
- Panel A: Conceptual network model (healthy modular vs. disease coupled)
- Panels B-C: Modularity and spectral metrics by group (Discovery cohort)

**Figure 2 - Geometric Constraints**
- U-shaped state space (modularity vs. spectral energy)
- Binned median analysis (equal-count bins, n=8)
- Paradox of signs: opposite correlations in opposing state arms

**Figure 3 - Spectral Fingerprints**
- Panel A: KDE density of eigenvalue spectra
- Panel B: Log-log rank vs. magnitude (dominant modes)

### Supplementary Figures

- **FigS1:** Density robustness (0.05 vs. 0.10)
- **FigS2:** Sampling depth (500 vs. 800 cells)
- **FigS3:** Lineage persistence (CD4+ T cells, Monocytes)

---

## Citation

If you use this repository, please cite:

```bibtex
@article{navarro2025geometry,
  title={Geometric constraints on immune state organization reveal critical transitions in TCR-driven systems},
  author={Navarro Quiroz, Elkin and [Collaborators]},
  journal={Proceedings of the National Academy of Sciences},
  year={2025},
  note={Preprint}
}
```

**GitHub Citation:**
```
Navarro Quiroz, E. (2025). immune-state-geometry-pnas: Code and data for PNAS submission. 
https://github.com/elkinnavarro-glitch/immune-state-geometry-pnas
```

---

## System Requirements

| Component | Requirement |
|-----------|-------------|
| **OS** | Linux, macOS, or Windows |
| **Python** | 3.9, 3.10, 3.11 |
| **RAM** | 8 GB minimum; 16+ GB recommended |
| **Disk** | ~20 GB (data + results) |
| **Runtime** | ~20-30 min (full pipeline) |

---

## Dependencies

**Core:**
- scanpy >= 1.9.0 (single-cell analysis)
- pandas >= 1.3.0 (data manipulation)
- numpy >= 1.20.0 (numerical computing)
- scipy >= 1.7.0 (scientific computing)

**Visualization:**
- matplotlib >= 3.4.0 (publication figures)
- seaborn >= 0.11.0 (statistical graphics)
- networkx >= 2.6 (network analysis)

**Statistics:**
- scikit-learn >= 0.24.0 (utilities)

Full environment specification in `environment.yml` and `requirements.txt`.

---

## Troubleshooting

### Common Issues

**Issue:** "Module 'scanpy' not found"
```bash
# Solution: Install with conda
conda install -c conda-forge scanpy
```

**Issue:** Memory error on Windows when loading large H5AD files
```bash
# Solution: Check sparse matrix density with audit_sparse.py
python code/audit_sparse.py --file data/large_file.h5ad
# If density > 10%, consider subsampling
```

**Issue:** Figures not being saved
```bash
# Solution: Create results directory
mkdir -p results/publication_figures
python code/generate_publication_figures.py
```

See `docs/REPRODUCIBILITY.md` for additional troubleshooting.

---

## License

MIT License - see LICENSE file for details. Research code is available for:
- Academic research
- Educational use
- Collaborative development

For commercial use, contact the authors.

---

## Contact & Support

**Author:** Elkin Navarro Quiroz  
**Institution:** Centro de Investigaciones en Ciencias de la Vida, Universidad Simón Bolívar, Colombia  
**Email:** elkinnavarro@gmail.com  
**ORCID:** [0000-0001-7567-6409](https://orcid.org/0000-0001-7567-6409)  
**GitHub:** [@elkinnavarro-glitch](https://github.com/elkinnavarro-glitch)

---

## Acknowledgments

This work leverages:
- **CellxGENE & CZI Single-Cell Initiative** for data infrastructure
- **GEO & NCBI** for public data archiving
- **Python scientific stack** (scanpy, scipy, networkx, matplotlib)
- **Colombian research funding** (Minciencias Convocatoria 50)

---

**Last updated:** December 2025  
**Status:** Preprint submitted to PNAS
