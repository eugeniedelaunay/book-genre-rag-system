
# Book Genre RAG System

This project processes a large books dataset, enriches each entry with high‑quality descriptions and genres, and uses the result as the basis for a retrieval‑augmented generation (RAG) pipeline over books.

The goal is to combine classic data preprocessing with API‑based enrichment and LLMs, then experiment with semantic retrieval and generation for book‑related queries and recommendations.

## Features

- Cleaning and preprocessing of raw book metadata (titles, authors, ratings, pages, identifiers)
- Automatic description fetching from the Google Books API using ISBN, title, and author
- LLM‑based description completion when no external description is available
- Genre generation based on book descriptions and metadata
- RAG notebook to query the enriched corpus with semantic search and LLM answers

## Project Structure

- `README.md`  
  Project overview, setup, and usage instructions.

- `data/`  
  - `books.csv` – original books metadata  
  - `cleaned_books.csv` – cleaned subset with selected columns  
  - `FullDataSetGenreAndDescription.csv` – enriched dataset with descriptions and genres

- `src/`  
  - `Preprocessing.py` – data loading, cleaning, and selection of relevant columns  
  - `DescriptionGoogle.py` – fetches descriptions from the Google Books API with ISBN/title/author fallback  
  - `GenerateDescription.py` – uses an LLM to generate descriptions where external APIs fail  
  - `GenerateGenre.py` – predicts or generates a genre label from the description/metadata

- `notebooks/`  
  - `rag-generator.ipynb` – experiments and RAG pipeline over the enriched dataset

You can adapt file and folder names if your local structure differs, but this layout is a good default.

## Requirements

Install dependencies via:

```bash
pip install -r requirements.txt
```

The typical stack includes:

- `pandas` for data manipulation  
- `requests` for HTTP/API calls  
- `openai` (or compatible client) for LLM calls  
- `kaggle` if running inside Kaggle or using Kaggle secrets  
- `numpy`, `tqdm`, and other utilities  
- RAG/ML tooling such as `transformers`, `torch`, `faiss-cpu`, `scikit-learn` (if used in the notebook)

Adjust the exact list and versions to match your environment.

## Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/<your-username>/book-genre-rag-system.git
   cd book-genre-rag-system
   ```

2. **Create and activate a virtual environment** (recommended)

   ```bash
   python -m venv .venv
   source .venv/bin/activate    # on Windows: .venv\Scripts\activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

4. **Configure API keys**

   - Set your OpenAI‑compatible API key (for `GenerateDescription.py`)  
   - Set your Google Books API key if you use one (for `DescriptionGoogle.py`)  
   - Optionally configure secrets via your environment or platform‑specific secrets manager

   Example (bash):

   ```bash
   export OPENAI_API_KEY="your-key-here"
   export GOOGLE_API_KEY="your-key-here"
   ```

5. **Check and adjust data paths**

   Make sure the paths inside the scripts (e.g. to `cleaned_books.csv` and intermediate CSVs) match your folder layout.

## Usage

### 1. Preprocess the data

Clean the raw dataset and create a simplified version:

```bash
python src/Preprocessing.py
```

This step typically:

- Loads `data/books.csv`
- Selects relevant columns (title, author(s), rating, pages, identifiers)
- Outputs `data/cleaned_books.csv` (or similar)

### 2. Fetch descriptions from Google Books

Use ISBN/title/author to retrieve descriptions:

```bash
python src/DescriptionGoogle.py
```

This script:

- Reads the cleaned dataset
- Calls the Google Books API per ISBN (with optional title/author filters)
- Writes a CSV with an additional `description` column, leaving `"No description found"` where nothing was returned

### 3. Complete missing descriptions with an LLM

Generate descriptions where the API failed:

```bash
python src/GenerateDescription.py
```

This script:

- Reads the partially enriched CSV
- For rows with `"No description found"`, builds a prompt from title/author/ISBN
- Calls the LLM to generate a concise, complete description
- Writes a new CSV where every row has a usable `description` field

### 4. Generate genres

Infer or generate a genre label:

```bash
python src/GenerateGenre.py
```

Depending on your implementation, this can:

- Use rule‑based heuristics over descriptions
- Use a trained classifier
- Or use an LLM to assign a genre

The output is typically a dataset like `FullDataSetGenreAndDescription.csv` with both `description` and `genre`.

### 5. Explore the RAG pipeline

Launch Jupyter and open the notebook:

```bash
jupyter notebook notebooks/rag-generator.ipynb
```

In the notebook, you can:

- Load the enriched dataset
- Build vector embeddings for descriptions and/or titles
- Index them in a vector store
- Implement a simple RAG loop:
  - retrieve top‑k similar books for a query
  - pass them as context to an LLM
  - generate an answer or recommendation

## Contributing

This is primarily a learning/experimentation project, but you can:

- Improve preprocessing and data quality checks
- Experiment with different embedding models and vector databases
- Add evaluation metrics for genre predictions or RAG quality

Feel free to open issues or propose improvements if you fork the repository.

## License

Add your preferred license here (e.g. MIT, Apache‑2.0, or leave this section out until you decide).

```