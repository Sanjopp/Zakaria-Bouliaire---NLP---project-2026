# Who Speaks to Whom? Sponsorship, Spatial Discourse, and Political Identity in French Electoral Manifestos (1973–1993)

**Author:** Zakaria Bouliaire   
**Course:** Machine Learning for NLP — ENSAE 2026  

## Project overview

This repository contains the code and report for an NLP project on the Archelec corpus, a dataset consisting of digitised French legislative electoral manifestos from several elections between 1973 and 1993.

The project applies Named Entity Recognition to the corpus using spaCy's `fr_core_news_lg` model. It studies political references and geographic references in local candidates' manifestos.

The project addresses two specific questions drawn from the Archelec project call:

1. **Sponsorship:** which politicians are cited by local candidates, and to what extent do they agree with them or reject them?
2. **Spatial discourse:** do candidates focus their manifestos in local level politic or national and global ones?

The full analysis is implemented in:

`archelec_ner_analysis.ipynb`

The written report is available in:

`Zakaria_BOULIAIRE_NLP_2026_Rapport.pdf`

## Repository structure

```text
.
├── archelec_ner_analysis.ipynb
├── Zakaria_BOULIAIRE_NLP_2026_Rapport.pdf
├── README.md
├── LICENSE
└── .gitignore
```

## Data

The project uses the Archelec corpus and candidate metadata.

The corpus contains the `professions de foi` of five French legislative elections:

```text
1973
1978
1981
1988
1993
```

The unit of analysis is the candidate manifesto, not the individual page. A single candidate can have many pages. Before the analysis, all pages belonging to the same candidate are grouped and concatenated.

Only the `profession de foi` files are kept. The `bulletins de vote` are discarded because they are too short to carry meaningful discourse.

The corpus spans five departments:

```text
Bas-Rhin
Haut-Rhin
Rhone
Pyrenees-Orientales
Hautes-Pyrenees
```

The geographic scope is limited and should be considered when interpreting spatial findings. The corpus should not be interpreted as representative of all French legislative campaigns.

### Filename structure

The filename encodes document metadata. Example:

```text
EL067_L_1973_03_059_10_1_PF_04.txt
```

This filename means:

```text
EL067       department code, here Bas-Rhin
L           legislative election
1973_03     March 1973
059         constituency number
10          candidate number within the constituency
1           first round
PF          profession de foi, as opposed to BV, bulletin de vote
04          page number
```

## Required data layout

The notebook expects the corpus files to be located in:

```text
data/arkindex_archelec/text_files/
```

The required structure is:

