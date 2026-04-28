# Named Entity Recognition on the Archelec Corpus

## Sponsorship and Spatial Analysis of French Legislative Manifestos (1973-1993)

**Author:** Zakaria Bouliaire  
**Institution:** ENSAE Paris  
**Course:** Machine Learning for NLP - ENSAE 2026  
**Supervisor:** Christopher Kermorvant  

---

## Project overview

This repository contains a Natural Language Processing project on the Archelec corpus, a collection of digitised French legislative electoral manifestos covering the elections of 1973, 1978, 1981, 1988 and 1993.

The project applies Named Entity Recognition to the OCR-transcribed manifestos in order to study two dimensions of French electoral discourse:

1. **Sponsorship**: which politicians are cited by local candidates, and to what extent do they agree with them or reject them?
2. **Spatial discourse**: do candidates focus their manifestos in local level politic or national and global ones?

The analysis is implemented in the notebook:

```text
archelec_ner_analysis.ipynb
```

The final written report is provided in:

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

The repository contains the notebook, the report, the license and this README. The raw Archelec data files are not included in the repository and must be added manually before running the notebook.

---

## Data required to run the project

Two external data sources are required.

### 1. Archelec OCR text files

The corpus transcriptions should be obtained from the Archelec GitLab repository:

```text
https://gitlab.teklia.com/ckermorvant/arkindex_archelec
```

The notebook expects the text files to be stored as yearly zip archives. The expected path is:

```text
data/arkindex_archelec/text_files/
```

The final structure must be:

```text
data/
└── arkindex_archelec/
    └── text_files/
        ├── 1973/
        │   └── legislatives.zip
        ├── 1978/
        │   └── legislatives.zip
        ├── 1981/
        │   └── legislatives.zip
        ├── 1988/
        │   └── legislatives.zip
        └── 1993/
            └── legislatives.zip
```

The notebook uses the following years:

```python
YEARS = ["1973", "1978", "1981", "1988", "1993"]
```

Only `PF` files, corresponding to *professions de foi*, are kept. `BV` files, corresponding to *bulletins de vote*, are discarded because they are too short to carry meaningful discourse.

### 2. Archelec candidate metadata

Candidate-level metadata should be downloaded from the Archelec explorer:

```text
https://archelec.sciencespo.fr/explorer
```

The metadata CSV must be placed at:

```text
data/metadata/archelect_search.csv
```

The notebook uses this file to retrieve candidate-level information, especially the party field:

```text
titulaire-soutien
```

---

## Data format

Each corpus file is a text file corresponding to one page of a candidate document. The filename contains the metadata needed to reconstruct candidate-level documents.

Example filename:

```text
EL067_L_1973_03_059_10_1_PF_04.txt
```

This filename is parsed as follows:

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

Since one candidate may have several pages, the notebook groups and concatenates all pages belonging to the same candidate. The candidate manifesto is therefore the unit of analysis.

The corpus covers five departments:

```text
Bas-Rhin
Haut-Rhin
Rhône
Pyrénées-Orientales
Hautes-Pyrénées
```

This geographic restriction is important when interpreting the spatial results.

---

## Environment setup

The notebook was written in Python and uses spaCy for Named Entity Recognition.

### 1. Create a virtual environment

Using `venv`:

```bash
python -m venv .venv
```

Activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

Activate it on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Alternatively, using conda:

```bash
conda create -n archelec-ner python=3.11
conda activate archelec-ner
```

### 2. Install Python packages

Install the packages used by the notebook:

```bash
pip install pandas numpy matplotlib spacy jupyter ipykernel
```

### 3. Install the French spaCy model

The notebook uses spaCy's large French model:

```text
fr_core_news_lg
```

Install it with:

```bash
python -m spacy download fr_core_news_lg
```

### 4. Register the environment as a Jupyter kernel

This step is optional but useful if several Python environments are installed:

```bash
python -m ipykernel install --user --name archelec-ner --display-name "Python (archelec-ner)"
```

---

## How to run the project

### Step 1: Clone or download this repository

```bash
git clone <repository-url>
cd <repository-folder>
```

If the repository was downloaded as a zip file, unzip it and move into the project folder.

### Step 2: Add the Archelec text files

Create the expected folders:

```bash
mkdir -p data/arkindex_archelec/text_files
mkdir -p data/metadata
```

Then place the yearly `legislatives.zip` files as follows:

```text
data/arkindex_archelec/text_files/1973/legislatives.zip
data/arkindex_archelec/text_files/1978/legislatives.zip
data/arkindex_archelec/text_files/1981/legislatives.zip
data/arkindex_archelec/text_files/1988/legislatives.zip
data/arkindex_archelec/text_files/1993/legislatives.zip
```

