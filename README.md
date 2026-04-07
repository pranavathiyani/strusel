# StruSel
### Structurome-wide Selectivity of pathogen-exclusive targets

![Status](https://img.shields.io/badge/status-active-brightgreen)
![Python](https://img.shields.io/badge/python-3.8+-blue)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## Overview

**StruSel** is a computational pipeline for identifying pathogen-exclusive drug target candidates through proteome-scale structural comparison. It goes beyond traditional sequence-based homology exclusion by leveraging AI-predicted 3D structures to uncover proteins that are structurally divergent from the human proteome — representing a largely unexplored space for selective antimicrobial drug discovery.

Given a target pathogen, StruSel:

1. Downloads AlphaFold2-predicted structures for the pathogen proteome
2. Performs structurome-wide structural similarity search against the human structurome using FoldSeek (3Di-based)
3. Performs parallel sequence-based search using BLASTp for benchmarking
4. Identifies proteins uniquely classified as non-homologous by structural (but not sequence) analysis
5. Cross-references with essential gene databases to filter for essential proteins
6. Maps candidates to CARD to assess AMR relevance
7. Outputs a ranked, annotated list of pathogen-exclusive target candidates

---

## Use Case

**Current implementation:** *Klebsiella pneumoniae* (WHO critical priority pathogen)

Planned extension: Pan-ESKAPE (*E. faecium*, *S. aureus*, *K. pneumoniae*, *A. baumannii*, *P. aeruginosa*, *Enterobacter* spp.)

---

## Pipeline Overview

```
Pathogen proteome (AlphaFold DB)
        │
        ▼
FoldSeek structural search ──────────────────┐
        │                                    │
BLASTp sequence search                       │
        │                                    │
        ▼                                    ▼
Structure-sequence decoupling          Non-homologs (structural)
        │
        ▼
Essential gene filtering (DEG / OGEE)
        │
        ▼
AMR mapping (CARD)
        │
        ▼
StruSel target candidates
```

---

## Repository Structure

```
strusel/
├── README.md
├── notebooks/
│   └── strusel_pipeline.ipynb    # Main Colab notebook
├── data/
│   └── .gitkeep                  # Input data (not tracked)
├── results/
│   └── .gitkeep                  # Output files (not tracked)
└── scripts/
    └── .gitkeep                  # Modular scripts (coming soon)
```

---

## Quick Start (Google Colab)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/pranavathiyani/strusel/blob/main/notebooks/strusel_pipeline.ipynb)

---

## Dependencies

- Python 3.8+
- FoldSeek
- BLAST+
- Biopython
- pandas, numpy
- requests (AlphaFold DB API)

---

## Citation

If you use StruSel, please cite:

> Pranavathiyani G. StruSel: Structurome-wide Selectivity of pathogen-exclusive targets. GitHub. https://github.com/pranavathiyani/strusel

*Manuscript in preparation.*

---

## Author

**Pranavathiyani G**  
Assistant Professor (Research), Division of Bioinformatics  
SASTRA Deemed University, Thanjavur  
[pranavathiyani@scbt.sastra.edu](mailto:pranavathiyani@scbt.sastra.edu)  
[Google Scholar](https://scholar.google.com/citations?user=iPMRqAoAAAAJ) | [ORCID](https://orcid.org/0000-0003-4854-8238)

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

*Part of the Bio-GRID AMR Atlas ecosystem — AMRfold | AMRscape | AMRnetwork | StruSel*
