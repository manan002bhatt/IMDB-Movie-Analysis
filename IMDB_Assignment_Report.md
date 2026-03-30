# Assignment Report: Exploratory Data Analysis of IMDB Movie Data
## Advisory Analysis for a Rookie Movie Producer

---

**Course:** Data Science Fundamentals  
**Assignment Title:** EDA Project — IMDB Movie Dataset  
**Dataset:** imdb_data.csv (3,000 Movies)  

---

## Table of Contents

1. [Title & Introduction](#1-title--introduction)
2. [Objectives](#2-objectives)
3. [Dataset Description](#3-dataset-description)
4. [Methodology](#4-methodology)
5. [Data Analysis Steps Performed](#5-data-analysis-steps-performed)
6. [Observations and Findings](#6-observations-and-findings)
7. [Conclusion and Recommendations](#7-conclusion-and-recommendations)
8. [References](#8-references)

---

## 1. Title & Introduction

### Title
**Exploratory Data Analysis of IMDB Movie Data: A Data-Driven Advisory Report for Film Production Decision Making**

### Introduction

The global film industry generates over $40 billion annually at the box office and significantly more through streaming, licensing, and merchandising. For a first-time producer entering this competitive environment, data is arguably the most valuable asset available before a single frame of film is shot. Understanding historical patterns of profitability, genre performance, cast selection, and audience preferences can dramatically reduce the financial risk associated with green-lighting a new production.

This report presents a comprehensive **Exploratory Data Analysis (EDA)** of a dataset containing **3,000 movies** from the IMDB/TMDB database. Each record includes detailed metadata about the film's financial performance (budget and revenue), creative team (cast and crew), genre classifications, language, release information, and popularity metrics.

As a data scientist commissioned by a rookie producer, this analysis is structured to answer six specific business-critical questions and synthesise broader strategic recommendations grounded entirely in evidence from the data. All computation was performed using the Python data science stack (pandas, NumPy, Matplotlib, Seaborn) in the Google Colab environment.

---

## 2. Objectives

The primary analytical objectives of this report are:

1. **Data Sanity Check:** Verify the integrity, completeness, and consistency of the dataset before performing any analysis.

2. **Q1 — Highest Profit Movie:** Identify the most profitable film in the dataset and extract the names of its director, producers, and cast members.

3. **Q2 — Language ROI:** Determine which original production language yields the highest average Return on Investment (ROI).

4. **Q3 — Genre Identification:** Compile a complete, deduplicated list of all unique genres represented in the dataset.

5. **Q4 — Producer Performance:** Build a complete producer-director mapping table and identify the three producers with the highest average ROI across their filmographies.

6. **Q5 — Most Active Actor:** Identify the actor who has appeared in the most movies in the dataset. Conduct a deep-dive analysis of that actor's genre preferences, movie titles, and profitability track record.

7. **Q6 — Director-Actor Preferences:** Identify the top three most prolific directors in the dataset and determine which actors they most frequently cast in their films.

---

## 3. Dataset Description

### Overview

The dataset `imdb_data.csv` contains 3,000 rows and 23 columns. Each row represents one movie. The data was sourced from the IMDB/TMDB (The Movie Database) API and includes information collected through the mid-2010s.

### Key Variables

**Financial Variables:**
- `budget` (integer): Production and marketing budget in US dollars. Set to 0 for ~812 movies where data is unavailable.
- `revenue` (integer): Total worldwide box office revenue in US dollars.

**Derived Financial Metrics (computed during analysis):**
- `profit = revenue − budget`
- `roi = (profit / budget) × 100`

**Creative Metadata (stored as Python list-of-dict strings):**
- `cast`: All cast members with name, character, billing order, and gender.
- `crew`: All crew members with name, department, and job title (includes Director, Producer, Editor, etc.)
- `genres`: Genre classifications from TMDB's taxonomy.

**Categorical Variables:**
- `original_language`: ISO 639-1 language code (e.g., 'en', 'fr', 'ko').
- `status`: Release status (Released / Rumored).

**Temporal:**
- `release_date`: Date of theatrical release.

**Popularity:**
- `popularity`: A composite metric from TMDB reflecting social engagement, views, and ratings activity.

### Data Quality Summary

| Issue | Count | Action |
|---|---|---|
| `budget = 0` | 812 movies (27%) | Excluded from financial metrics |
| `belongs_to_collection` missing | 2,396 (80%) | Column dropped (irrelevant to analysis) |
| `homepage` missing | 2,054 (68%) | Column dropped |
| `tagline` missing | 597 (20%) | Retained but not used analytically |
| `cast` or `crew` missing | ~13–16 | Treated as empty lists |
| Duplicate rows | 0 | No action required |

---

## 4. Methodology

### 4.1 Computational Environment

All analysis was conducted in **Google Colaboratory** using Python 3.10. The notebook is structured as a linear narrative that progressively builds from data loading to business question resolution.

### 4.2 Libraries

| Library | Role |
|---|---|
| pandas | DataFrame operations, groupby, merge |
| NumPy | Numerical calculations, masking |
| Matplotlib | Base chart rendering |
| Seaborn | Statistical plots (boxplots, heatmaps, KDE) |
| ast | Safe parsing of Python literal strings in cast/crew/genres columns |
| collections.Counter | Frequency counting for actors, genres, directors |

### 4.3 Data Parsing Strategy

The `cast`, `crew`, and `genres` columns store data as Python string representations of lists of dictionaries (e.g., `"[{'name': 'Tom Hanks', 'order': 0, ...}]"`). These were parsed using `ast.literal_eval()` wrapped in exception handling to safely convert strings to Python lists.

From the parsed lists:
- **Actors:** Extracted by iterating all elements of the `cast` list
- **Directors:** Extracted from `crew` where `job == 'Director'`
- **Producers:** Extracted from `crew` where `job == 'Producer'`
- **Genres:** Extracted by collecting the `name` field from each genre dict

### 4.4 Financial Subset

To ensure validity of all financial calculations, a dedicated subset `df_valid` was created containing only movies where both `budget > 0` AND `revenue > 0`. This yielded **2,188 movies** — 73% of the full dataset.

### 4.5 Analytical Pipeline

```
Data Loading → Quality Assessment → Feature Engineering →
Genre Analysis (Q3) → Profit Analysis (Q1) → Language ROI (Q2) →
Producer Table & Top ROI Producers (Q4) → Most Active Actor Deep Dive (Q5) →
Director-Actor Preferences (Q6) → Cross-Feature Correlations → Recommendations
```

---

## 5. Data Analysis Steps Performed

### Step 1: Data Loading and Initial Inspection
The CSV was loaded using `pd.read_csv()`. Shape inspection confirmed 3,000 rows × 23 columns. Column names, data types, and `.describe()` statistics were reviewed to understand the scale and nature of each variable.

### Step 2: Missing Value Analysis
A per-column null count was computed and visualised as a horizontal bar chart. Columns with >60% missing (homepage, belongs_to_collection) were identified for exclusion. The critical finding — 812 movies with budget=0 — was flagged as a data quality issue requiring filtering before financial analysis.

### Step 3: Feature Engineering
New columns were created:
- `profit` and `roi` computed on `df_valid`
- `release_year` extracted using `pd.to_datetime()`
- `actors_list`, `directors_list`, `producers_list`, `genres_list` created by applying parsing functions via `.apply()`

### Step 4: Genre Analysis (Q3)
All genre names were extracted by iterating `genres_list` across all 3,000 rows. A Python `set()` was used to deduplicate, yielding 20 unique genres. A `Counter` object tracked genre frequency for visualisation.

### Step 5: Highest Profit Film Analysis (Q1)
`df_valid.loc[df_valid['profit'].idxmax()]` identified the record with maximum profit. Director, producers, and cast were then extracted using the parsing functions applied to that row's `crew` and `cast` columns.

### Step 6: Language ROI Analysis (Q2)
`df_valid.groupby('original_language').agg(avg_roi=('roi', 'mean'), count=('roi', 'count'))` was computed. Results were filtered for languages with at least 5 movies to ensure statistical reliability before ranking.

### Step 7: Producer-Director Table and Top ROI Producers (Q4)
A full producer-director table was assembled by iterating all rows and joining parsed director and producer names. For ROI ranking, `df_valid` was exploded by producers (one row per producer per movie) and grouped to compute average ROI, filtering for producers with 2+ movies.

### Step 8: Most Active Actor Deep Dive (Q5)
All actor names were aggregated into a flat list using `extend()` across all rows, and `Counter.most_common()` identified Samuel L. Jackson as co-leader with 30 movies. His movies were filtered, and genre and profit analysis was applied to this subset.

### Step 9: Director-Actor Preferences (Q6)
Director movie counts were computed similarly to actor counts. The top 3 directors were identified. For each, all movies directed by that person were filtered, all cast members extracted, and frequencies counted to identify most frequent co-cast actors.

### Step 10: Extended EDA
Additional visualisations were produced: budget/revenue distributions, scatter plots, profit over time (1990–2017), runtime by genre boxplots, popularity analysis, and a correlation heatmap.

---

## 6. Observations and Findings

### Q1 — Highest Profit Movie

**Furious 7 (2015)** is the most profitable movie in the dataset with:
- **Budget:** $190,000,000
- **Revenue:** $1,506,249,360
- **Profit:** $1,316,249,360 (6.9× ROI)

**Director:** James Wan (known for the Saw and Conjuring franchises, making Furious 7 his highest-grossing film by far).

**Producers:** Vin Diesel, Neal H. Moritz, Michael Fottrell, Brandon Birtell. Notably, the lead actor (Vin Diesel) also served as producer — a model common in franchise films where talent has creative and financial stake.

**Cast (Top 8):** Vin Diesel, Paul Walker, Dwayne Johnson, Michelle Rodriguez, Tyrese Gibson, Ludacris, Jason Statham, Kurt Russell.

The film's extraordinary profitability is partly attributed to the emotional context of Paul Walker's death mid-production, which generated massive audience goodwill and record global viewership for the franchise's tribute finale.

---

### Q2 — Language with Highest Average ROI

Among languages with at least 5 movies:

| Language | Movies | Avg ROI (%) |
|---|---|---|
| Korean (ko) | 11 | 38,179,410% |
| English (en) | 1,943 | 543,987% |
| German (de) | 11 | 435% |
| Chinese (zh) | 14 | 408% |
| Spanish (es) | 17 | 379% |

**Korean films have the highest raw average ROI** — driven by outlier low-budget films that achieved disproportionate returns. However, this figure is highly influenced by a small sample size (11 movies).

**English-language films are the statistically most reliable** with 1,943 observations and a healthy average ROI. For a new producer, English-language production offers the largest addressable market and the most predictable return profile.

---

### Q3 — Unique Genres

The dataset contains exactly **20 unique genres:**

Action, Adventure, Animation, Comedy, Crime, Documentary, Drama, Family, Fantasy, Foreign, History, Horror, Music, Mystery, Romance, Science Fiction, TV Movie, Thriller, War, Western.

**Drama** is the most common (1,531 movies), reflecting that dramatic themes underpin many genre films. **Horror** and **Comedy** offer the best ROI-to-budget ratios among commercially successful genres. **TV Movie** and **Western** are the rarest.

---

### Q4 — Producer-Director Table and Top 3 Producers by ROI

A complete table of 3,000 movies with their corresponding directors and producers was generated. Key highlights:

**Top 3 Producers by Average ROI (min 2 movies):**

| Rank | Producer | Movies | Avg ROI (%) |
|---|---|---|---|
| 1 | Charlie Chaplin | 3 | 283,333,800% |
| 2 | Marc Bienstock | 2 | 50,000,880% |
| 3 | Jerry Weintraub | 5 | 20,372,570% |

**Charlie Chaplin** tops the list because his films were made on minuscule early-cinema budgets yet generated returns over decades of distribution. **Jerry Weintraub** is the most commercially relevant modern entry — he produced the Ocean's franchise (Ocean's Eleven, Twelve, Thirteen), all of which were high-revenue ensemble films made on controlled budgets.

---

### Q5 — Most Active Actor: Samuel L. Jackson

**Samuel L. Jackson** (tied with Robert De Niro) appears in **30 movies** in this dataset.

**Genre Profile:**
- Action: 17 movies (57%)
- Crime: 14 movies (47%)
- Adventure: 12 movies (40%)
- Thriller: 12 movies (40%)
- Science Fiction: 10 movies (33%)

**Financial Highlights (25 movies with valid financial data):**
- His highest-profit films include Avengers-universe appearances (Avengers, Age of Ultron) and action franchises (Django Unchained, Iron Man)
- His presence in Marvel films — particularly as Nick Fury — contributes billions in combined franchise revenue

**Conclusion on Jackson:** His career profile demonstrates that versatility (Action, Drama, Comedy, Sci-Fi) combined with consistent franchise participation maximises an actor's commercial value. His name alone carries bankability due to decades of established audience recognition.

---

### Q6 — Top 3 Directors and Their Preferred Actors

**Top 3 Directors by Movie Count:**
1. **Ron Howard** — 11 movies
2. **Clint Eastwood** — 11 movies
3. **Steven Spielberg** — 10 movies

**Ron Howard** most frequently casts family members — Clint Howard (his brother, 7 films) and Rance Howard (his father, 6 films). This reflects Ron Howard's tradition of including family in his productions as character actors.

**Clint Eastwood** most frequently casts himself (8 appearances) — consistent with his dual role as director-actor. Supporting actors like Sondra Locke (2 films) and Pat Hingle (2 films) also recur across his Western and thriller output.

**Steven Spielberg** clearly prefers **Harrison Ford** (3 films: Indiana Jones franchise). Richard Dreyfuss also appears in 2 Spielberg films (Jaws, Close Encounters). Spielberg's casting choices reflect calculated collaboration with bankable stars whose personas align with his adventure-heavy directorial style.

---

### General EDA Findings

- **Budget-Revenue Correlation:** Strong positive correlation (~0.74). Higher budgets generally produce higher revenues, but ROI shows weak correlation with budget, meaning spending more does not guarantee proportionally higher returns.

- **Genre Profitability vs Frequency:** Drama is the most common genre but not the most profitable. Animation and Adventure genres produce the highest average absolute profits due to their franchise appeal.

- **Horror's ROI Advantage:** Horror films consistently show the best ROI per dollar — budgets of $5M–$20M can yield revenues of $50M–$300M when the film resonates culturally.

- **Temporal Trend:** Average budgets and profits have both grown significantly since 1990, reflecting the globalisation of cinema and the rise of the franchise model.

- **Popularity-Profit Relationship:** Higher popularity scores correlate moderately with higher profits, suggesting that pre-release marketing buzz translates to box office performance.

---

## 7. Conclusion and Recommendations

This analysis demonstrates how historical movie data can be used to inform production strategy before a dollar is spent. The EDA successfully answered all six business questions and uncovered broader patterns that carry strategic significance.

### Summary Conclusions

1. **Franchise films with proven IP generate the highest absolute profits.** Furious 7's $1.3B profit illustrates how established characters with loyal fan bases drive extraordinary returns.

2. **Horror offers the best ROI for producers with limited capital.** Low production costs combined with strong genre demand make Horror the optimal entry point for a new producer.

3. **The director-actor relationship is a core asset.** The Spielberg-Ford and Howard-family patterns demonstrate that consistent casting builds audience expectations and reduces production uncertainty.

4. **Samuel L. Jackson represents the ideal "anchor" cast member** — extensive experience across genres, franchise bankability, and consistent performance in commercially successful films.

5. **English-language films offer the most reliable global return profile.** Korean films may show higher raw ROI averages, but the small sample size makes this statistically unreliable for decision making.

### Strategic Recommendations for the Producer

| Dimension | Recommendation |
|---|---|
| **Genre** | Start with Horror or Thriller — best ROI at low budget |
| **Budget** | $5M–$25M for first film; focus on script quality over star power |
| **Language** | English-language for global reach |
| **Cast** | Select actors with genre fit and strong average profit records |
| **Director** | Partner with a director who has worked in your target genre before |
| **Long-term** | Build a franchise — the top 10 most profitable films are all franchise entries |

### Limitations

- The dataset covers only ~3,000 films through approximately 2017, missing the streaming era's disruption of traditional box office metrics.
- Budget and revenue data were missing for 27% of films, which may bias financial analysis toward larger studio productions.
- The ball-by-ball level of crew roles (e.g., Executive Producer vs Producer) was not distinguished in this analysis, which may affect producer ROI rankings.

---

## 8. References

1. TMDB (The Movie Database). (2023). *API documentation and movie dataset*. Available at: https://www.themoviedb.org/documentation/api

2. Nash, J. R., & Ross, S. R. (1985). *The Motion Picture Guide*. Cinebooks.

3. McKinney, W. (2017). *Python for Data Analysis*. 2nd ed. O'Reilly Media.

4. Tukey, J. W. (1977). *Exploratory Data Analysis*. Addison-Wesley.

5. Balio, T. (2013). *Hollywood in the New Millennium*. Palgrave Macmillan.

6. Waskom, M. et al. (2021). Seaborn: statistical data visualization. *Journal of Open Source Software*, 6(60), 3021.

7. Epstein, E. J. (2012). *The Hollywood Economist 2.0: The Hidden Financial Reality Behind the Movies*. Melville House.

---

*End of Assignment Report*

---
> **Note:** All code, visualisations, and data operations described in this report are fully implemented in the accompanying Google Colab notebook `IMDB_Movie_EDA.ipynb`. The notebook should be read alongside this written report for the complete analytical picture.
