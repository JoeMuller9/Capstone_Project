# In-Demand Developer Skills Analysis

A data analytics project built as the capstone for the **IBM Professional Data Analyst Certification**. The brief: act as a data analyst at a global IT and business consulting firm and identify which programming languages, database technologies and IDEs are currently most in demand, using the Stack Overflow Developer Survey alongside supplementary job-posting and salary data.

## Overview

The project follows a full analytics workflow — collecting data from multiple sources, cleaning and wrangling it into a usable form, exploring it statistically, and querying and visualising it to surface trends — using the **Stack Overflow Developer Survey** (65,437 respondents, 114 columns) as the primary dataset.

## Objectives

- Identify the most in-demand programming languages, database technologies and IDEs
- Understand how skill demand and compensation vary by role, experience level, employment type and country
- Practise the full data analyst workflow: collection → wrangling → EDA → SQL querying → visualisation

## Data Sources

- **Stack Overflow Developer Survey** — the primary dataset (survey-data.csv), 65,437 rows × 114 columns
- **Job postings API** — job counts by technology (e.g. Python, Java, SQL Server) and by location (e.g. New York, Seattle, Austin), pulled via a REST API and exported to Excel
- **Web-scraped salary data** — average annual salary by programming language, scraped from an HTML table using BeautifulSoup

## Repository Structure

| Notebook | Purpose |
|---|---|
| `Data_Collection_APIs_Web_Scraping.ipynb` | Pulls job-posting counts via API calls, scrapes a salary-by-language table with BeautifulSoup, and loads the raw survey dataset |
| `Data_Wrangling.ipynb` | Cleans and prepares the survey data: standardises inconsistent categories, handles missing values, encodes categorical variables, scales and transforms skewed fields, and engineers new features |
| `Exploratory_Data_Analysis.ipynb` | Explores the cleaned dataset statistically and visually: distributions, cross-tabulations, correlations and outlier detection |
| `Data_Visualisation_SQL_Querying.ipynb` | Loads the data into a SQLite database and uses SQL queries (via `%%sql` magic) to drive a set of visualisations |

## Methodology

**Data collection** — Job counts by technology and by US city were retrieved via API calls and written to a multi-sheet Excel workbook (`job-postings.xlsx`). Average salary by programming language was scraped from an HTML table and saved to CSV. The main survey dataset was loaded directly from its hosted CSV source.

**Data wrangling** — Country names were standardised (16 of 185 unique values needed normalising, e.g. "Russian Federation" → "Russia"), education levels were simplified into 8 consistent categories, and the `Employment` field was one-hot encoded into 9 binary columns. Missing values were handled deliberately rather than uniformly: numeric fields were filled with the mean and categorical fields with the mode, but only after checking which columns had missing data and why. Compensation (`ConvertedCompYearly`) was heavily right-skewed (skewness of 88.4) due to a small number of extreme values, so a log transform was applied, reducing skewness to -4.4. `YearsCodePro` was binned into Junior/Mid-Level/Senior experience bands for group-level comparisons.

**Exploratory data analysis** — Rather than imputing every missing value by default, fields with very high missingness (e.g. `JobSat`, ~55% missing) were deliberately left as `NaN` instead of being filled with a mean or mode, since doing so would have manufactured a false majority value and distorted every group-level comparison run on that field afterwards. Lower-missingness fields (e.g. `RemoteWork`, ~16% missing) were mode-imputed, since the risk of distortion was much smaller. Analysis covered remote-work preference by employment type and role, programming language usage by country (normalised to % of respondents per country so that the US — the largest single sample — didn't dominate the comparison purely by sample size), the relationship between experience and job satisfaction, and outlier detection in compensation using the IQR method.

**SQL querying and visualisation** — The cleaned dataset was loaded into a local SQLite database and queried directly using SQL (via Jupyter's `%%sql` magic), including group-by counts, filtered subsets and aggregations, each feeding into a corresponding chart (bar charts, box plots, heatmaps, a pie chart of the top 5 most-desired databases, and scatter/regression plots).

## Key Findings

- Experience and job satisfaction are only weakly correlated (Pearson r ≈ 0.10, Spearman ρ ≈ 0.12) — more years of professional coding experience does not strongly predict higher job satisfaction in this sample.
- Remote-work preference varies significantly by employment type: independent contractors/freelancers report working remotely far more often (~67%) than full-time employees (~34%).
- Programming language popularity varies meaningfully by country once normalised for sample size, rather than simply reflecting which country has the most respondents.
- Reported compensation is heavily right-skewed, with a small number of extreme high values; log-transforming and applying IQR-based outlier removal were both necessary before compensation could be compared fairly across countries.

## Tools & Technologies

- **Python**: Pandas, NumPy, Matplotlib, Seaborn
- **SQL**: SQLite, `ipython-sql`
- **Data collection**: `requests` (API calls), BeautifulSoup (web scraping), `openpyxl`
- **Environment**: Jupyter Notebook

## Acknowledgements

Survey dataset and course materials provided by IBM Skills Network as part of the IBM Professional Data Analyst Certification.
