# ai-powered-sentiment-analyser
The project aims to understand the sentiments of customers when they buy a product online. Star ratings don’t always show the real experience of customers. Using the AI-powered sentiment analyser, one can identify hidden patterns and detect mismatches between ratings and sentiment. This can help in understanding customer experience more accurately.

A Python-based sentiment analysis pipeline that processes text data using **VADER (Valence Aware Dictionary and sEntiment Reasoner)**, stores results in a **SQLite** database, and visualises insights through interactive **Power BI** dashboards.

---

## What It Does

Takes text data like reviews, feedback, or social media posts, analyses the sentiment using VADER, stores the scored results in a SQLite database, and visualises the insights through a Power BI dashboard.

## Tech Stack

- **VADER Sentiment** — scores each text as positive, negative, or neutral without any model training
- **SQLite** — stores all the sentiment results in a local database
- **Power BI** — connects to the SQLite database and displays interactive charts and dashboards

## How It Works

1. Text data is fed into VADER
2. VADER returns a compound score from -1 (most negative) to +1 (most positive)
3. The scores and labels are stored in a SQLite database
4. Power BI reads the database and shows trends, distributions, and key metrics

## Database Structure

| Column | Description |
|---|---|
| text_content | the original text |
| pos_score | positive score (0 to 1) |
| neg_score | negative score (0 to 1) |
| neu_score | neutral score (0 to 1) |
| compound_score | overall score (-1 to +1) |
| label | Positive, Negative, or Neutral |
| analysed_at | timestamp |

## Power BI Dashboard

The dashboard includes:

- Sentiment distribution (pie chart)
- Sentiment trends over time (line chart)
- Average compound score (KPI card)
- Top positive and negative texts

### Connecting Power BI to SQLite

1. Install the SQLite ODBC driver from http://www.ch-werner.de/sqliteodbc/
2. Open the `.pbix` file in Power BI Desktop
3. Update the data source path to your `sentiment.db` file
4. Refresh the data

## License

MIT

