# Assignment-2

# 🎬 IMDB Movie Data — Exploratory Data Analysis
### A Data Science Advisory Report for a Rookie Movie Producer

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python)
![Jupyter](https://img.shields.io/badge/Notebook-Jupyter%2FColab-orange?logo=jupyter)
![Pandas](https://img.shields.io/badge/pandas-1.5%2B-lightblue?logo=pandas)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![License](https://img.shields.io/badge/License-MIT-green)

> **A professional, full-scale Exploratory Data Analysis project on 3,000 IMDB movies designed to help a rookie film producer make data-backed decisions on what to produce and who to cast.**

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Business Questions Answered](#-business-questions-answered)
- [Dataset Description](#-dataset-description)
- [Tools & Technologies](#-tools--technologies)
- [Methodology](#-methodology)
- [Key Findings at a Glance](#-key-findings-at-a-glance)
- [Visualizations Produced](#-visualizations-produced)
- [Repository Structure](#-repository-structure)
- [How to Run in Google Colab](#-how-to-run-in-google-colab)
- [Producer Recommendations](#-producer-recommendations)
- [Future Work](#-future-work)

---

## 🔍 Project Overview

A first-time movie producer needs help deciding what type of films to invest in and which actors to cast. This project performs a rigorous **Exploratory Data Analysis (EDA)** on a dataset of **3,000 IMDB movies** to surface actionable insights grounded in data.

The analysis spans data sanity checks, financial analysis (profit, ROI, budget-revenue relationships), genre profitability, language performance, actor and director patterns, and cross-feature correlations.

---

## ❓ Business Questions Answered

| # | Question | Key Answer |
|---|---|---|
| **Q1** | Which movie made the highest profit? Director? Producer? Cast? | **Furious 7** — $1.316B profit. Dir: James Wan. Prod: Vin Diesel & Neal H. Moritz |
| **Q2** | Which language has the highest average ROI? | **Korean** has the highest raw avg ROI; **English** has the most reliable ROI at scale |
| **Q3** | Unique genres in the dataset? | **20 genres** — from Action to Western |
| **Q4** | Top 3 producers by average ROI? | **Charlie Chaplin, Marc Bienstock, Jerry Weintraub** |
| **Q5** | Most active actor? Deep dive into their movies, genres, profits? | **Samuel L. Jackson** (30 movies) — Action/Crime dominant, Marvel films drive profit |
| **Q6** | Top 3 directors prefer which actors? | Ron Howard → Clint Howard; Eastwood → himself; Spielberg → Harrison Ford |

---

## 📊 Dataset Description

**File:** `imdb_data.csv`  
**Records:** 3,000 movies  
**Source:** IMDB / TMDB metadata compilation

### Column Reference

| Column | Type | Description |
|---|---|---|
| `title` | str | Movie title |
| `budget` | int | Production budget in USD |
| `revenue` | int | Box office revenue in USD |
| `genres` | str (list-of-dicts) | Genre classification |
| `original_language` | str | ISO language code |
| `cast` | str (list-of-dicts) | Cast members with names, characters, order |
| `crew` | str (list-of-dicts) | Crew with names, departments, job titles |
| `release_date` | str | Release date |
| `runtime` | int | Duration (minutes) |
| `popularity` | float | TMDB popularity score |
| `production_companies` | str | Studios involved |
| `overview` | str | Plot summary |
| `tagline` | str | Marketing tagline |
| `Keywords` | str | Associated keywords |

### Data Quality Notes
- 812 movies have `budget = 0` (treated as missing, excluded from financial analysis)
- `belongs_to_collection` is 80% null (not critical for analysis)
- `cast` and `crew` columns contain nested Python dict strings requiring parsing with `ast.literal_eval()`
- All financial metrics (profit, ROI) are computed only on the **2,188 movies** where both budget and revenue > 0

---

## 🛠 Tools & Technologies

| Tool | Version | Purpose |
|---|---|---|
| Python | 3.10+ | Core language |
| pandas | 1.5+ | Data manipulation |
| NumPy | 1.23+ | Numerical operations |
| Matplotlib | 3.6+ | Base plots |
| Seaborn | 0.12+ | Statistical visualization |
| ast | stdlib | Parsing nested dict strings |
| collections.Counter | stdlib | Actor/director/genre frequency counts |
| Google Colab | — | Cloud notebook environment |

---

## 🔬 Methodology

### Phase 1: Data Loading & Inspection
Load CSV, check shape (3000 × 23), review dtypes, call `.info()` and `.describe()`.

### Phase 2: Data Sanity Check
- Missing value quantification per column
- Zero-budget / zero-revenue identification
- Duplicate detection

### Phase 3: Feature Engineering
- `profit = revenue - budget`
- `roi = (profit / budget) × 100`
- `release_year` extracted from `release_date`
- `actors_list`, `directors_list`, `producers_list`, `genres_list` parsed from nested strings
- Financial subset: `df_valid` (budget > 0 AND revenue > 0 → 2,188 rows)

### Phase 4: Answering Business Questions
Each of the 6 questions addressed with:
- Dedicated data aggregation code
- Supporting visualizations
- Insight commentary

### Phase 5: Extended EDA
- Popularity distribution and correlation with profit
- Budget & profit trends over time (1990–2017)
- Full correlation heatmap
- Runtime distribution by genre
- Director-actor co-occurrence analysis

---

## 💡 Key Findings at a Glance

| Insight | Detail |
|---|---|
| 🏆 Highest profit film | **Furious 7** — $1.316B profit on $190M budget (6.9x ROI) |
| 🌍 Best ROI language | Korean films have extreme avg ROI; English most consistent |
| 🎭 Most common genre | **Drama** (1,531 movies), then Comedy, Thriller, Action |
| 💰 Best ROI genre | **Horror** — low budgets, high multiplier when successful |
| 🎬 Most prolific actor | **Samuel L. Jackson / Robert De Niro** — 30 movies each |
| 🎥 SLJ top genre | **Action** (17 movies), strongest in Marvel franchise films |
| 📽 Top director | **Ron Howard & Clint Eastwood** (11 movies each) |
| 🤝 Spielberg-Ford | Harrison Ford appears in 3 Spielberg films — a classic partnership |
| 📈 Budget-Revenue corr | Strong positive correlation (~0.74) but ROI weakly correlated with budget |
| 🗓 Trend | Budgets and profits both grew significantly post-1995 |

---

## 📊 Visualizations Produced

| # | Plot Title | Type |
|---|---|---|
| 1 | Missing Data by Column | Horizontal Bar Chart |
| 2 | Budget & Revenue Distributions | Dual Histogram |
| 3 | Budget vs Revenue (coloured by profit) | Scatter Plot |
| 4 | Genre Frequency Distribution | Horizontal Bar Chart |
| 5 | Average Profit & ROI by Genre | Dual Bar Chart |
| 6 | Top 10 Most Profitable Movies | Horizontal Bar Chart |
| 7 | Budget, Revenue, Profit — Top 10 | Grouped Bar Chart |
| 8 | Language ROI & Movie Count | Dual Bar Chart |
| 9 | Top 20 Producers by Avg ROI | Horizontal Bar Chart |
| 10 | Top 20 Most Prolific Producers | Horizontal Bar Chart |
| 11 | Top 15 Most Active Actors | Horizontal Bar Chart |
| 12 | Samuel L. Jackson — Genres & Profit | Pie Chart + Bar Chart |
| 13 | Top 15 Actors — Avg Movie Profit | Horizontal Bar Chart |
| 14 | Top 3 Directors' Preferred Actors | 3-Panel Bar Charts |
| 15 | Top 20 Most Prolific Directors | Horizontal Bar Chart |
| 16 | Popularity Distribution & vs Profit | Histogram + Scatter |
| 17 | Avg Budget & Profit Over Time | Dual-Axis Line+Bar |
| 18 | Correlation Heatmap | Seaborn Heatmap |
| 19 | Runtime by Genre | Boxplot |

---

## 📁 Repository Structure

```
IMDB-Movie-EDA/
│
├── 📓 IMDB_Movie_EDA.ipynb         # Main Google Colab notebook
├── 📋 README.md                     # This file
├── 📝 Assignment_Report.md          # Written college submission report
│
├── data/
│   └── imdb_data.csv               # Dataset (upload to Colab)
│
└── plots/                          # Saved chart outputs
    ├── v1_missing_values.png
    ├── v2_budget_revenue_dist.png
    ├── v3_budget_vs_revenue.png
    ├── v4_genre_distribution.png
    ├── v5_genre_profit_roi.png
    ├── v6_top10_profit.png
    ├── v7_top10_grouped.png
    ├── v8_language_roi.png
    ├── v9_top_producers_roi.png
    ├── v10_prolific_producers.png
    ├── v11_top_actors.png
    ├── v12_slj_deep_dive.png
    ├── v13_actors_avg_profit.png
    ├── v14_director_actor_preference.png
    ├── v15_top_directors.png
    ├── v16_popularity.png
    ├── v17_profit_over_time.png
    ├── v18_correlation_heatmap.png
    └── v19_runtime_by_genre.png
```

---

## 🚀 How to Run in Google Colab

### Step-by-Step

1. Go to [Google Colab](https://colab.research.google.com/)
2. **File → Upload Notebook** → Select `IMDB_Movie_EDA.ipynb`
3. In the left sidebar, click the 📁 Files icon
4. Click **Upload** and select `imdb_data.csv`
5. **Runtime → Run All**

### Alternative: Google Drive Mount

```python
from google.colab import drive
drive.mount('/content/drive')
FILE_PATH = '/content/drive/MyDrive/imdb_data.csv'
```

### Requirements (all pre-installed in Colab)

```
pandas>=1.5.0
numpy>=1.23.0
matplotlib>=3.6.0
seaborn>=0.12.0
```

---

## 🎯 Producer Recommendations

Based on the full analysis, here are the key data-driven recommendations:

### Genre
- **Invest in Horror** for best ROI with limited capital
- **Action-Adventure** for maximum absolute profit (requires larger budget and established IP)
- **Thriller/Crime** for consistent returns with moderate risk

### Language
- **English** for maximum market reach and statistical reliability
- Consider **Hindi/Tamil** co-productions for South Asian market access

### Casting
- Cast **Samuel L. Jackson** or actors with proven genre fit and high average profit per film
- Build long-term **director-actor partnerships** (Spielberg+Ford, Howard+family) for brand consistency

### Budgeting
- Higher budgets correlate with higher revenues but NOT higher ROI
- A tight $5M–$20M Horror/Thriller budget can generate disproportionate returns

---

## 🔮 Future Work

- **Sentiment Analysis** on movie overviews and taglines to predict genre tone
- **Time Series Forecasting** of genre profitability trends
- **Network Analysis** of actor-director co-occurrence graphs
- **ML Models** to predict movie profitability from pre-production features (genre, cast, budget)
- **Streaming era analysis** — how post-2018 streaming affected theatrical ROI

---
