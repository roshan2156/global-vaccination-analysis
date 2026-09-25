# 🌍 Global Vaccination Analysis

> **Healthier People | Stronger Communities | Brighter Tomorrows**

An end-to-end data analytics project that turns raw global vaccination datasets into cleaned data, a structured MySQL database, and an interactive Power BI dashboard.

---

## 📌 Project Overview

**Global Vaccination Analysis** focuses on understanding global vaccination coverage, disease incidence, reported cases, vaccine introductions, and vaccination schedules.

The project uses **Python, SQL/MySQL, and Power BI** to support public health and epidemiology use cases such as:

- Understanding vaccination coverage trends
- Examining disease incidence and reported cases
- Identifying geographical disparities in vaccination coverage
- Studying vaccine introduction patterns
- Exploring vaccination schedules and target populations
- Comparing vaccination coverage with disease incidence
- Supporting public health planning and resource allocation

---

## 🎯 Problem Statement

Vaccination plays an important role in preventing infectious diseases and improving population health. However, vaccination coverage and disease incidence vary significantly across countries, regions, diseases, and years.

This project analyzes global vaccination data to identify:

- Trends in vaccination coverage and disease incidence
- Changes in reported disease cases
- Differences in coverage between countries and regions
- Vaccine introduction patterns
- Vaccination schedules and target populations
- Relationships between vaccination coverage and disease incidence

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| **Python** | Data cleaning, preprocessing and analysis |
| **Pandas** | Data manipulation and transformation |
| **NumPy** | Numerical operations |
| **Matplotlib** | Data visualization |
| **Seaborn** | Exploratory visualization |
| **MySQL** | Structured data storage and SQL analysis |
| **phpMyAdmin / XAMPP** | MySQL database management |
| **Power BI** | Interactive dashboard and DAX measures |
| **Git & GitHub** | Version control and project sharing |

---

## 🚀 Project Workflow

```text
Raw Excel Data
      ↓
Python Data Loading
      ↓
Data Cleaning & Validation
      ↓
Exploratory Data Analysis
      ↓
Clean CSV Files
      ↓
MySQL Database
      ↓
Power BI Data Model + DAX Measures
      ↓
Interactive Dashboard
      ↓
Public Health Insights
```

---

## 📂 Project Structure

```text
global-vaccination-analysis/
│
├── cleaned_data_csv/
│   ├── coverage_clean.csv
│   ├── incidence_rate_clean.csv
│   ├── reported_cases_clean.csv
│   ├── vaccine_introduction_clean.csv
│   └── vaccine_schedule_clean.csv
│
├── raw data/
│   └── Original Excel datasets
│
├── Vaccination_analysis_data_cleaning_COMPLETE.ipynb
├── vaccination project workbook.pbix
├── .gitattributes
├── .gitignore
└── README.md
```

---

## 📊 Datasets

The project uses five datasets.

| Dataset | Rows | Columns | Description |
|---|---:|---:|---|
| Vaccination Coverage | 399,859 | 11 | Coverage by country, year, antigen and coverage category |
| Incidence Rate | 84,946 | 8 | Disease incidence by country and year |
| Reported Cases | 84,870 | 7 | Reported disease cases by country and year |
| Vaccine Introduction | 138,321 | 6 | Vaccine introductions by country and WHO region |
| Vaccine Schedule | 8,053 | 12 | Schedules, target populations, age administered, geographic area |

*Row counts are approximate and refer to the original datasets.*

<details>
<summary><b>Column details for each dataset</b></summary>

**1. Vaccination Coverage**
`GROUP`, `CODE`, `NAME`, `YEAR`, `ANTIGEN`, `ANTIGEN_DESCRIPTION`, `COVERAGE_CATEGORY`, `COVERAGE_CATEGORY_DESCRIPTION`, `TARGET_NUMBER`, `DOSES`, `COVERAGE`

**2. Incidence Rate**
`GROUP`, `CODE`, `NAME`, `YEAR`, `DISEASE`, `DISEASE_DESCRIPTION`, `DENOMINATOR`, `INCIDENCE_RATE`

**3. Reported Cases**
`GROUP`, `CODE`, `NAME`, `YEAR`, `DISEASE`, `DISEASE_DESCRIPTION`, `CASES`

**4. Vaccine Introduction**
`ISO_3_CODE`, `COUNTRYNAME`, `WHO_REGION`, `YEAR`, `DESCRIPTION`, `INTRO`

