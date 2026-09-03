# GitHub Copilot Instructions: BBC News IR Assignment, Parts A and B

## 1. Purpose

This repository supports an Information Retrieval assignment using the **raw BBC News dataset** containing 2,225 documents across five categories: business, entertainment, politics, sport, and tech.

The student's assigned contribution is limited to:

- **Part A: Text Processing**
- **Part B: Vocabulary and Indexing**

---

## 2. Scope ownership

Only create notebook content for Parts A and B.

### In scope

#### A. Text Processing

- Corpus loading
- Tokenization
- Case normalization
- Stop-word removal
- Porter stemming
- Lemmatization
- Comparison of the processing stages
- Representative Porter stemming examples

#### B. Vocabulary and Indexing

- Term dictionary or vocabulary
- Document-frequency information
- Inverted index
- Sorted postings lists
- Vocabulary and index statistics before and after preprocessing

### Out of scope

Do not implement or add placeholder solutions for:

- Boolean query parsing or execution
- AND, OR, NOT, or parentheses handling
- Query optimization
- Wildcard, edit-distance, or k-gram retrieval
- Relevance judgments
- Precision, recall, or F1 evaluation

The rest of the team will integrate Parts C, D, and E later. Keep interfaces clean so that the produced vocabulary and inverted index can be reused by them.

---

## 3. Expected repository structure

Create or preserve this structure:

```text
project-root/
├── .github/
│   └── copilot-instructions.md
├── data/
│   ├── raw/
│   │   └── bbc/
│   │       ├── business/
│   │       ├── entertainment/
│   │       ├── politics/
│   │       ├── sport/
│   │       ├── tech/
│   │       └── README.TXT
│   └── preprocessed-reference/
│       └── README.md
├── notebooks/
│   └── 01_text_processing_and_indexing.ipynb
├── outputs/
│   ├── figures/
│   ├── tables/
│   └── indexes/
├── requirements.txt
├── .gitignore
└── README.md
```

Rules:

1. The raw BBC text files are the authoritative input.
2. The downloaded preprocessed dataset may be stored only as a reference for later cross-checking.
3. Never use the preprocessed files to calculate the assignment's primary results.
4. Do not commit large generated outputs or the virtual environment.
5. Use repository-relative paths, not machine-specific absolute paths.

---

## 4. Environment setup

Assume Python 3.10 or newer. Prefer Python 3.11.

