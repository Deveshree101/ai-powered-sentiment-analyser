# ai-powered-sentiment-analyser
The project aims to understand the sentiments of customers when they buy a product online. Star ratings don’t always show the real experience of customers. Using the AI-powered sentiment analyser, one can identify hidden patterns and detect mismatches between ratings and sentiment. This can help in understanding customer experience more accurately.

A Python-based sentiment analysis pipeline that processes text data using **VADER (Valence Aware Dictionary and sEntiment Reasoner)**, stores results in a **SQLite** database, and visualises insights through interactive **Power BI** dashboards.

---

##  Overview

AI Sentiment Analyser automates the process of extracting emotional tone from text data, whether it's customer reviews, social media posts, survey responses, or any unstructured text. The pipeline scores each piece of text across four sentiment dimensions (positive, negative, neutral, and compound), persists the results in a lightweight SQLite database, and connects to Power BI for real-time visual analytics.

### Why This Stack?

| Component | Role | Why It Was Chosen |
|---|---|---|
| **VADER Sentiment** | NLP engine | Purpose-built for social media and short-form text; no model training required; handles slang, emojis, and emphasis |
| **SQLite** | Data storage | Zero-configuration, serverless, portable, perfect for local and mid-scale analysis |
| **Power BI** | Visualisation | Rich interactive dashboards, DAX measures for custom KPIs, easy sharing with stakeholders |

---

## Architecture
┌──────────────┐     ┌──────────────────┐     ┌──────────────┐     ┌──────────────┐
│  Data Source  │────▶│  Python Pipeline  │────▶│    SQLite     │────▶│   Power BI   │
│ (CSV / API)  │     │  (VADER + ETL)    │     │   Database    │     │  Dashboard   │
└──────────────┘     └──────────────────┘     └──────────────┘     └──────────────┘

1. **Ingest** — Raw text data is loaded from CSV files, APIs, or other sources.
2. **Analyse** — Each text record is scored using VADER's `SentimentIntensityAnalyzer`.
3. **Store** — Scored results (with metadata) are written to a SQLite database.
4. **Visualise** — Power BI connects to the SQLite database via ODBC and renders dashboards.

---

## Features

- **Batch and single-text analysis** — process entire datasets or analyse individual strings on the fly.
- **Compound scoring** — VADER's normalised compound score (−1 to +1) for quick positive/negative classification.
- **Granular breakdown** — separate positive, negative, and neutral proportion scores for deeper analysis.
- **Sentiment labelling** — automatic classification into `Positive`, `Negative`, or `Neutral` based on configurable thresholds.
- **Timestamped records** — every analysis is stored with a timestamp for trend tracking.
- **Power BI dashboards** — pre-built `.pbix` template with sentiment distribution charts, trend lines, word clouds, and KPI cards.
- **Extensible** — plug in new data sources or swap VADER for another NLP model with minimal code changes.

---

## 📂 Project Structure
ai-sentiment-analyser/
├── data/
│   ├── raw/                    # Raw input data (CSV, JSON, etc.)
│   └── processed/              # Cleaned data ready for analysis
├── db/
│   └── sentiment.db            # SQLite database (auto-generated)
├── dashboards/
│   └── sentiment_dashboard.pbix  # Power BI dashboard template
├── src/
│   ├── __init__.py
│   ├── analyser.py             # Core VADER sentiment analysis logic
│   ├── database.py             # SQLite connection and CRUD operations
│   ├── pipeline.py             # End-to-end ETL orchestration
│   └── utils.py                # Helper functions (text cleaning, etc.)
├── tests/
│   ├── test_analyser.py
│   └── test_database.py
├── config.py                   # Configuration and thresholds
├── main.py                     # Entry point
├── requirements.txt
├── .gitignore
├── LICENSE
└── README.md

---

## Getting Started

### Prerequisites

- Python 3.8 or higher
- Power BI Desktop (Windows) or Power BI Service for dashboard viewing
- SQLite ODBC Driver (for Power BI connection)

### Installation

1. **Clone the repository**
```bash
   git clone https://github.com/yourusername/ai-sentiment-analyser.git
   cd ai-sentiment-analyser
```

2. **Create a virtual environment**
```bash
   python -m venv venv
   source venv/bin/activate        # Linux / macOS
   venv\Scripts\activate           # Windows
```

3. **Install dependencies**
```bash
   pip install -r requirements.txt
```

4. **Verify installation**
```bash
   python -c "from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer; print('VADER ready')"
```

---

## ⚙️ Configuration