### Step 3: Add the metadata file

Place the metadata CSV here:

```text
data/metadata/archelect_search.csv
```

### Step 4: Start Jupyter

```bash
jupyter notebook
```

Open:

```text
archelec_ner_analysis.ipynb
```

Select the Python environment in which the dependencies and `fr_core_news_lg` were installed.

### Step 5: Run the notebook

Run all cells from top to bottom.

The NER cell prints:

```text
Running NER on all candidates... (this may take a few minutes)
```

This step processes all candidate manifestos with `fr_core_news_lg` using `nlp.pipe()`.

---

## Notebook workflow

The notebook follows the pipeline below.

### 1. Loading and parsing the corpus

The notebook opens each yearly `legislatives.zip` archive, parses the filenames, keeps only `PF` documents and groups pages by candidate.

The final loaded corpus contains:

```text
2,551 candidates
```

The distribution by election year is:

| Year | Candidates | Mean words |
|---:|---:|---:|
| 1973 | 473 | 5,821 |
| 1978 | 478 | 5,934 |
| 1981 | 487 | 5,612 |
| 1988 | 562 | 6,043 |
| 1993 | 551 | 5,967 |
| Total | 2,551 | 5,876 |

### 2. Exploratory data analysis

Before running NER, the notebook checks the number of candidates per election year and the length of the manifestos. This is used to identify potential data issues and to contextualise the later results.

### 3. Text cleaning

The notebook removes repeated OCR watermark text such as:

```text
Sciences Po / fonds CEVIPOF
```

It also normalises whitespace. The text is not fully lowercased, because capitalisation helps spaCy detect proper names.

### 4. Named Entity Recognition

The notebook extracts two entity types with spaCy:

```text
PER
LOC
```

`PER` is used for person names.  
`LOC` is used for geographic locations.

The other spaCy entity types, such as `ORG` and `MISC`, are not used because they generate too much noise on this OCR corpus.

The raw extraction gives:

```text
Average persons per manifesto: 84.3
Average locations per manifesto: 93.0
```

### 5. Metadata and party classification

The candidate metadata are merged with the corpus using candidate identifiers extracted from the filenames.

The original party field is:

```text
titulaire-soutien
```

The raw party labels are heterogeneous, with more than 100 unique values. The notebook maps them into simplified political families using keyword matching and manual matching for smaller parties.

Party-specific analyses exclude candidates without a party mention. The report therefore treats these results as applying to the subset of candidates for whom a party family could be assigned.

### 6. Name normalisation

The raw NER output is noisy because of OCR errors, inconsistent capitalisation and variant forms of the same political figure.

The notebook applies:

- a filter keeping only person entities with at least one lowercase letter;
- a stoplist removing titles, function names and non-name tokens;
- case normalisation;
- a canonical mapping for major political figures.

This reduces fragmentation in citation counts.

### 7. Context-window alignment analysis

For each mention of selected target figures, the notebook extracts a 10-word window around the mention.

It counts positive lexical markers such as:

```text
soutien
fidèle
favorable
accord
victoire
```

and negative lexical markers such as:

```text
contre
opposition
danger
échec
dénonçons
```

The alignment score is:

```text
s = (n_positive - n_negative) / (n_positive + n_negative)
```

A score of `+1` indicates a positive lexical context.  
A score of `-1` indicates a negative lexical context.

Only citations with at least one marker in the context window are included. The score is an exploratory lexical proxy, not a definitive measure of political endorsement.

### 8. Spatial classification

The spatial analysis uses the extracted `LOC` entities and classifies selected geographic references into three categories:

```text
local
national
international
```

The classification relies on a curated list of unambiguous references, including the five departments covered by the corpus, their main cities and a set of foreign countries and supranational entities.

---

## Main results

### Political figures cited by candidates

Mitterrand dominates the citation counts, with 6,149 total mentions. He is followed by Laguiller, De Gaulle and Rocard.

The most cited figures are:

| Rank | Figure | Citations |
|---:|---|---:|
| 1 | François Mitterrand | 6,149 |
| 2 | Arlette Laguiller | 1,480 |
| 3 | Charles De Gaulle | 1,403 |
| 4 | Michel Rocard | 1,278 |
| 5 | Valéry Giscard d'Estaing | 1,065 |
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

The report emphasises that high citation counts should not be interpreted directly as endorsement. A national figure can be cited because candidates support them, oppose them or use them as a central reference point in political debate.

### Citation patterns over time