```text
data/
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

The metadata CSV is loaded from:

```text
data/metadata/archelect_search.csv
```

## Data sources

Two data sources are required.

### Corpus transcriptions

The corpus transcriptions should be obtained from:

```text
https://gitlab.teklia.com/ckermorvant/arkindex_archelec
```

The repository should be cloned or downloaded so that the `legislatives.zip` files are placed under:

```text
data/arkindex_archelec/text_files/
```

### Candidate metadata

The candidate metadata should be downloaded from:

```text
https://archelec.sciencespo.fr/explorer
```

The file must be placed at:

```text
data/metadata/archelect_search.csv
```

The metadata file is used to merge candidate-level information with the extracted corpus. In particular, the notebook uses the party field:

```text
titulaire-soutien
```

## Installation

### 1. Clone the repository

```bash
git clone <repository-url>
cd <repository-name>
```

### 2. Create a Python environment

Using `venv`:

```bash
python -m venv .venv
source .venv/bin/activate
```

On Windows PowerShell:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

Using `conda`:

```bash
conda create -n archelec-nlp python=3.13
conda activate archelec-nlp
```

The notebook was run with Python 3.13. It should also run with a recent Python 3 version if the dependencies below are installed.

### 3. Install Python packages

Install the packages used by the notebook:

```bash
pip install spacy pandas numpy matplotlib jupyter
```

The original repository README also lists:

```bash
pip install spacy pandas matplotlib seaborn scikit-learn
```

The notebook imports the following main packages:

```text
pandas
numpy
matplotlib
spacy
```

It also imports the following standard-library modules:

```text
os
zipfile
re
json
urllib.request
pathlib
collections
```

### 4. Install the French spaCy model

The notebook uses:

```text
fr_core_news_lg
```

Install it with:

```bash
python -m spacy download fr_core_news_lg
```

## How to run the project

### Step 1: prepare folders

Create the required folders:

```bash
mkdir -p data/arkindex_archelec/text_files
mkdir -p data/metadata
```

On Windows PowerShell:

```powershell
New-Item -ItemType Directory -Force data\arkindex_archelec\text_files
New-Item -ItemType Directory -Force data\metadata
```

### Step 2: add the corpus files

Place the five `legislatives.zip` files exactly as follows:

```text
data/arkindex_archelec/text_files/1973/legislatives.zip
data/arkindex_archelec/text_files/1978/legislatives.zip
data/arkindex_archelec/text_files/1981/legislatives.zip
data/arkindex_archelec/text_files/1988/legislatives.zip
data/arkindex_archelec/text_files/1993/legislatives.zip
```

### Step 3: add the metadata file

Place the metadata CSV exactly as follows:

```text
data/metadata/archelect_search.csv
```

### Step 4: launch Jupyter

```bash
jupyter notebook
```

Open:

```text
archelec_ner_analysis.ipynb
```

### Step 5: run the notebook

Run all cells in order.

The NER step processes all candidates and may take several minutes. The notebook prints:

```text
Running NER on all candidates... (this may take a few minutes)
```

The original README indicates that the NER step takes approximately 20 to 30 minutes on a standard CPU.

## Notebook pipeline

The notebook follows this structure:

```text
1. Data
2. Exploratory statistics
3. Cleaning
4. Named Entity Recognition with spaCy
5. Metadata and party classification
6. Analysis — Part 1: political personalities and sponsorship
7. Context-window proxy for agreement or rejection
8. Analysis — Part 2: spatial discourse
9. Relation between sponsorship and spatial discourse
10. Conclusion
```

## Methodology

### Corpus loading

The corpus is organised by election year. Each year is stored as a zip archive containing one text file per page of each manifesto.

The notebook loads the five election years:

```python
YEARS = [1973, 1978, 1981, 1988, 1993]
```

The data directory is defined as:

```python
DATA_DIR = Path("data/arkindex_archelec/text_files")
```

The metadata file is defined as:

```python
METADATA_PATH = Path("data/metadata/archelect_search.csv")
```

The notebook parses each filename, keeps only `PF` files, groups pages by candidate, and concatenates the text.

### Exploratory statistics

After parsing and grouping, the notebook loads:

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

| Statistic | Words |
|---|---:|
| Count | 2,551.0 |
| Mean | 5,875.6 |
| Standard deviation | 2,678.4 |
| Minimum | 643.0 |
| 25% | 3,772.0 |
| Median | 5,446.0 |
| 75% | 7,529.0 |
| Maximum | 17,078.0 |

### Cleaning

The preprocessing is deliberately light.

The notebook removes repeated OCR watermark text such as:

```text
Sciences Po / fonds CEVIPOF
```

It also normalises whitespace.

The notebook does not lowercase the full text because spaCy relies on capitalisation to detect proper nouns.

### Named Entity Recognition

The project uses:

```text
spaCy fr_core_news_lg
```

The notebook extracts two entity types:

```text
PER
LOC
```

`PER` is used for person names.

`LOC` is used for geographic locations.

The notebook does not use `ORG` and `MISC`, because they generated too much noise on the OCR corpus.

The NER extraction gives:

```text
Average persons per manifesto: 84.3
Average locations per manifesto: 93.0
```

### Metadata and party classification

The notebook merges candidate metadata with the corpus using the candidate identifier.

The raw party field is:

```text
titulaire-soutien
```

The raw party names are heterogeneous, with more than 100 distinct values. The notebook maps them into simplified political families.

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

### Political personalities and sponsorship

The first analysis studies which national political figures are cited by candidates.

The raw person extraction contains OCR artefacts and non-name entities. The notebook filters and normalises these mentions.

The notebook:

```text
keeps entities with at least one lowercase letter
removes titles, function names and other non-name tokens
normalises case
maps known name variants to canonical forms
```

The analysis then studies:

```text
who gets cited
when they are cited
who cites whom
whether citation contexts suggest alignment or distancing
```

### Context-window proxy for agreement or rejection

For each citation of a target figure, the notebook extracts a window of 10 words before and after the mention.

It then searches for positive and negative lexical markers.

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

A score of `+1` means a positive lexical context.

A score of `-1` means a negative lexical context.

Only citations with at least one marker in the context window are included.

This is used as a proxy for agreement or rejection. The notebook and report treat this as exploratory, not as a full sentiment model.

### Spatial discourse

The second analysis studies whether candidates frame their discourse locally, nationally or internationally.

It uses the `LOC` entities extracted by spaCy.

Because the OCR corpus is noisy, the notebook does not attempt to classify every detected location. It focuses on a curated list of unambiguous geographic references:

```text
the five departments covered by the corpus
their main cities
a set of foreign countries and supranational entities
```

The geographic references are classified into:

```text
local
national
international
```

### Relation between sponsorship and spatial discourse

The notebook computes correlations between citation frequency for selected political figures and the share of national or international geographic references.

This part tests whether citing national political figures is associated with a more national or international rhetorical framing.

## Results

### Most cited political figures

The top 20 cited political figures are:

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

Mitterrand dominates citation counts across the political spectrum.

His citations peak in 1981 and 1988, following his presidential victories.

De Gaulle still generates substantial citations in 1973 and 1978, even though he died in 1970.

Le Pen appears progressively from 1988 onward, marking the rise of the Front National.

### Citation patterns by party

The notebook finds that Mitterrand is cited across the political spectrum, including by RPR and FN candidates.

The report interprets these cross-party citations as likely reflecting opposition rhetoric rather than endorsement.

De Gaulle remains a reference across parties, including the PS, reflecting his status as a founding figure of the Fifth Republic.

The PCF cites Marchais less often than Mitterrand.

### Agreement or rejection proxy

The alignment scores by party and figure are:

| Party | Charles De Gaulle | François Mitterrand | Georges Marchais | Jacques Chirac | Jean-Marie Le Pen |
|---|---:|---:|---:|---:|---:|
| Extrême gauche | -1.00 | 0.43 | -0.46 | 0.44 | 0.00 |
| PCF | 0.29 | 0.69 | -0.60 | 0.33 | 0.60 |
| PS/Gauche | 0.20 | 0.51 | -0.50 | 0.56 | 0.00 |
| UDF/Centre-droit | 0.33 | 0.45 | -1.00 | 0.57 | 0.00 |
| RPR/Gaulliste | 0.27 | 0.63 | -1.00 | 0.43 | -0.33 |
| FN | 0.00 | 0.60 | 0.00 | -1.00 | 0.00 |

The notebook notes several historically coherent patterns:

```text
Mitterrand is cited positively by PCF and PS/Gauche.
De Gaulle is cited negatively by the Extrême gauche and positively by the RPR and UDF.
Marchais is cited negatively across the board, including by the PCF itself.
The RPR cites Le Pen negatively.
```

The notebook also notes that some results are less reliable. The PCF scores positively on Le Pen, which is historically implausible. Inspection of the context windows around Le Pen mentions shows that most citations are administrative, such as references to candidates presented by the Front National or to a Le Pen list, rather than rhetorical.

### Spatial discourse results

The total geographic mentions across all manifestos are:

| Scope | Mentions |
|---|---:|
| Local | 1,528 |
| National | 11,863 |
| International | 4,563 |

National references dominate throughout the period.

International references increase from 1988 onward, which the report links to the Maastricht debate.

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

### Geographic scope by department

The geographic mention counts by department are:

| Department | Local | National | International |
|---|---:|---:|---:|
| Bas-Rhin | 19 | 311 | 113 |
| Haut-Rhin | 261 | 736 | 200 |
| Hautes-Pyrenees | 14 | 333 | 138 |
| Pyrenees-Orientales | 25 | 407 | 195 |
| Rhone | 13 | 390 | 133 |

The notebook highlights that the Haut-Rhin has a markedly higher share of local references.

It also notes that the two Pyrenees departments show high international proportions.

These results should be interpreted cautiously because the corpus covers only five departments.

### Sponsorship and spatial discourse

The notebook computes correlations between figure citations and geographic scope.

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

## Limitations

The results should be interpreted as exploratory.

The main limitations are:

```text
OCR noise
limited geographic coverage of the corpus
noisy NER output on historical political documents
manual normalisation of political names
lexicon-based context-window proxy for agreement or rejection
curated geographic reference list rather than exhaustive geocoding
```

The report notes that OCR-induced variants are resolved through a canonical name normalisation pipeline, and that the tone of each citation is approximated using a lexicon-based context-window proxy.

The report also notes that the spatial analysis classifies spaCy-detected geographic entities as local, national or international using a curated reference list.

## Conclusion

The project applies Named Entity Recognition to the Archelec corpus to study political sponsorship and spatial discourse in French legislative electoral manifestos between 1973 and 1993.

The analysis shows that Mitterrand dominates citation counts for all parties, peaking in 1981 and 1988 following his presidential victories.

It also shows that national references consistently overwhelm local ones over time.

The two rhetorical dimensions appear to be largely independent of one another.

These results should be interpreted as exploratory, given OCR noise and the limited geographic coverage of the corpus.

## Troubleshooting

### spaCy model not found

If the notebook raises an error saying that `fr_core_news_lg` cannot be found, run:

```bash
python -m spacy download fr_core_news_lg
```

### Corpus files not found

Check that the files are exactly here:

```text
data/arkindex_archelec/text_files/1973/legislatives.zip
data/arkindex_archelec/text_files/1978/legislatives.zip
data/arkindex_archelec/text_files/1981/legislatives.zip
data/arkindex_archelec/text_files/1988/legislatives.zip
data/arkindex_archelec/text_files/1993/legislatives.zip
```

### Metadata file not found

Check that the metadata file is exactly here:

```text
data/metadata/archelect_search.csv
```

### NER step is slow

This is expected. The NER step runs spaCy on 2,551 candidate manifestos and may take several minutes. The original README indicates approximately 20 to 30 minutes on a standard CPU.

## License

This repository is distributed under the MIT License.

See:

```text
LICENSE
```
