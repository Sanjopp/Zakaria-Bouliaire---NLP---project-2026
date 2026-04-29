# Named Entity Recognition on the Archelec Corpus

**Sponsorship and Spatial Analysis of French Legislative Manifestos (1973–1993)**  
**Author:** Zakaria Bouliaire  
**Course:** Machine Learning for NLP — ENSAE 2026  
**Supervisor:** Christopher Kermorvant  

---

## Overview

This project applies Named Entity Recognition (NER) to the Archelec corpus, a collection of digitised French legislative electoral manifestos from 1973 to 1993.

The project follows two research questions:

1. **Sponsorship:** which politicians are cited by local candidates, and to what extent do they agree with them or reject them?
2. **Spatial discourse:** do candidates frame their discourse locally, nationally, or toward international stakes?

The full analysis is implemented in the Jupyter notebook:

```text
archelec_ner_analysis.ipynb
```

The written report is available in:

```text
Zakaria_BOULIAIRE_NLP_2026_Rapport.pdf
```

---

## Repository structure

```text
.
├── archelec_ner_analysis.ipynb
├── Zakaria_BOULIAIRE_NLP_2026_Rapport.pdf
├── README.md
├── LICENSE
├── .gitignore
└── data/
    ├── arkindex_archelec/
    │   └── text_files/
    │       ├── 1973/legislatives.zip
    │       ├── 1978/legislatives.zip
    │       ├── 1981/legislatives.zip
    │       ├── 1988/legislatives.zip
    │       └── 1993/legislatives.zip
    └── metadata/
        └── archelect_search.csv
```

The required data files are included in the repository. No manual data download is required to run the notebook.

---

## Installation

Clone the repository:

```bash
git clone https://github.com/Sanjopp/Zakaria-Bouliaire---NLP---project-2026.git
cd Zakaria-Bouliaire---NLP---project-2026
```

Install the required Python packages:

```bash
pip install pandas numpy matplotlib spacy jupyter notebook ipykernel
```

Install the French spaCy model used for NER:

```bash
python -m spacy download fr_core_news_lg
```

---

## Running the project

Launch Jupyter Notebook from the root of the repository:

```bash
python -m notebook
```

Then open:

```text
archelec_ner_analysis.ipynb
```

Run all cells in order.

The NER step may take several minutes because the notebook processes all candidate manifestos with `fr_core_news_lg`.

---

## Data and corpus construction

The corpus is organised by election year. Each year is stored as a zip archive containing one OCR text file per page of each manifesto.

The notebook uses the filename structure to recover metadata. For example:

```text
EL067_L_1973_03_059_10_1_PF_04.txt
```

encodes the department, election type, date, constituency, candidate number, round, document type and page number.

The notebook keeps only `PF` files, corresponding to the *professions de foi*, and discards `BV` files, corresponding to the *bulletins de vote*. It then groups and concatenates all pages belonging to the same candidate.

The final corpus contains **2,551 candidates** across five legislative elections:

| Year | Candidates |
|---:|---:|
| 1973 | 473 |
| 1978 | 478 |
| 1981 | 487 |
| 1988 | 562 |
| 1993 | 551 |

---

## Methodology

The notebook follows four main steps:

1. **Corpus loading and cleaning**  
   The OCR text files are loaded from the yearly archives. Repeated OCR watermark text is removed and whitespace is normalised.

2. **Named Entity Recognition**  
   The French spaCy model `fr_core_news_lg` is used to extract person entities (`PER`) and location entities (`LOC`).

3. **Political sponsorship analysis**  
   Person entities are cleaned, normalised and mapped to canonical political figures. The notebook studies who is cited, when they are cited, who cites whom, and whether citation contexts suggest alignment or distancing.

4. **Spatial discourse analysis**  
   Location entities are classified into local, national and international references in order to study the geographic framing of the manifestos.

---

## Main results

### Political figures

The most cited political figures include François Mitterrand, Arlette Laguiller, Charles De Gaulle, Michel Rocard, Valéry Giscard d'Estaing, Jean-Marie Le Pen and Jacques Chirac.

François Mitterrand dominates citation counts across the political spectrum, especially in 1981 and 1988. Charles De Gaulle remains a major reference after his death, and Jean-Marie Le Pen emerges progressively from 1988 onward.

### Alignment and distancing

The notebook uses a simple context-window method around each citation. It searches for positive and negative lexical markers in the words surrounding a political figure's name.

The resulting score is used as an exploratory proxy for alignment or distancing. The report stresses that this method is useful for interpretation but remains limited because it cannot fully capture negation, irony or administrative mentions.

### Spatial discourse

The geographic analysis shows that national references dominate the corpus.

The total number of classified geographic mentions is:

| Geographic category | Mentions |
|---|---:|
| Local | 1,528 |
| National | 11,863 |
| International | 4,563 |

International references rise from 1988 onward, consistently with the Maastricht debate. Europe is by far the most cited international location.

### Link between sponsorship and spatial discourse

The notebook computes correlations between citations of major political figures and geographic framing. The correlations are weak, suggesting that sponsorship and spatial discourse are largely independent rhetorical dimensions.

---

## Limitations

The analysis is exploratory and should be interpreted with several limitations in mind:

- the source texts are OCR transcriptions and therefore contain recognition errors;
- the spaCy model is applied to historical political manifestos, not to manually annotated Archelec data;
- person and location entities require cleaning and normalisation;
- the alignment analysis is lexicon-based and cannot capture all rhetorical nuances;
- the corpus covers five departments and should not be treated as fully representative of all French legislative campaigns.

---

## Troubleshooting

If `jupyter notebook` is not recognised, use:

```bash
python -m notebook
```

If the spaCy model is missing, run:

```bash
python -m spacy download fr_core_news_lg
```

If the notebook cannot find the data, make sure Jupyter was launched from the root of the repository, where the `data/` folder is located.

---

## License

This repository is distributed under the MIT License. See `LICENSE` for details.
