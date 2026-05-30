 Northern Trust (NTRS) — SEC XBRL Financial Data Analysis



## Overview

Using Python, I sourced SEC EDGAR's XBRL Financial Statement Data Sets (4 raw `.txt` files — `tag`, `num`, `pre`, `sub`) for **Northern Trust Corporation (NTRS)** from Kaggle. The raw data contained **1,200+ entries spanning multiple fiscal years**, which required a proper big-data handling approach rather than just loading everything into memory at once.

The entire pipeline is built in Python converting raw tab-separated files into CSVs, loading them into a **SQLite database** for efficient SQL querying, merging and filtering down to NTRS-specific records, applying data cleaning (deduplication, null handling, amendment filtering), running **inferential statistics** (t-test, chi-square, bootstrap confidence interval), and exporting the final analysis-ready output to Excel.



## Why SQLite Instead of Just Pandas?

The raw `num.txt` file alone contains hundreds of thousands of rows across all SEC filers. Loading it directly into a dataframe would consume significant memory and slow everything down. By converting to CSV in chunks first, then loading into SQLite, I could run fast SQL queries to filter just Northern Trust's records before ever pulling data into Pandas keeping memory usage low and the pipeline scalable.



 ## Project Structure


PROJECT DATA/
├── NTRS Data/
│   ├── num.txt        ← Raw XBRL numerical values
│   ├── pre.txt        ← Presentation map
│   ├── sub.txt        ← Company/submission identity
│   └── tag.txt        ← Tag definitions
├── csv/
│   ├── num.csv
│   ├── pre.csv
│   ├── sub.csv
│   └── tag.csv
├── excel/
│   ├── Northern_Trust_2024_Final.xlsx
│   ├── Northern_Trust_2025_Final.xlsx
│   └── Northern_Trust_Final_Output.xlsx
├── main.ipynb                      ← Main analysis notebook
├── master.sqlite                   ← SQLite database
├── NTRS_Raw_Data_Proof.csv
├── requirements.txt
└── data_details.txt




## Pipeline — Step by Step

Step 1 — Raw TSV → CSV
- Reads `.txt` tab-separated source files in **chunks** (memory-efficient)
- Writes clean `.csv` files to the `csv/` folder

 Step 2 — Load into SQLite
- Loads all 4 CSV files into a single **SQLite database** (`master.sqlite`)
- Enables fast SQL queries without loading full tables into memory

 Step 3 — Filter for Northern Trust
- Filters by `cik = 73124` (Northern Trust)
- Merges `num + tag + pre + sub` into one master dataset via SQL joins

 Step 4 — Data Cleaning
- Removes segment-level rows (`segments IS NULL`)
- Removes co-registrant rows (`coreg IS NULL`)
- Filters to annual/point-in-time periods (`qtrs IN (0, 4)`)
- Deduplicates by keeping the latest filed amendment per `(tag, version, ddate)`

Step 5 — Descriptive Analysis & EDA
- `.head()`, `.describe()`, `.info()`, `.isnull().sum()`
- Null value audit across all columns

Step 6 — Inferential Statistics & Plots
- **Normality test** on `log1p(|value|)` sample
- **One-sample t-test** on trimmed value distribution
- **Bootstrap confidence interval** for the mean of `value`
- **Chi-square test** on collapsed SIC × fiscal year categories (`sub`)
- **Correlation matrix** across numeric columns in `sub`
- **Histogram** of reported financial magnitudes

 Step 7 — Export
- Exports separate Excel workbooks per year to `excel/`



## Skills & Tools Used

| Category | Tools |
|----------|-------|
| Languages | Python 3.14, SQL |
| Data Manipulation | Pandas, NumPy |
| Database | SQLite (`sqlite3`) |
| Statistical Tests | SciPy (t-test, chi-square, normality), Bootstrap CI |
| Visualization | Matplotlib |
| Data Normalization | Log transformation (`log1p`) |
| File I/O | openpyxl, pathlib, chunked CSV processing |
| Environment | Jupyter Notebook, VS Code, venv |



## Data Source

- **SEC EDGAR — Financial Statement Data Sets**
- Available on [Kaggle](https://www.kaggle.com/) and directly from [SEC.gov](https://www.sec.gov/dera/data/financial-statements)
- All data is **publicly available** under SEC open data policy



## Key Takeaways

- Demonstrates handling of **real-world messy financial filings** (duplicates, amendments, segment splits)
- Uses **SQL + Python together** — not just Pandas — for scalable querying on large datasets
- Applies **inferential statistics** to validate data quality and distributions
- Produces **analysis-ready Excel outputs** for financial modeling



Author

Aenika Desai  
(https://www.linkedin.com/in/aenikadesai/)
(https://github.com/aenikadesai90)
