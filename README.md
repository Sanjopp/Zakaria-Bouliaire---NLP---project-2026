# NLP Project 2026 — Archelec Corpus Analysis

**Author:** Zakaria Bouliaire  
**Institution:** ENSAE Paris  
**Course:** Machine Learning for NLP  
**Supervisor:** Christopher Kermorvant  
**Deadline:** April 30, 2026

---

## Project overview

This project applies Named Entity Recognition to the Archelec corpus, a collection of digitised French legislative electoral manifestos from the period 1973-1993.

The project investigates two questions:

1. **Sponsorship**: which national political figures did candidates choose to invoke, and does that choice reflect alignment or distancing?
2. **Spatial discourse**: do candidates frame their discourse locally, nationally, or toward international stakes?

The full analysis is implemented in:

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
└── .gitignore
```

---

## Data required to run the notebook

The raw data is not included in this repository. Two external data sources are required.

### 1. Archelec corpus transcriptions

The corpus transcriptions should be obtained from:

```text
https://gitlab.teklia.com/ckermorvant/arkindex_archelec
```

The notebook expects the corpus files to be located in:

```text
data/arkindex_archelec/text_files/
```

with the following structure:

```text
data/arkindex_archelec/text_files/1973/legislatives.zip
data/arkindex_archelec/text_files/1978/legislatives.zip
data/arkindex_archelec/text_files/1981/legislatives.zip
data/arkindex_archelec/text_files/1988/legislatives.zip
data/arkindex_archelec/text_files/1993/legislatives.zip
```

The notebook uses the following election years:

```text
1973
1978
1981
1988
1993
```

Each `legislatives.zip` archive contains one text file per page of each document.

The notebook keeps only the `PF` files, corresponding to `profession de foi`, and discards the `BV` files, corresponding to `bulletin de vote`.

### 2. Candidate metadata

The candidate metadata should be downloaded from:

```text
https://archelec.sciencespo.fr/explorer
```

The notebook expects the metadata CSV at:

```text
data/metadata/archelect_search.csv
```

This file is used to retrieve candidate-level information, especially the party field:

```text
titulaire-soutien
```

---

## Expected local data structure

Before running the notebook, the project folder should look like this:

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
    │       ├── 1973/
    │       │   └── legislatives.zip
    │       ├── 1978/
    │       │   └── legislatives.zip
    │       ├── 1981/
    │       │   └── legislatives.zip
    │       ├── 1988/
    │       │   └── legislatives.zip
    │       └── 1993/
    │           └── legislatives.zip
    └── metadata/
        └── archelect_search.csv
```

If the data is not placed exactly in these paths, the notebook will print missing-file messages such as:

```text
Missing: data/arkindex_archelec/text_files/<year>/legislatives.zip
```

or fail when loading:

```text
data/metadata/archelect_search.csv
```

---

## Installation

A clean Python environment is recommended.

### Option 1: using `venv`

From the repository root:

```bash
python -m venv .venv
```

Activate the environment on macOS or Linux:

```bash
source .venv/bin/activate
```

Activate the environment on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

### Option 2: using `conda`

```bash
conda create -n archelec-nlp python=3.13
conda activate archelec-nlp
```

### Install Python packages

Install the packages required by the notebook and by the existing project instructions:

```bash
pip install spacy pandas numpy matplotlib seaborn scikit-learn notebook
```

Then install the French spaCy model used for Named Entity Recognition:

```bash
python -m spacy download fr_core_news_lg
```

The notebook loads the model with:

```python
spacy.load("fr_core_news_lg")
```

---

## How to run the project

### Step 1: clone or download the repository

Place the repository on your machine and move into the project folder.

### Step 2: add the corpus files

Create the expected folders:

```bash
mkdir -p data/arkindex_archelec/text_files/1973
mkdir -p data/arkindex_archelec/text_files/1978
mkdir -p data/arkindex_archelec/text_files/1981
mkdir -p data/arkindex_archelec/text_files/1988
mkdir -p data/arkindex_archelec/text_files/1993
mkdir -p data/metadata
```

Then place the five corpus archives at:

```text
data/arkindex_archelec/text_files/1973/legislatives.zip
data/arkindex_archelec/text_files/1978/legislatives.zip
data/arkindex_archelec/text_files/1981/legislatives.zip
data/arkindex_archelec/text_files/1988/legislatives.zip
data/arkindex_archelec/text_files/1993/legislatives.zip
```

### Step 3: add the metadata file

Place the metadata CSV at:

```text
data/metadata/archelect_search.csv
```

### Step 4: install the dependencies

Run:

```bash
pip install spacy pandas numpy matplotlib seaborn scikit-learn notebook
python -m spacy download fr_core_news_lg
```

### Step 5: launch Jupyter

From the repository root, run:

```bash
jupyter notebook
```

Then open:

```text
archelec_ner_analysis.ipynb
```

### Step 6: run all cells

Run the notebook from top to bottom.

The NER step processes all candidate manifestos and can take several minutes. The notebook prints:

```text
Running NER on all candidates... (this may take a few minutes)
```

When the NER step is complete, the notebook prints the average number of person and location entities per manifesto.

---

## Corpus loading logic

The corpus is organised by election year. Each year is stored as a zip archive named:

```text
legislatives.zip
```

The filename structure gives a unique identifier for each candidate. For example:

```text
EL067_L_1973_03_059_10_1_PF_04.txt
```

This filename encodes:

```text
EL067       department code
L           legislative election
1973_03     March 1973
059         constituency number
10          candidate number within the constituency
1           first round
PF          profession de foi
04          page number
```

A single candidate can have several pages. The notebook groups and concatenates all pages belonging to the same candidate. The resulting candidate manifesto is the unit of analysis throughout the notebook.

---

## Corpus statistics

After parsing and grouping the corpus, the notebook loads:

```text
2,551 candidates
```

The distribution by election year is:

| Year | Candidates |
|---:|---:|
| 1973 | 473 |
| 1978 | 478 |
| 1981 | 487 |
| 1988 | 562 |
| 1993 | 551 |

The manifesto length distribution is:

| Statistic | Number of words |
|---|---:|
| Count | 2,551.0 |
| Mean | 5,875.6 |
| Standard deviation | 2,678.4 |
| Minimum | 643.0 |
| 25% | 3,772.0 |
| Median | 5,446.0 |
| 75% | 7,529.0 |
| Maximum | 17,078.0 |

---

## Notebook methodology

### Text preprocessing

The notebook removes the repeated OCR watermark:

```text
Sciences Po / fonds CEVIPOF
```

It also normalises whitespace.

The notebook deliberately avoids lowercasing everything, since spaCy relies on capitalisation to detect proper nouns.

### Named Entity Recognition

The project uses spaCy's French model:

```text
fr_core_news_lg
```

The notebook extracts two entity types:

```text
PER
LOC
```

`PER` is used for person names.

`LOC` is used for geographic locations.

The other entity types, such as `ORG` and `MISC`, are not used because they generate too much noise on this OCR corpus.

The raw NER extraction gives:

```text
Average persons per manifesto: 84.3
Average locations per manifesto: 93.0
```

The notebook uses `nlp.pipe()` for efficiency.

### Metadata and party classification

The metadata file contains candidate-level information and is loaded from:

```text
data/metadata/archelect_search.csv
```

The original party field used in the notebook is:

```text
titulaire-soutien
```

The raw party names are heterogeneous, with over 100 distinct values. The notebook maps them into simplified political families.

The resulting party distribution is:

| Party | Candidates |
|---|---:|
| Non mentionné | 1,514 |
| PCF | 321 |
| Extrême gauche | 250 |
| PS/Gauche | 155 |
| RPR/Gaulliste | 154 |
| UDF/Centre-droit | 81 |
| Divers centre | 27 |
| Divers gauche | 15 |
| FN | 10 |
| Divers droite | 10 |
| Écologiste | 8 |
| Extrême droite | 6 |

Candidates with no party mention are excluded from party-based analyses.

### Political personality analysis

The NER model initially detects many noisy person entities. The notebook applies filtering and normalisation before studying political figures.

The analysis then studies:

```text
who gets cited
when they are cited
who cites whom
whether citation contexts suggest alignment or distancing
```

### Context window analysis

For each citation of a target figure, the notebook extracts the 10 words before and after the mention.

It searches for positive and negative markers.

Positive markers include:

```text
soutien
fidèle
favorable
accord
victoire
```

Negative markers include:

```text
contre
opposition
danger
échec
dénonçons
```

The alignment score is:

```text
(positive markers - negative markers) / (positive markers + negative markers)
```

A score of `+1` indicates a positive lexical context.

A score of `-1` indicates a negative lexical context.

Only citations with at least one marker in context are included.

The score is used as an exploratory lexical proxy for alignment or distancing.

### Spatial discourse analysis

The notebook studies whether candidates frame their discourse locally, nationally, or internationally.