**5. Vaccine Schedule**
`ISO_3_CODE`, `COUNTRYNAME`, `WHO_REGION`, `YEAR`, `VACCINECODE`, `VACCINE_DESCRIPTION`, `SCHEDULEROUNDS`, `TARGETPOP`, `TARGETPOP_DESCRIPTION`, `GEOAREA`, `AGEADMINISTERED`, `SOURCECOMMENT`

</details>

---

## 🧹 Data Cleaning (Python)

Cleaning was performed in the Jupyter notebook using Pandas and NumPy.

**1. Load datasets**

```python
coverage_df = pd.read_excel("/content/coverage-data.xlsx")
incidence_df = pd.read_excel("/content/incidence-rate-data.xlsx")
reported_cases_df = pd.read_excel("/content/reported-cases-data.xlsx")
vaccine_introduction_df = pd.read_excel("/content/vaccine-introduction-data.xlsx")
vaccine_schedule_df = pd.read_excel("/content/vaccine-schedule-data.xlsx")
```

**2. Handle missing values** – rows missing key identifying fields were removed:

```python
coverage_df = coverage_df.dropna(subset=["CODE", "NAME", "YEAR", "ANTIGEN"])
```

**3. Text cleaning** – leading and trailing spaces were stripped from text columns.

**4. Negative-value validation** – an invalid negative value found in the dose data was replaced with a missing value:

```python
coverage_df.loc[coverage_df["DOSES"] < 0, "DOSES"] = np.nan
```

**5. Optional missing values** – in the vaccine schedule dataset, missing descriptive fields (`TARGETPOP`, `GEOAREA`, `AGEADMINISTERED`, `SOURCECOMMENT`) were replaced with `Not Available`.

**6. Duplicate checks** – duplicate records were checked across all datasets.

**7. Export** – cleaned datasets were exported as CSV files for database integration and Power BI.

---

## 🗄️ SQL Database

A MySQL database named `vaccination_analysis` was created using MySQL, XAMPP and phpMyAdmin. It contains five tables: `coverage`, `incidence_rate`, `reported_cases`, `vaccine_introduction`, and `vaccine_schedule`.

<details>
<summary><b>View table definitions</b></summary>

```sql
CREATE TABLE coverage (
    `GROUP` VARCHAR(50),
    CODE VARCHAR(20),
    NAME VARCHAR(150),
    YEAR INT,
    ANTIGEN VARCHAR(100),
    ANTIGEN_DESCRIPTION TEXT,
    COVERAGE_CATEGORY VARCHAR(50),
    COVERAGE_CATEGORY_DESCRIPTION TEXT,
    TARGET_NUMBER DOUBLE,
    DOSES DOUBLE,
    COVERAGE DOUBLE
);

CREATE TABLE incidence_rate (
    `GROUP` VARCHAR(50),
    CODE VARCHAR(20),
    NAME VARCHAR(150),
    YEAR INT,
    DISEASE VARCHAR(100),
    DISEASE_DESCRIPTION TEXT,
    DENOMINATOR VARCHAR(100),
    INCIDENCE_RATE DOUBLE
);

CREATE TABLE reported_cases (
    `GROUP` VARCHAR(50),
    CODE VARCHAR(20),
    NAME VARCHAR(150),
    YEAR INT,
    DISEASE VARCHAR(100),
    DISEASE_DESCRIPTION TEXT,
    CASES DOUBLE
);

CREATE TABLE vaccine_introduction (
    ISO_3_CODE VARCHAR(10),
    COUNTRYNAME VARCHAR(150),
    WHO_REGION VARCHAR(100),
    YEAR INT,
    DESCRIPTION VARCHAR(255),
    INTRO VARCHAR(50)
);

CREATE TABLE vaccine_schedule (
    ISO_3_CODE VARCHAR(10),
    COUNTRYNAME VARCHAR(150),
    WHO_REGION VARCHAR(100),
    YEAR INT,
    VACCINECODE VARCHAR(100),
    VACCINE_DESCRIPTION TEXT,
    SCHEDULEROUNDS DOUBLE,
    TARGETPOP VARCHAR(100),
    TARGETPOP_DESCRIPTION TEXT,
    GEOAREA VARCHAR(100),
    AGEADMINISTERED VARCHAR(255),
    SOURCECOMMENT TEXT
);
```

</details>

**SQL workflow**

1. Create the `vaccination_analysis` database
2. Create the analytical tables
3. Import the cleaned CSV datasets
4. Validate imported records
5. Run analytical SQL queries
6. Connect the structured data to Power BI

---

## 📈 Exploratory Data Analysis

The notebook explores vaccination coverage, dose counts, disease incidence, reported cases, vaccine introductions, vaccination schedules, geographic variation, and trends over time.

---