Edit `config.py` to customise thresholds and database settings:
```python
# Sentiment classification thresholds (compound score)
POSITIVE_THRESHOLD = 0.05
NEGATIVE_THRESHOLD = -0.05

# Database
DB_PATH = "db/sentiment.db"

# Input
DEFAULT_INPUT_PATH = "data/raw/reviews.csv"
TEXT_COLUMN = "review_text"       # Column name containing the text to analyse
```

---

## 📖 Usage

### Analyse a CSV file
```bash
python main.py --input data/raw/reviews.csv --column review_text
```

### Analyse a single string
```bash
python main.py --text "This product is absolutely fantastic! Best purchase ever."
```

**Output:**
### Run the full pipeline (ingest → analyse → store)
```bash
python main.py --pipeline --input data/raw/reviews.csv
```

### Python API
```python
from src.analyser import SentimentAnalyser

analyser = SentimentAnalyser()
result = analyser.analyse("The customer support was unhelpful and rude.")

print(result)
# {
#     "text": "The customer support was unhelpful and rude.",
#     "pos": 0.0,
#     "neu": 0.476,
#     "neg": 0.524,
#     "compound": -0.7003,
#     "label": "Negative"
# }
```

---

## Database Schema

The SQLite database (`sentiment.db`) uses a single primary table:
```sql
CREATE TABLE sentiment_results (
    id            INTEGER PRIMARY KEY AUTOINCREMENT,
    source_file   TEXT,
    text_content  TEXT NOT NULL,
    pos_score     REAL,
    neg_score     REAL,
    neu_score     REAL,
    compound_score REAL,
    label         TEXT CHECK(label IN ('Positive', 'Negative', 'Neutral')),
    analysed_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

You can query results directly:
```bash
sqlite3 db/sentiment.db "SELECT label, COUNT(*) FROM sentiment_results GROUP BY label;"
```

---

## 📊 Power BI Dashboard

The included `.pbix` file provides a ready-to-use dashboard with the following visuals:

- **Sentiment Distribution** — Pie/donut chart showing the proportion of Positive, Negative, and Neutral records.
- **Compound Score Histogram** — Distribution of compound scores across the dataset.
- **Sentiment Over Time** — Line chart tracking sentiment trends by date.
- **KPI Cards** — Average compound score, total records analysed, and positive-to-negative ratio.
- **Top Positive & Negative Texts** — Tables highlighting the highest and lowest scoring entries.

### Connecting Power BI to SQLite

1. Install the [SQLite ODBC Driver](http://www.ch-werner.de/sqliteodbc/).
2. Open `sentiment_dashboard.pbix` in Power BI Desktop.
3. Go to **Home → Transform Data → Data Source Settings**.
4. Update the connection string to point to your local `db/sentiment.db` path.
5. Click **Refresh** to load the latest data.

---

## Testing

Run the test suite with `pytest`:
```bash
pytest tests/ -v
```

Tests cover core analyser output validation, database read/write operations, and threshold-based label assignment.

---

## 📦 Dependencies

| Package | Version | Purpose |
|---|---|---|
| `vaderSentiment` | ≥ 3.3.2 | Sentiment analysis engine |
| `pandas` | ≥ 1.5.0 | Data manipulation and CSV handling |
| `sqlite3` | stdlib | Database operations (built into Python) |
| `pytest` | ≥ 7.0.0 | Testing framework |

Full list in `requirements.txt`.

---

## Roadmap

- [ ] Add support for Twitter/X API as a live data source
- [ ] Implement text preprocessing (stopword removal, lemmatisation)
- [ ] Add multi-language sentiment support
- [ ] Build a Streamlit web interface for non-technical users
- [ ] Add export to CSV/Excel from the database
- [ ] Implement scheduled/automated pipeline runs (cron / Task Scheduler)
- [ ] Add comparison mode (compare sentiment across multiple datasets)

---

## Contributing

Contributions are welcome! To get started:

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -m "Add your feature"`
4. Push to the branch: `git push origin feature/your-feature-name`
5. Open a Pull Request.

Please make sure all tests pass before submitting a PR.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## Acknowledgements

- [VADER Sentiment Analysis](https://github.com/cjhutto/vaderSentiment) by C.J. Hutto — the NLP engine powering this project.
- [SQLite](https://www.sqlite.org/) — the most widely deployed database engine in the world.
- [Power BI](https://powerbi.microsoft.com/) by Microsoft — for turning data into actionable visuals.

---

> Built with  Python ·  Power BI ·  SQLite ·  VADER
