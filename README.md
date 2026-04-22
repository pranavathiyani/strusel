# StruSel

### Structurome-wide Selectivity of pathogen-exclusive targets

[![Status](https://img.shields.io/badge/status-active-brightgreen)](https://github.com/pranavathiyani/strusel)
[![Python](https://img.shields.io/badge/python-3.8+-blue)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/pranavathiyani/strusel/blob/main/notebooks/strusel_pipeline.ipynb)

---

## Overview

**StruSel** is a computational pipeline for identifying pathogen-exclusive drug target candidates through proteome-scale structural comparison. It goes beyond traditional sequence-based homology exclusion by leveraging AI-predicted 3D structures to uncover proteins that are structurally divergent from the human proteome — representing a largely unexplored space for selective antimicrobial drug discovery.

Given a target pathogen, StruSel:

1. Downloads AlphaFold2-predicted structures for the pathogen proteome
2. Applies pLDDT-based quality filtering (≥ 70) to retain high-confidence structures
3. Performs structurome-wide structural similarity search against the human structurome using FoldSeek (3Di + GPU)
4. Performs parallel sequence-based search using MMseqs2 for benchmarking
5. Computes ESM2 protein language model embeddings for embedding-space similarity (prototype)
6. Classifies all proteins into a seven-category scheme (A/B/B2/C/C2/D/D*) based on sequence–structure concordance
7. Cross-references with essential gene databases (DEG) to filter for essential proteins
8. Maps candidates to CARD (via RGI) to assess AMR relevance
9. Outputs a tiered, annotated list of pathogen-exclusive target candidates

---

## Results: *Klebsiella pneumoniae* vs Human Structurome

### Input Scale

| Proteome | Total structures | pLDDT ≥ 70 | Source |
|---|---|---|---|
| *K. pneumoniae* (UP000007841) | 5,727 | 5,358 | AlphaFold DB v6 |
| *H. sapiens* (UP000005640) | 23,586 | 15,716 | AlphaFold DB v6 |
| *H. sapiens* (sequence) | 205,155 | — | UniProt |

### Seven-Category Classification

| Category | Description | Count |
|---|---|---|
| A | Seq-homolog + Struct-homolog | 445 |
| B | Seq-similar + Struct-divergent | 13 |
| B2 | Seq-similar + Struct-partial | 5 |
| C | Structural mimic (no seq hit) | 1,167 |
| C2 | Partial structural mimic (no seq hit) | 287 |
| D | StruSel candidate (no seq hit, low TM) | 151 |
| D* | High-confidence StruSel candidate (no seq/struct hit) | 3,290 |
| | **Total** | **5,358** |

### Target Tiers

| Tier | Definition | Count |
|---|---|---|
| **Tier 1** | Essential + D/D* + no AMR (prime targets) | **1,412** |
| **Tier 2** | Essential + D/D* + AMR-associated | 16 |
| **Tier 3** | Essential + C/C2 + no AMR (structural mimics) | 895 |
| **Tier 4** | Essential + C/C2 + AMR-associated | 7 |

### Tier 1 Functional Profile

- **Enzymatic targets:** 277 / 1,412 (19.6%)
- **Membrane-associated:** 466
- **Transcription regulators:** 219
- **Transporters:** 355
- **Top molecular functions:** DNA-binding transcription factor activity (171), ATP binding (155), metal ion binding (113)

### Pipeline Parameters

| Parameter | Value |
|---|---|
| pLDDT cutoff | ≥ 70.0 |
| MMseqs2 e-value | 1e-5 |
| MMseqs2 sensitivity | 7.5 |
| Query/target coverage | ≥ 0.7 |
| FoldSeek e-value | 0.001 |
| TM-score homolog | ≥ 0.5 |
| TM-score unrelated | < 0.3 |
| ESM2 cosine similarity | ≤ 0.5 |

---

## Use Case

**Current implementation:** *Klebsiella pneumoniae* (WHO critical priority pathogen)

**Planned extension:** Pan-ESKAPE (*E. faecium*, *S. aureus*, *K. pneumoniae*, *A. baumannii*, *P. aeruginosa*, *Enterobacter* spp.)

---

## Pipeline Overview

```
Pathogen proteome (AlphaFold DB)
        │
        ▼
pLDDT quality filter (≥ 70)
        │
        ├──────────────────────────────────┐
        ▼                                  ▼
MMseqs2 sequence search          FoldSeek structural search (GPU)
        │                                  │
        ├──────────────────────────────────┤
        ▼                                  ▼
  Sequence hits/non-homologs     Structural hits (TM-score bins)
        │                                  │
        └──────────┬───────────────────────┘
                   ▼
    Seven-category classification (A/B/B2/C/C2/D/D*)
                   │
                   ▼
    Essential gene filtering (DEG)
                   │
                   ▼
    AMR mapping (CARD-RGI)
                   │
                   ▼
    Tiered target candidates (Tier 1–4)
                   │
                   ▼
    UniProt GO/pathway annotation
```

---

## Repository Structure

```
strusel/
├── README.md
├── LICENSE
├── notebooks/
│   └── strusel_pipeline.ipynb    # Full Colab pipeline (12 cells)
├── scripts/
│   └── .gitkeep
└── results/
    ├── strusel_classification.tsv
    ├── strusel_tier1_prime_targets.tsv
    ├── strusel_tier2_amr_targets.tsv
    ├── strusel_tier3_mimic_risk.tsv
    ├── strusel_tier4_amr_mimic.tsv
    ├── strusel_top_amr_targets.tsv
    ├── tier1_uniprot_annotations.tsv
    └── StruSel_Report_Klebsiella.pdf
```

---

## Quick Start (Google Colab)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/pranavathiyani/strusel/blob/main/notebooks/strusel_pipeline.ipynb)

The pipeline runs end-to-end on Google Colab (GPU runtime, ~45 min). All dependencies are installed within the notebook.

---

## Dependencies

- Python 3.8+
- FoldSeek (GPU-enabled)
- MMseqs2
- DIAMOND (for RGI)
- ESM2 (`fair-esm`)
- RGI (CARD)
- Biopython, pandas, numpy, matplotlib, seaborn

---

## Citation

If you use StruSel, please cite:

> Pranavathiyani G. StruSel: Structurome-wide Selectivity of pathogen-exclusive targets. GitHub. https://github.com/pranavathiyani/strusel

---

## Author

**Pranavathiyani Gnanasekar**
Assistant Professor (Research), Division of Bioinformatics
SASTRA Deemed University, Thanjavur
[pranavathiyani@scbt.sastra.edu](mailto:pranavathiyani@scbt.sastra.edu)
[Google Scholar](https://scholar.google.com/citations?user=iPMRqAoAAAAJ) | [ORCID](https://orcid.org/0000-0003-4854-8238)

---

## License

MIT License. See [LICENSE](LICENSE) for details.