### Windows PowerShell

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip --trusted-host pypi.org --trusted-host files.pythonhosted.org
python -m pip install -r requirements.txt --trusted-host pypi.org --trusted-host files.pythonhosted.org
python -m ipykernel install --user --name bbc-ir-ab --display-name "Python (BBC IR A-B)"
```

If script execution is blocked in the current PowerShell session:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

### VS Code steps

1. Open the repository root in VS Code.
2. Install the Microsoft Python and Jupyter extensions if they are not already installed.
3. Run the appropriate virtual-environment commands above.
4. Open `notebooks/01_text_processing_and_indexing.ipynb`.
5. Select the kernel named `Python (BBC IR A-B)`.
6. Run the environment-verification cells before accessing the corpus.

---

## 5. Required `requirements.txt`

Create a minimal, pinned or bounded dependency file. Avoid unnecessary packages.

```text
jupyter>=1.0,<2.0
ipykernel>=6.29,<7.0
nltk>=3.9,<4.0
pandas>=2.2,<3.0
matplotlib>=3.8,<4.0
```

Guidance:

- Use Python standard-library modules wherever practical: `pathlib`, `re`, `collections`, `json`, `pickle`, `time`, `sys`, `platform`, and `statistics`.
- NLTK may provide the stop-word list, `PorterStemmer`, and `WordNetLemmatizer`.
- The student must implement corpus traversal, processing-stage orchestration, vocabulary construction, document frequency, inverted index, and sorted postings logic.
- Pandas is for display and export, not for hiding the core indexing logic.
- Matplotlib is optional and only for student-generated plots based on real outputs.

### NLTK resources

Create a setup cell that checks for required local NLTK resources and gives a clear message if they are absent. Likely resources are:

- `stopwords`
- `wordnet`
- `omw-1.4`

If downloads are permitted, place any download command in a clearly labeled setup-only cell. If the Virtual Lab blocks downloads, instruct the student to prepare the resources in advance or use a documented, instructor-approved local alternative. Never silently substitute a different stop-word list.

---

## 6. `.gitignore`

Create the following entries:

```gitignore
.venv/
__pycache__/
*.py[cod]
.ipynb_checkpoints/
.vscode/
.DS_Store
outputs/indexes/*.json
outputs/indexes/*.pkl
outputs/tables/*.csv
outputs/figures/*.png
```

Do not ignore the notebook, requirements file, README, or Copilot instructions.

---

## 7. Notebook creation requirements

Create `notebooks/01_text_processing_and_indexing.ipynb` with Markdown and code cells in the order below. The notebook must run from top to bottom after the student completes the TODO implementations.

Each assessed code cell must contain:

- A descriptive function signature
- A docstring specifying inputs, outputs, and assumptions
- A `TODO(student)` marker
- Relevant assertions or test expectations outside the implementation
- `raise NotImplementedError("Student implementation required")`

Do not fill these TODOs automatically.

### Notebook section 0: Title and ownership

Include:

- Assignment title
- Dataset: BBC News raw text dataset
- Scope: Parts A and B only
- Student name placeholder
- Group identifier placeholder
- Date placeholder
- Academic-integrity note
- A statement that Parts C, D, and E belong to other team members

### Notebook section 1: Reproducibility and imports

Create cells that:

- Print Python version and platform.
- Print package versions.
- Define a fixed random seed even if sampling is not initially used.
- Resolve the repository root robustly from either the project root or `notebooks/`.
- Create output directories if missing.
- Avoid changing the global working directory without explanation.

Imports should remain explicit and minimal.

### Notebook section 2: Configuration

Create a configuration cell with constants like:

```python
DATASET_ROOT = REPO_ROOT / "data" / "raw" / "bbc"
EXPECTED_CATEGORIES = (
    "business",
    "entertainment",
    "politics",
    "sport",
    "tech",
)
EXPECTED_DOCUMENT_COUNT = 2225
ENCODING_CANDIDATES = ("utf-8", "latin-1")
```

The expected count is a validation target, not a replacement for counting the actual files.

### Notebook section 3: Dataset validation and loading

Copilot may scaffold utilities for:

- Confirming that the dataset root exists
- Confirming expected category directories exist
- Finding `.txt` files deterministically
- Sorting category names and paths
- Detecting duplicate document IDs
- Reading a text file with documented encoding fallback
- Reporting unreadable or empty files

Define a stable document record schema:

```python
{
    "doc_id": "business/001",
    "category": "business",
    "path": "relative/path/to/file.txt",
    "text": "full raw article text"
}
```

Requirements:

- Document IDs must be deterministic and unique.
- Preserve the full raw text.
- Do not use category labels as tokens.
- Do not include `README.TXT` as a document.
- Sort documents by `doc_id` before processing.
- Display actual document count, category counts, empty-file count, and unreadable-file count.
- Add an assertion or warning if the count is not 2,225.

Copilot may implement generic file checking and loading helpers because they are setup utilities, but it must not implement assessed text-processing or indexing logic.

### Notebook section 4: Raw corpus baseline

Before preprocessing, require the student to define clearly what a raw token means for the baseline.

Create Markdown prompts requiring documentation of:

- Whether titles and article bodies are both included
- Token boundary rule
- Treatment of punctuation
- Treatment of numbers
- Treatment of apostrophes and hyphens
- Treatment of acronyms
- Whether one-character tokens are retained

Add placeholders to calculate from actual data:

- Number of documents
- Corpus size in bytes and MiB
- Total raw tokens
- Raw unique terms
- Minimum, maximum, mean, and median document length
- Category-level document counts

Do not generate values.

### Notebook section 5: Part A, tokenization

Create an assessed function scaffold similar to:

```python
def tokenize(text: str) -> list[str]:
    """Return tokens from one raw document according to the documented policy."""
    # TODO(student): implement and explain the token boundary policy.
    raise NotImplementedError("Student implementation required")
```

Required student tests should cover at least:

- Empty string
- Whitespace-only text
- Mixed uppercase and lowercase
- Punctuation
- Apostrophes
- Hyphenated terms
- Integers and decimals
- Acronyms
- A realistic BBC headline or a short student-created equivalent

The notebook must display representative input and output examples generated by the student's code.

### Notebook section 6: Case normalization

Create an assessed scaffold:

```python
def normalize_case(tokens: list[str]) -> list[str]:
    """Normalize token case without changing token ordering."""
    # TODO(student)
    raise NotImplementedError("Student implementation required")
```

Require comparison of unique-term counts before and after normalization. Include tests for repeated words with different casing and acronyms. Ask the student to discuss the advantage and information loss associated with lowercasing.

### Notebook section 7: Stop-word removal

Create an assessed scaffold:

```python
def remove_stopwords(
    tokens: list[str],
    stop_words: set[str],
) -> list[str]:
    """Remove exact normalized-token matches found in stop_words."""
    # TODO(student)
    raise NotImplementedError("Student implementation required")
```

Requirements:

- Record the stop-word source and library version.
- Apply case normalization before stop-word removal.
- Do not mutate the input list.
- Preserve the order of retained tokens.
- Report how many token occurrences and unique terms are removed.
- Display several common removed terms using actual computed frequencies.
- Discuss why stop-word removal may improve index size but can affect phrase or Boolean semantics later.

### Notebook section 8: Porter stemming

Use NLTK's `PorterStemmer` as the library-provided stemmer. The student must study and explain its principles in their own words.

Create an assessed pipeline scaffold that applies a provided stemmer to tokens, but do not complete it for the student.

The notebook should prompt the student to discuss:

- Suffix-stripping stages
- Conditions based on word structure
- Conflation of related surface forms
- Over-stemming
- Under-stemming
- Why a stem need not be a valid dictionary word

Require a reproducible table generated from the actual library output with columns:

- Original term
- Porter stem
- Observation

Suggested terms for the student to test include:

```text
connect, connected, connecting, connection, connections,
study, studies, studied, studying,
policy, policies,
economy, economic, economics,
organization, organizational,
news, business, technology
```

The student must verify all outputs by running the stemmer. Copilot must not invent the resulting stems or observations.

### Notebook section 9: Lemmatization

Use NLTK's `WordNetLemmatizer` as the library-provided lemmatizer.

Create an assessed scaffold that accepts tokens and a clearly documented POS strategy. Do not silently claim POS-aware lemmatization if every word is being treated as a noun.

Require the student to compare at least:

1. Default noun-only lemmatization
2. A documented alternative, if implemented, such as rule-based or POS-tag-assisted mapping

If POS tagging adds packages or NLTK resources, it must be explicitly documented. Keep it optional unless required by the student's design.

Require comparison examples involving plural nouns and verb forms. Ask the student to identify cases where stemming produces a non-word but lemmatization produces a dictionary form.

### Notebook section 10: Processing configurations

Require separate, named processing configurations rather than overwriting one corpus representation.

At minimum preserve:

1. `raw_tokens`
2. `normalized_tokens`
3. `normalized_no_stop_tokens`
4. `stemmed_tokens`
5. `lemmatized_tokens`

Define and document exactly which previous stages feed stemming and lemmatization. A recommended comparison is:

```text
Raw
→ Tokenized
→ Case-normalized
→ Stop words removed
↘ Porter stemmed
↘ Lemmatized
```

Stemming and lemmatization should normally be parallel alternatives for comparison, not sequentially applied to one another.

Avoid storing unnecessary duplicated text if memory becomes an issue. It is acceptable to process one document at a time and store token lists needed for reproducibility.

### Notebook section 11: Shared statistics function

Create an assessed or student-reviewed scaffold for computing statistics from a mapping of `doc_id -> tokens`.

Its documented output should include:

- Number of documents
- Total token occurrences
- Number of unique terms
- Minimum document length
- Maximum document length
- Mean document length
- Median document length
- Number of empty documents after processing

The function must compute values from the supplied representation and must not read global variables implicitly.

Require a comparison DataFrame with one row per processing configuration. All displayed values must be calculated by running the notebook.

### Notebook section 12: Part B, vocabulary and term dictionary

Create an assessed scaffold:

```python
def build_vocabulary(
    documents_tokens: dict[str, list[str]],
) -> dict[str, int]:
    """Map each unique term to a deterministic integer term ID."""
    # TODO(student): define and implement deterministic ordering.
    raise NotImplementedError("Student implementation required")
```

Requirements:

- Vocabulary terms must be unique.
- Term IDs must be deterministic.
- Prefer lexicographically sorted terms before assigning IDs.
- Document the starting term ID, such as zero or one.
- Add assertions checking uniqueness and contiguous IDs.
- Display only a small sample, not the entire vocabulary.

### Notebook section 13: Document frequency

Create an assessed scaffold:

```python
def compute_document_frequency(
    documents_tokens: dict[str, list[str]],
) -> dict[str, int]:
    """Return the number of distinct documents containing each term."""
    # TODO(student): count each term at most once per document.
    raise NotImplementedError("Student implementation required")
```

Required invariants:

- `0 < df(term) <= N` for every indexed term.
- Repeated occurrences in one document increase term frequency but not document frequency.
- The document-frequency key set must match the vocabulary key set.

Include a tiny synthetic example for which the student manually calculates expected DFs before running assertions. Copilot may create the test data but must leave the expected reasoning and assessed function implementation to the student.

### Notebook section 14: Inverted index and sorted postings

Create an assessed scaffold:

```python
def build_inverted_index(
    documents_tokens: dict[str, list[str]],
) -> dict[str, list[str]]:
    """Map each term to a duplicate-free, deterministically sorted postings list."""
    # TODO(student)
    raise NotImplementedError("Student implementation required")
```

Minimum representation:

```text
term -> [doc_id_1, doc_id_2, ...]
```

Requirements:

- Each posting identifies a document containing the term.
- A document appears at most once in one term's postings list.
- Every postings list is sorted deterministically.
- `len(postings[term]) == document_frequency[term]`.
- The vocabulary, DF map, and inverted-index term sets must match.
- Do not store category names as artificial tokens.
- Do not implement query operations in this notebook.

If the group later needs within-document term frequency, offer it as a separately documented optional structure:

```text
term -> [(doc_id, term_frequency), ...]
```

Do not change the agreed interface without informing teammates.

### Notebook section 15: Validation tests

Require validation on a tiny student-created corpus before indexing the BBC corpus.

Tests must check:

- Deterministic output across repeated runs
- No duplicate term IDs
- No duplicate document IDs within postings
- Sorted postings lists
- DF equals postings length
- All vocabulary terms have postings
- No postings refer to unknown documents
- Empty text does not crash the pipeline
- An empty post-processing document is represented consistently
- Results do not depend on directory enumeration order

Use plain `assert` statements or small transparent test helpers. Do not introduce a large testing framework unless needed.

### Notebook section 16: Before-and-after comparison

Produce real, computed comparison tables for:

- Raw/tokenized
- Case-normalized
- Stop words removed
- Porter stemmed
- Lemmatized

Required columns:

```text
configuration
number_of_documents
total_tokens
unique_terms
minimum_document_length
maximum_document_length
mean_document_length
median_document_length
empty_documents
vocabulary_size
number_of_postings
average_postings_length
maximum_postings_length
estimated_index_size_bytes
```

Define metrics precisely:

- `number_of_postings` is the sum of all postings-list lengths.
- `average_postings_length` is number of postings divided by vocabulary size.
- `estimated_index_size_bytes` must use one clearly stated and reproducible method.
- Python object-memory estimates are environment-dependent and must be labelled as estimates.
- Serialized JSON or pickle file size may be used if the serialization method is held constant across configurations.

Never hard-code expected trends. Let the actual corpus output determine whether a metric rises, falls, or stays unchanged.

### Notebook section 17: Required samples for the report

Add cells that export, from actual computed results:

- Dataset summary table
- Preprocessing comparison table
- Vocabulary/index comparison table
- Representative Porter stemming table
- Representative stemming-versus-lemmatization table
- A small sample of vocabulary entries
- A small sample of `(term, df, postings)` entries
- Environment/package versions

Export tables to `outputs/tables/` as CSV. Export indexes only if needed for team integration.

Do not generate conclusions or report paragraphs. Add Markdown questions that the student answers in their own words after inspecting the real outputs.

### Notebook section 18: Team handoff

Export a clear, documented artifact for team members working on Parts C, D, and E.

Preferred JSON structure:

```json
{
  "metadata": {
    "dataset": "BBC News",
    "document_count": 2225,
    "processing_configuration": "normalized_no_stop_tokens",
    "document_id_policy": "category/file_stem",
    "postings_sorted": true
  },
  "vocabulary": {},
  "document_frequency": {},
  "inverted_index": {}
}
```

The actual processing configuration must be decided and documented by the group. Do not assume stemming or lemmatization is the final retrieval representation without team agreement.

Add a loader-validation cell that checks the exported artifact can be read and obeys the same invariants.

## 8. Data and preprocessing decisions that must be documented

Prompt the student to record the following choices in notebook Markdown before executing the full pipeline:

1. Raw dataset folder and source attribution
2. Whether article titles are included
3. File encoding strategy
4. Tokenization regular expression or rule
5. Number-handling rule
6. Apostrophe-handling rule
7. Hyphen-handling rule
8. Case-normalization rule
9. Stop-word source and version
10. Order of preprocessing operations
11. Porter stemmer implementation and library version
12. Lemmatizer implementation, resource version, and POS policy
13. Document-ID construction policy
14. Vocabulary term-ID ordering
15. Postings-list sorting policy
16. Index-size estimation method
17. Any unreadable, empty, excluded, or duplicate files
18. Which representation is handed to the rest of the team

Do not allow undocumented silent preprocessing.

---

## 9. Cross-checking against the downloaded preprocessed BBC files

The preprocessed BBC archive has already undergone Porter stemming, stop-word removal, and filtering of terms with corpus frequency below three. It must not be used as the authoritative input for Parts A and B.

If the student performs an optional cross-check later:

- Put it in a clearly separated appendix section.
- Label it as a non-authoritative reference comparison.
- Do not expect exact equality unless the same tokenizer, stop-word list, Porter implementation, filtering rule, encoding choices, and document interpretation are reproduced.
- Explain mismatches rather than changing the student's raw-data results to match.
- Do not import the provided `.mtx`, `.terms`, `.docs`, or `.classes` files into the main indexing pipeline.