## 📊 Power BI Dashboard

The cleaned data powers an interactive single-page Power BI dashboard titled **🌍 Global Vaccination Analysis**, combining KPI cards, trend charts, disease analysis, a geographic map, vaccine introduction analysis, and vaccination schedule analysis.

<img width="1322" height="742" alt="image" src="https://github.com/user-attachments/assets/34d535e1-2c38-48ba-ae58-11c304a2bc04" />
<img width="1897" height="982" alt="image" src="https://github.com/user-attachments/assets/c298e977-4e23-4012-b973-280d8f1508ab" />


### KPI Cards

| KPI | DAX Measure | What it shows |
|---|---|---|
| Total Countries / Areas | `DISTINCTCOUNT(coverage_clean[CODE])` | Unique countries/areas in the coverage data |
| Total Vaccination Doses | `SUM(coverage_clean[DOSES])` | Total recorded vaccination doses |
| Average Vaccination Coverage | `AVERAGE(coverage_clean[COVERAGE])` | Average recorded coverage |
| Total Reported Cases | `SUM(reported_cases_clean[CASES])` | Total reported disease cases |
| Average Incidence Rate | `AVERAGE(incidence_rate_clean[INCIDENCE_RATE])` | Average recorded incidence rate |
| Total Vaccine Introductions | `COUNTROWS(vaccine_introduction_clean)` | Number of vaccine introduction records |

### Visualizations

| # | Visual | Type | Description |
|---|---|---|---|
| 1 | Vaccination Coverage Trend | Line chart | Average coverage by `YEAR` |
| 2 | Reported Cases by Disease | Horizontal bar chart | Compares reported cases across diseases |
| 3 | Incidence Rate Trend | Line chart | Average incidence rate by `YEAR` |
| 4 | Vaccination Coverage by Country | Map | Countries shaded by coverage range |
| 5 | Vaccine Introduction by Year | Column chart | Introduction records per year |
| 6 | Vaccination Schedule by Target Population | Donut chart | Schedule records by `TARGETPOP` |

**Coverage range classification** (used by the map):

```DAX
Coverage Range =
SWITCH(
    TRUE(),
    ISBLANK(coverage_clean[COVERAGE]), "No data",
    coverage_clean[COVERAGE] >= 90, "≥ 90",
    coverage_clean[COVERAGE] >= 70, "70 - 89",
    coverage_clean[COVERAGE] >= 50, "50 - 69",
    coverage_clean[COVERAGE] >= 30, "30 - 49",
    "< 30"
)
```

### Data Model

Dimension tables were created to improve filtering across the report.

<details>
<summary><b>View DAX for DimCountry, DimDisease and DimYear</b></summary>

```DAX
DimCountry =
SUMMARIZE(
    coverage_clean,
    coverage_clean[CODE],
    coverage_clean[NAME]
)
```

```DAX
DimDisease =
DISTINCT(
    UNION(
        SELECTCOLUMNS(reported_cases_clean, "DISEASE", reported_cases_clean[DISEASE]),
        SELECTCOLUMNS(incidence_rate_clean, "DISEASE", incidence_rate_clean[DISEASE])
    )
)
```

```DAX
DimYear =
DISTINCT(
    UNION(
        SELECTCOLUMNS(coverage_clean, "YEAR", coverage_clean[YEAR]),
        SELECTCOLUMNS(incidence_rate_clean, "YEAR", incidence_rate_clean[YEAR]),
        SELECTCOLUMNS(reported_cases_clean, "YEAR", reported_cases_clean[YEAR]),
        SELECTCOLUMNS(vaccine_introduction_clean, "YEAR", vaccine_introduction_clean[YEAR]),
        SELECTCOLUMNS(vaccine_schedule_clean, "YEAR", vaccine_schedule_clean[YEAR])
    )
)
```

</details>

### Interactivity

- Country, disease, and year filtering
- Map and chart selections with cross-filtering
- KPI cards that update with the selected filters

For example, selecting a disease in the bar chart filters every connected visual that contains disease information, and selecting a country on the map filters the country-level visuals.


---

## ⚠️ Data Limitations

The five datasets do not contain sufficient fields for direct analysis of:

- Gender-based vaccination differences
- Education-level impact
- Urban vs rural vaccination rates
- Population density
- Socioeconomic disparities
- Monthly / seasonal vaccination patterns
- Door-to-door vs centralized vaccination strategies

Conclusions about these topics should not be inferred from this data.

### Correlation vs Causation

A relationship between higher vaccination coverage and lower disease incidence does **not** by itself prove that vaccination caused the reduction. Other factors can influence incidence, including:

