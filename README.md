# Netflix Titles — Data Cleaning with Python & Pandas

A hands-on data cleaning project on the Netflix Movies & TV Shows dataset (8,807 titles).
The goal: turn a raw, messy file into an analysis-ready dataset **without losing a single row**.

---

## Business Problem

Raw data is rarely ready for analysis. Before a BI analyst can build a dashboard or answer questions like
*"Which countries produce the most content?"* or *"How long are Netflix movies on average?"*,
the data must be profiled, cleaned, and standardized.

This project documents that process step by step.

## Dataset

| Item | Details |
|---|---|
| Source | Netflix Movies and TV Shows dataset (Kaggle) |
| Rows | 8,807 |
| Columns | 12 (13 after cleaning) |
| Content | `show_id`, `type`, `title`, `director`, `cast`, `country`, `date_added`, `release_year`, `rating`, `duration`, `listed_in`, `description` |

## Tools

- **Python** (Pandas, NumPy)
- **Jupyter Notebook**

## Cleaning Steps

1. **Loaded** the CSV and inspected it with `info()` and `dtypes`.
2. **Checked for duplicates** with `duplicated()` → **0 duplicate rows**.
3. **Fixed data types**
   - `release_year` → `int64`
   - `date_added` → `datetime`
4. **Split the `duration` column** into two usable columns:
   - `duration_value` (number, e.g. `90`, `2`)
   - `duration_type` (`min`, `Season`, `Seasons`)

   Why? `"90 min"` and `"2 Seasons"` are text, so you can't calculate averages or filter by length.
5. **Handled missing values** (see the table below).
6. **Exported** the result to `netflix_titles_clean.csv`.

```python
# Split duration into value + type
df[['duration_value', 'duration_type']] = df['duration'].str.split(' ', expand=True)
df['duration_value'] = df['duration_value'].fillna(0).astype('int64')

# Fill missing text values instead of deleting rows
for col in ['director', 'cast', 'country', 'duration_type']:
    df[col] = df[col].fillna('Unknown')
```

## Key Decision: I Did NOT Drop Rows With Missing Values

The easy option is `df.dropna()`. I tested it and chose **not** to use it.

| Approach | Rows left | Data lost |
|---|---|---|
| Original data | 8,807 | – |
| `dropna()` | **5,332** | **3,475 rows (~39%)** |
| Fill with `Unknown` (my approach) | **8,807** | **0 rows** |

**Why?**

- Most missing values are in `director` (2,634) and `cast` (825). A title without a listed director is still a valid title with a valid `type`, `country`, `release_year`, and `rating`.
- Dropping those rows would **remove ~39% of the catalog** and bias every later analysis (for example, counts by country or by year would be wrong).
- Filling with `Unknown` keeps all rows, and the missing information stays **visible and filterable** instead of silently disappearing.

### Missing values: before vs. after

| Column | Missing (raw) | Treatment |
|---|---|---|
| `director` | 2,634 | Filled with `Unknown` |
| `country` | 831 | Filled with `Unknown` |
| `cast` | 825 | Filled with `Unknown` |
| `date_added` | 10 | Filled with `Null` label |
| `duration` | 3 | `duration_value` = 0, `duration_type` = `Unknown` |
| `rating` | 4 | Left as-is (flagged for review) |

## Result

- **8,807 rows preserved** (100% of the data)
- Correct data types for dates and numbers
- `duration` split into analyzable numeric + category columns
- Only 4 `rating` values remain empty, intentionally left for review

## Lessons Learned / Things I'd Improve

- **Filling is not always the best choice.** Placeholders like `Unknown` are good for text categories, but `0` in `duration_value` and the text `Null` in `date_added` can distort calculations (e.g. average duration). A better approach is to keep these as real empty values (`NaN` / `NaT`) and exclude them only when calculating.
- Use a **relative file path** instead of a local Windows path, so the notebook runs on any machine.
- Always reassign results (`df = df.drop_duplicates()`); calling the method without assignment doesn't change the data.

## Project Structure

```
netflix-titles-data-cleaning/
├── README.md
├── netflix_titles_clean.ipynb     # cleaning notebook
└── data/
    ├── netflix_titles.csv         # raw data
    └── netflix_titles_clean.csv   # cleaned data
```

## How to Run

```bash
pip install pandas numpy jupyter
jupyter notebook netflix_titles_clean.ipynb
```

## Next Steps

- Explore the cleaned data: content by country, genre trends, movies vs. TV shows over time
- Build a **Power BI** dashboard on top of the cleaned file
- Split multi-value columns (`cast`, `listed_in`, `country`) for deeper analysis

## Author

**Ziad Khalil** —  Data Analysis & BI
[LinkedIn](www.linkedin.com/in/ziad-khalil-dev) · [GitHub]([https://github.com/your-username](https://github.com/ziadkhalil04-jpg)) 
[LiveDimo](https://drive.google.com/file/d/1XlQUEB9QAWhh4cpeAl65-j_YdADjqJsf/view?usp=drive_link)