Mitterrand peaks in 1981 and again in 1988, following his presidential victories, then falls sharply in 1993.

De Gaulle is still frequently cited in 1973 and 1978, even though he died in 1970.

Le Pen appears progressively from 1988, in line with the rise of the Front National.

### Citation patterns by party

Mitterrand is cited across the political spectrum, including by RPR/Gaulliste and FN candidates. The report interprets this as likely reflecting opposition rhetoric as well as endorsement.

De Gaulle remains a reference across several parties, including PS/Gauche, because of his status as a founding figure of the Fifth Republic.

The PCF refers to Marchais less often than it does to Mitterrand.

### Alignment and distancing

The context-window analysis produces the following alignment scores:

| Party | Mitterrand | De Gaulle | Marchais | Chirac | Le Pen |
|---|---:|---:|---:|---:|---:|
| Extreme gauche | 0.45 | -1.00 | -0.46 | 0.44 | 0.00 |
| PCF | 0.69 | 0.29 | -0.52 | 0.33 | 0.60 |
| PS/Gauche | 0.51 | 0.20 | -0.50 | 0.56 | 0.00 |
| UDF/Centre-droit | 0.45 | 0.33 | -1.00 | 0.57 | 0.00 |
| RPR/Gaulliste | 0.63 | 0.27 | -1.00 | 0.43 | -0.33 |
| FN | 0.60 | 0.00 | 0.00 | -1.00 | 0.00 |

The report notes that several patterns are historically coherent, including the negative score of the Extreme gauche toward De Gaulle, the negative score of the RPR toward Le Pen and the generally positive scores for Mitterrand.

The report also stresses that some results should be interpreted with caution. The positive PCF score on Le Pen is historically implausible and comes from administrative mentions rather than rhetorical endorsement.

### Geographic scope

The spatial analysis shows that national references dominate classified geographic mentions.

The report gives the following proportions:

```text
National references: 65%
Local references: 8%
International references: 25%
```

International references rise after 1988, which the report links to the increased salience of the Maastricht debate.

The most frequent international references are:

```text
Europe: 3,295 mentions
Algeria: 425 mentions
Germany: 320 mentions
```

The report also notes that the FN has a high share of international references, presumably reflecting anti-European and anti-immigrant rhetoric.

At department level, the Haut-Rhin has a higher share of local references than the other departments, which is interpreted as reflecting a stronger regional identity.

### Link between sponsorship and spatial discourse

The report computes Pearson correlations between citation counts and the national share of geographic references.

The correlations are:

| Figure | Correlation with national geographic share |
|---|---:|
| Mitterrand | 0.028 |
| De Gaulle | 0.023 |
| Le Pen | -0.082 |

These weak correlations suggest that sponsorship and spatial discourse are largely independent rhetorical dimensions in this corpus.

---

## Limitations

The report highlights several limitations.

First, the corpus is OCR-transcribed. OCR noise creates recognition errors, inconsistent capitalisation and layout artefacts.

Second, the NER model is a pretrained spaCy model applied directly to historical political manifestos. The raw output is noisy and requires post-processing.

Third, the context-window alignment score is lexicon-based. It cannot reliably handle negation, irony or administrative citations.

Fourth, the geographic analysis is limited by the structure of the corpus. The corpus only covers five departments, including peripheral regions such as Alsace and the Pyrenees. The results should therefore not be interpreted as representative of all French legislative campaigns.

---

## Troubleshooting

### `OSError: [E050] Can't find model 'fr_core_news_lg'`

Install the spaCy model:

```bash
python -m spacy download fr_core_news_lg
```

Then restart the Jupyter kernel and run the notebook again.

### `Missing: data/arkindex_archelec/text_files/<year>/legislatives.zip`

The yearly corpus zip files are not in the expected location.

Check that the project contains:

```text
data/arkindex_archelec/text_files/1973/legislatives.zip
data/arkindex_archelec/text_files/1978/legislatives.zip
data/arkindex_archelec/text_files/1981/legislatives.zip
data/arkindex_archelec/text_files/1988/legislatives.zip
data/arkindex_archelec/text_files/1993/legislatives.zip
```

### `FileNotFoundError: data/metadata/archelect_search.csv`

The metadata file is missing.

Download the metadata CSV from the Archelec explorer and place it at:

```text
data/metadata/archelect_search.csv
```

### The NER cell is slow

This is expected. The notebook processes 2,551 candidate manifestos with the large French spaCy model. The cell may take several minutes depending on the machine.

---

## License

This repository is distributed under the MIT License. See:

```text
LICENSE
```