- Surveillance and reporting practices
- Population characteristics
- Disease outbreaks
- Other public-health interventions
- Healthcare access
- Changes in diagnostic practices

This analysis therefore focuses on observed relationships and trends. Interpretation of any result should take into account the selected country, disease, year, antigen, and available records.

---

## 💡 Public Health Use Cases

| Use case | How this project helps |
|---|---|
| Public health strategy | Identify countries and regions with lower vaccination coverage |
| Disease prevention | Compare vaccination coverage with incidence and reported cases |
| Resource allocation | Highlight areas that may need further investigation or resources |
| Vaccine introduction planning | Analyze historical introduction patterns across WHO regions |
| Program monitoring | Track coverage and disease trends over time |
| Geographic analysis | Use maps to identify regional differences in coverage |

---

## ▶️ How to Run the Project

**Step 1 — Clone the repository**

```bash
git clone https://github.com/roshan2156/global-vaccination-analysis.git
cd global-vaccination-analysis
```

> If the `.pbix` file is stored with Git LFS, install [Git LFS](https://git-lfs.com/) and run `git lfs pull` after cloning.

**Step 2 — Install Python libraries**

```bash
pip install pandas numpy matplotlib seaborn openpyxl
```

**Step 3 — Run the notebook**

Open `Vaccination_analysis_data_cleaning_COMPLETE.ipynb` in Google Colab, Jupyter Notebook, JupyterLab, or VS Code.

**Step 4 — Use the cleaned CSV files**

The cleaned datasets are in `cleaned_data_csv/` and can be loaded directly into MySQL or Power BI.

**Step 5 — Set up MySQL**

```sql
CREATE DATABASE vaccination_analysis;
```

Then create the five tables (see [SQL Database](#️-sql-database)) and import the cleaned CSV files.

**Step 6 — Open the Power BI report**

Open `vaccination project workbook.pbix` in Power BI Desktop.

---

## 📁 Repository Files

| File | Description |
|---|---|
| `Vaccination_analysis_data_cleaning_COMPLETE.ipynb` | Python data cleaning and analysis notebook |
| `cleaned_data_csv/coverage_clean.csv` | Cleaned vaccination coverage dataset |
| `cleaned_data_csv/incidence_rate_clean.csv` | Cleaned disease incidence dataset |
| `cleaned_data_csv/reported_cases_clean.csv` | Cleaned reported cases dataset |
| `cleaned_data_csv/vaccine_introduction_clean.csv` | Cleaned vaccine introduction dataset |
| `cleaned_data_csv/vaccine_schedule_clean.csv` | Cleaned vaccine schedule dataset |
| `vaccination project workbook.pbix` | Power BI dashboard |
| `README.md` | Project documentation |

---

## 🔮 Future Improvements

- Add a **scatter plot of vaccination coverage vs disease incidence** across countries/regions to visualize the relationship directly
- Add population demographic, gender, education, urban/rural, and socioeconomic data
- Add population density and healthcare access indicators
- Add monthly vaccination data, vaccine stock/availability data, and detailed campaign information
- Add time-series forecasting and machine learning models
- Add statistical significance testing
- Automate Power BI refresh and deploy the database to the cloud

---

## 🧠 Skills Demonstrated

| Area | Skills |
|---|---|
| **Python** | Pandas, NumPy, data cleaning and validation, exploratory data analysis, CSV export |
| **SQL** | Database and table creation, data types, structured storage, analytical queries |
| **Power BI** | Data modeling, DAX measures, KPI cards, interactive and geographic visualizations, slicers, cross-filtering, dashboard design |
| **Analytics** | Trend analysis, correlation analysis, geographic analysis, public health analytics, insight generation |

---

## 👨‍💻 Author

**Roshan Patil**

- **Project:** Global Vaccination Analysis
- **Domain:** Public Health and Epidemiology
- **Tools:** Python | SQL | MySQL | Power BI | Git | GitHub
- **Repository:** [github.com/roshan2156/global-vaccination-analysis](https://github.com/roshan2156/global-vaccination-analysis)

---

## ⭐ Project Summary

This project demonstrates an end-to-end data analytics workflow for global vaccination data. It starts with raw Excel datasets, applies Python-based cleaning and validation, organizes the cleaned data in a MySQL database, and delivers an interactive Power BI dashboard.

The final solution lets users explore vaccination coverage, disease incidence, reported cases, vaccine introductions, vaccination schedules, and geographic patterns to support evidence-based analysis, monitoring, and decision-making in public health.

**🏥 Healthier People | Stronger Communities | Brighter Tomorrows**