It uses the extracted `LOC` entities and classifies selected geographic references into three groups:

```text
local
national
international
```

The spatial classification relies on a curated list of unambiguous geographic references, including the five corpus departments, their main cities, and a set of foreign countries and supranational entities.

The five departments covered by the corpus are:

```text
Bas-Rhin
Haut-Rhin
Rhône
Pyrénées-Orientales
Hautes-Pyrénées
```

---

## Results summary

### Most cited political figures

The top 20 most cited political figures are:

| Rank | Figure | Citations |
|---:|---|---:|
| 1 | François Mitterrand | 6,149 |
| 2 | Arlette Laguiller | 1,480 |
| 3 | Charles De Gaulle | 1,403 |
| 4 | Michel Rocard | 1,278 |
| 5 | Valéry Giscard D'Estaing | 1,065 |
| 6 | Jean-Marie Le Pen | 837 |
| 7 | Jacques Chirac | 750 |
| 8 | Georges Pompidou | 708 |
| 9 | Brice Lalonde | 585 |
| 10 | Pierre Mauroy | 380 |
| 11 | Raymond Barre | 325 |
| 12 | Georges Marchais | 315 |
| 13 | Guy Mollet | 309 |
| 14 | Edgar Faure | 250 |
| 15 | Maurice Faure | 220 |
| 16 | André Lajoinie | 214 |
| 17 | Robert Fabre | 201 |
| 18 | Jean-Marie Votez | 141 |
| 19 | Michel Jobert | 136 |
| 20 | Jean Lecanuet | 134 |

The notebook highlights that Mitterrand dominates citation counts across the political spectrum.

### Geographic scope

The total number of classified geographic mentions is:

| Geographic category | Mentions |
|---|---:|
| Local | 1,528 |
| National | 11,863 |
| International | 4,563 |

National references dominate throughout the period.

The most cited international locations are:

| Location | Mentions |
|---|---:|
| Europe | 3,295 |
| Algérie | 425 |
| Allemagne | 320 |
| Russie | 178 |
| Afrique | 175 |
| Maroc | 56 |
| Chine | 44 |
| États-Unis | 38 |
| Israël | 32 |

### Link between sponsorship and spatial discourse

The notebook computes correlations between citation frequency and geographic scope for three major figures.

Correlation between figure citations and national geographic share:

| Figure | Correlation |
|---|---:|
| François Mitterrand | 0.028 |
| Charles De Gaulle | 0.023 |
| Jean-Marie Le Pen | -0.082 |

Correlation between figure citations and international geographic share:

| Figure | Correlation |
|---|---:|
| François Mitterrand | -0.032 |
| Charles De Gaulle | -0.047 |
| Jean-Marie Le Pen | 0.094 |

The notebook concludes that sponsorship and spatial discourse are largely independent rhetorical dimensions.

---

## Limitations discussed in the project

The project highlights several limitations.

First, the corpus comes from OCR transcriptions. OCR noise can create recognition errors, inconsistent capitalisation and layout artefacts.

Second, the spaCy model is applied directly to historical political manifestos. The raw NER output is noisy, especially for person entities.

Third, the alignment analysis is lexicon-based. It cannot reliably handle negation, irony or administrative citations.

Fourth, the geographic analysis is limited by the corpus design. The corpus covers only five departments and should not be interpreted as representative of all French legislative campaigns.

---

## Troubleshooting

### spaCy model not found

If the notebook raises an error such as:

```text
OSError: [E050] Can't find model 'fr_core_news_lg'
```

run:

```bash
python -m spacy download fr_core_news_lg
```

### Metadata file not found

If the notebook cannot find:

```text
data/metadata/archelect_search.csv
```

download the metadata from:

```text
https://archelec.sciencespo.fr/explorer
```

and place it exactly at:

```text
data/metadata/archelect_search.csv
```

### Corpus files not found

If the notebook prints missing-file messages, check that the five zip archives are placed exactly at:

```text
data/arkindex_archelec/text_files/1973/legislatives.zip
data/arkindex_archelec/text_files/1978/legislatives.zip
data/arkindex_archelec/text_files/1981/legislatives.zip
data/arkindex_archelec/text_files/1988/legislatives.zip
data/arkindex_archelec/text_files/1993/legislatives.zip
```

### NER step is slow

The NER step processes 2,551 candidate manifestos with `fr_core_news_lg`. The existing README notes that this step can take approximately 20-30 minutes on a standard CPU.

---

## License

This repository is distributed under the MIT License.

See:

```text
LICENSE
```
