# NLP Project 2026 — Archelec Corpus Analysis
**Author:** Zakaria Bouliaire — ENSAE Paris  
**Course:** Machine Learning for NLP

---

## Project overview

This project applies Named Entity Recognition to the Archelec corpus — a collection of digitised French legislative electoral manifestos (1973–1993) — to investigate two questions:

1. **Sponsorship**: which politicians are cited by local candidates, and to what extent do they agree with them or reject them?
2. **Spatial discourse**: do candidates focus their manifestos in local level politic or national and global ones?

The full analysis and results are documented in the report (`Zakaria_BOULIAIRE_NLP_2026_Rapport.pdf`).

---

## How to run the notebook

Two data sources are required before running the notebook:

- **Corpus transcriptions**: clone from `https://gitlab.teklia.com/ckermorvant/arkindex_archelec` into `data/arkindex_archelec/`
- **Metadata CSV**: download from `https://archelec.sciencespo.fr/explorer` and place at `data/metadata/archelect_search.csv`

Then install the dependencies and run all cells in order. The NER step takes approximately 20–30 minutes on a standard CPU.

```bash
pip install spacy pandas matplotlib seaborn scikit-learn
python -m spacy download fr_core_news_lg
```
