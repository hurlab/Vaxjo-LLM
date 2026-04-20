# Vaxjo 2.0 & Vaxjo-LLM

**A Web-based Vaccine Adjuvant and Adjuvant Mechanism Knowledgebase Powered by Ontology and Large Language Model**

[![Status: Accepted](https://img.shields.io/badge/Status-Accepted%20for%20Publication-success)](#citation)
[![Journal](https://img.shields.io/badge/Journal-Frontiers%20in%20Cellular%20and%20Infection%20Microbiology-informational)](https://www.frontiersin.org/journals/cellular-and-infection-microbiology)
[![Section](https://img.shields.io/badge/Section-Microbial%20Vaccines-blueviolet)](#)
[![Database](https://img.shields.io/badge/Database-Vaxjo%202.0-orange)](https://violinet.org/vaxjo)
[![Python](https://img.shields.io/badge/Python-3.9%2B-blue)](https://www.python.org/)
[![Model](https://img.shields.io/badge/LLM-Llama%203.2-1f6feb)](#)

> **Web Interface:** [https://violinet.org/vaxjo](https://violinet.org/vaxjo)

---

## Table of Contents

- [About the Project](#about-the-project)
- [Abstract](#abstract)
- [Authors and Affiliations](#authors-and-affiliations)
- [Vaxjo-LLM Pipeline](#vaxjo-llm-pipeline)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Data & Outputs](#data--outputs)
- [Manuscript and Supplementary Materials](#manuscript-and-supplementary-materials)
- [Citation](#citation)
- [License](#license)
- [Contact](#contact)

---

## About the Project

**Vaxjo 2.0** is a significantly expanded and modernized release of the original Vaxjo database (2012), providing curated information on vaccine adjuvants — including names, storage conditions, structures, preparation methods, components, functions, safety profiles, and primary references. Vaxjo 2.0 grows the resource from **103** to **448** adjuvants organized into **16 categories**, with adjuvant entities and mechanism annotations represented in the **Vaccine Ontology (VO)** for standardized storage and exchange.

**Vaxjo-LLM** is the companion large-language-model (LLM) curation pipeline that accelerates adjuvant discovery and mechanism annotation. Using Llama 3.2 with carefully engineered prompts, the pipeline automatically extracts adjuvant names and immune-response mechanisms from PubMed literature, canonicalizes them, and produces structured mechanism summaries. Output was subsequently manually reviewed by expert curators to ensure consistency and accuracy before inclusion in the database.

This repository contains the **source code, prompts, datasets, outputs, and manuscript materials** underlying the Vaxjo-LLM pipeline described in the manuscript below.

---

## Abstract

**Introduction:** Vaccine adjuvants enhance immune responses by boosting vaccine efficacy, reducing required doses, and improving long-term immunity. The Vaxjo database is a web-based resource that stores information on vaccine adjuvants, including their names, storage conditions, structures, preparation methods, components, functions, safety, and references. The original version of Vaxjo, released in 2012 with 103 vaccine adjuvants, has been expanded and modernized as Vaxjo 2.0, a significantly enhanced version.

**Methods:** In Vaxjo 2.0, newly identified adjuvants from biomedical literature retrieved through PubMed searches, as well as from the Vaccine Adjuvant Compendium (VAC) and AdjuvareDB, were added. To accelerate data collection and curation, we developed a vaccine adjuvant large language model (Vaxjo-LLM) system that automatically identifies and annotates new vaccine adjuvants and characterizes their mechanisms. The LLM-mined results were manually evaluated, annotated, and selectively included in the database to ensure quality.

**Results:** Overall, Vaxjo 2.0 includes **448 vaccine adjuvants**, organized into **16 distinct categories** (e.g., mineral salt, emulsion, cytokine, peptide, and toll-like receptor (TLR) agonist vaccine adjuvants), all of which are represented in the Vaccine Ontology (VO) to streamline information storage and exchange. From **817 PubMed abstracts**, Vaxjo-LLM identified mechanisms for **323 unique vaccine adjuvants** across **16 mechanism families**, including T cell activation/polarization, dendritic cell activation, TLR signaling, inflammasome activation, cytokine signaling, B cell/antibody production, and pattern recognition receptor (PRR) sensing. The mined information was subsequently manually reviewed to ensure consistency and accuracy. Based on this analysis, the mechanism profiles and clustering of the top 20 adjuvants were generated, revealing shared and distinct mechanistic signatures. For deeper mechanistic understanding, Vaxjo 2.0 further classified adjuvants based on PRR families, including TLRs, C-type lectin receptors, NOD-like receptors, and RIG-I-like receptors. Vaccine adjuvants were also categorized based on host immune response profiles, such as Th1/Th2/Th17-biased, Th1/Th2-mixed, and Treg-biased immune profiles.

**Discussion:** The newly designed Vaxjo 2.0 web interface ([https://violinet.org/vaxjo](https://violinet.org/vaxjo)) provides an openly available and user-friendly platform for querying, visualizing, and analyzing vaccine adjuvant data.

---

## Authors and Affiliations

Joshua Monickaraj¹†, Hasin Rehana²,³†, Ani Bernardi¹, Yuping Zheng⁴, Taiyu Lin⁵, Le Liu⁶, Amogh Madireddi⁷, Leo Yeh⁸, Jie Zheng⁸, **Junguk Hur²\***, **Yongqun He⁸,⁹,¹⁰\***

† These authors contributed equally to this work.
\* Corresponding authors.

| # | Affiliation |
|---|-------------|
| 1 | College of Literature, Science, and the Arts, University of Michigan, Ann Arbor, MI, United States |
| 2 | Department of Biomedical Sciences, University of North Dakota, School of Medicine and Health Sciences, Grand Forks, ND, United States |
| 3 | School of Electrical Engineering & Computer Science, University of North Dakota, Grand Forks, ND, United States |
| 4 | School of Data Science, Chinese University of Hong Kong, Shenzhen, Guangdong, China |
| 5 | Division of Life Science, The Hong Kong University of Science and Technology, Hong Kong, China |
| 6 | Marine College, Shandong University, Weihai, Shandong, China |
| 7 | College of Engineering, University of Michigan, Ann Arbor, MI, United States |
| 8 | Unit for Laboratory Animal Medicine, University of Michigan, Ann Arbor, MI, United States |
| 9 | Department of Learning Health Sciences, University of Michigan Medical School, Ann Arbor, MI, United States |
| 10 | Center of Computational Medicine and Bioinformatics, University of Michigan Medical School, Ann Arbor, MI, United States |

---

## Vaxjo-LLM Pipeline

The `Vaxjo-LLM-2Phase-End2EndPipeline.ipynb` notebook implements a **two-stage LLM workflow** that transforms unstructured biomedical text into structured, consistent, and searchable mechanistic insights.

### Phase I — Adjuvant & Mechanism Extraction from PubMed Abstracts

- Reads a JSON file of PubMed articles (`{PMID: {title, abstract}}`).
- Uses **Llama 3.2** with a structured prompt to extract:
  - vaccine **adjuvant names**
  - associated **immune-response mechanism** descriptions
- Saves raw LLM responses to text and logs per-PMID status to CSV.

**Example output (JSON):**

```json
[
  {
    "adjuvant": "Alum",
    "immune_response_mechanism": "Activates NLRP3 inflammasome and promotes slow antigen release."
  }
]
```

### Intermediate — Canonicalization & Collapsing

- Maps variant adjuvant names to **canonical labels**.
- Collapses multiple papers into a single row per adjuvant with aggregated mechanism text.

### Phase II — Mechanism Summarization and Structuring

For each canonical adjuvant, Llama 3.2 is used to:

- Generate a multi-sentence **mechanistic summary**.
- Extract structured **mechanism subtypes** with supporting PMIDs.
- Write outputs to both human-readable `.txt` and machine-friendly `.jsonl` files in `Outputs/`.

**Example output (JSON):**

```json
{
  "adjuvant": "MPLA",
  "summary": "Stimulates TLR4 signaling, activating dendritic cells and promoting Th1 polarization.",
  "mechanism_types": [
    {
      "mechanism_type": "TLR4 activation",
      "evidence_refs": ["18479788"]
    }
  ]
}
```

### Postprocessing & Analysis

- Normalizes mechanism subtypes and assigns them to **standardized mechanism families**.
- Builds an **adjuvant × mechanism-family matrix** for downstream analysis.
- Computes mechanism-family frequencies and generates visualizations:
  - Mechanism family **pie chart**
  - Top-20 adjuvant **mechanism heatmap**
  - PRR-family classification (TLRs, CLRs, NLRs, RLRs)
  - Host immune-response profiles (Th1 / Th2 / Th17 / Treg)

---

## Repository Structure

```
Vaxjo-LLM/
├── Vaxjo-LLM-2Phase-End2EndPipeline.ipynb   # Main end-to-end notebook
├── Prompts/                                  # LLM prompts (Phase I, Phase II)
│   ├── Prompt_Phase I.txt
│   └── Prompt_Phase II.txt
├── Datasets/                                 # Input literature & curation data
│   ├── All PMID abstracts.txt
│   └── VO ID term editing.xlsx
├── Outputs/                                  # LLM outputs and aggregated results
│   ├── Vaxjo-LLM-Phase I-Response.txt
│   └── Vaxjo-LLM-Phase II-Response.txt
├── Manuscript/                               # Paper figures & supplementary files
│   ├── Figures/                              # FIGURE_2 … FIGURE_7 (SVG/EPS/PNG)
│   └── SupplementaryFiles/                   # Supplementary Files 1–7
├── v0/                                       # Archived initial prototype
├── v1/                                       # Archived intermediate version
├── requirements.txt                          # Python dependencies
└── README.md                                 # This file
```

---

## Getting Started

### Prerequisites

- Python **3.9+**
- Jupyter Notebook / JupyterLab
- A GPU is recommended for Llama 3.2 inference.
- Access to the Llama 3.2 model weights via Hugging Face (see [huggingface.co/meta-llama](https://huggingface.co/meta-llama)).

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/<your-org>/Vaxjo-LLM.git
cd Vaxjo-LLM

# 2. (Recommended) create a virtual environment
python -m venv .venv
source .venv/bin/activate        # On Windows: .venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt
```

### Running the Pipeline

```bash
jupyter notebook Vaxjo-LLM-2Phase-End2EndPipeline.ipynb
```

Execute cells sequentially to run Phase I → canonicalization → Phase II → postprocessing. Intermediate and final outputs will be written to the `Outputs/` directory.

---

## Data & Outputs

| Resource | Description |
|----------|-------------|
| `Datasets/All PMID abstracts.txt` | PubMed abstracts used as input to the pipeline |
| `Datasets/VO ID term editing.xlsx` | Vaccine Ontology (VO) term curation spreadsheet |
| `Outputs/Vaxjo-LLM-Phase I-Response.txt` | Raw Phase I extractions (adjuvant + mechanism) |
| `Outputs/Vaxjo-LLM-Phase II-Response.txt` | Phase II structured mechanism summaries |
| [Vaxjo 2.0 Web Interface](https://violinet.org/vaxjo) | Public interface for querying, visualizing, and analyzing vaccine adjuvant data |

---

## Manuscript and Supplementary Materials

All manuscript figures (SVG/EPS/PNG) and seven supplementary files (PDF/XLSX) are included in the `Manuscript/` directory for reproducibility and reference.

---

## Citation

Our manuscript has been **accepted for publication** (as of **April 19, 2026**; proofing in process) in:

> **Frontiers in Cellular and Infection Microbiology** — Section: *Microbial Vaccines*

**Title:** *Vaxjo 2.0: A Web-based Vaccine Adjuvant and Adjuvant Mechanism Knowledgebase Powered by Ontology and Large Language Model*

**Authors:** Joshua Monickaraj, Hasin Rehana, Ani Bernardi, Yuping Zheng, Taiyu Lin, Le Liu, Amogh Madireddi, Leo Yeh, Jie Zheng, Junguk Hur, Yongqun He

If you use Vaxjo 2.0 or the Vaxjo-LLM pipeline in your research, please cite the paper as follows once final publication details are available:

```bibtex
@article{Monickaraj2026Vaxjo2,
  title   = {Vaxjo 2.0: A Web-based Vaccine Adjuvant and Adjuvant Mechanism
             Knowledgebase Powered by Ontology and Large Language Model},
  author  = {Monickaraj, Joshua and Rehana, Hasin and Bernardi, Ani and
             Zheng, Yuping and Lin, Taiyu and Liu, Le and Madireddi, Amogh and
             Yeh, Leo and Zheng, Jie and Hur, Junguk and He, Yongqun},
  journal = {Frontiers in Cellular and Infection Microbiology},
  note    = {Section: Microbial Vaccines. Accepted for publication; in proof.},
  year    = {2026}
}
```

> *This citation will be updated with volume, pages, and DOI once the final version is published.*

---

## License

Unless otherwise noted, code in this repository is released for **academic and research use**. Please refer to the `LICENSE` file (if present) or contact the corresponding authors regarding redistribution and reuse.

---

## Contact

**Corresponding authors:**

- **Junguk Hur, Ph.D.** — University of North Dakota
  [jung.hur@und.edu](mailto:jung.hur@und.edu)
- **Yongqun "Oliver" He, Ph.D.** — University of Michigan
  [yongqunh@med.umich.edu](mailto:yongqunh@med.umich.edu)

**Lead developer (Vaxjo-LLM pipeline):**

- **Hasin Rehana** — Ph.D. Student, University of North Dakota
  [hasin.rehana@und.edu](mailto:hasin.rehana@und.edu)
  GitHub: [hasin-ruet13](https://github.com/hasin-ruet13)

---

*For questions, bug reports, or collaboration inquiries, please open an issue on GitHub or reach out to the corresponding authors.*
