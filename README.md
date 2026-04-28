# NLP Project 2026 — Archelec Corpus Analysis
**Author:** Zakaria Bouliaire — ENSAE Paris  
**Course:** Machine Learning for NLP, supervised by Christopher Kermorvant  
**Deadline:** April 30, 2026  

---

## Project overview

This project applies Named Entity Recognition to the Archelec corpus — a collection of digitised French legislative electoral manifestos (1973–1993) — to investigate two questions:

1. **Sponsorship**: which national political figures do local candidates invoke, and does that reflect alignment or distancing?
2. **Spatial discourse**: do candidates frame their discourse locally or project toward national and international stakes?

The full analysis and results are documented in the scientific report (`Zakaria_BOULIAIRE_NLP_2026_Rapport.pdf`).

---

## How to run the notebook

### Requirements

```bash
pip install spacy pandas matplotlib seaborn scikit-learn
python -m spacy download fr_core_news_lg
```

### Data

Two data sources are required:

- **Corpus transcriptions**: clone from `https://gitlab.teklia.com/ckermorvant/arkindex_archelec` into `data/arkindex_archelec/`
- **Metadata CSV**: download from `https://archelec.sciencespo.fr/explorer` and place at `data/metadata/archelect_search.csv`

### Run

Open the notebook and run all cells in order. The NER step (cell 10) takes approximately 20–30 minutes on a standard CPU.

---

## Results summary

- Mitterrand dominates citation counts across all parties, peaking in 1981 and 1988
- National geographic references account for 65% of all mentions throughout the period
- International references rise sharply from 1988, consistent with the Maastricht debate
- Sponsorship and spatial discourse are empirically independent rhetorical dimensions
