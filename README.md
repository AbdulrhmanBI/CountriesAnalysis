# 🌍 Countries Analysis – Power BI Dashboard

An interactive Power BI report that compares countries across **population**, **economy** and **energy & infrastructure** indicators, with a dark-themed, five-page layout and slicers by continent, income group and country.

### ▶️ [Open the live dashboard](https://app.powerbi.com/view?r=eyJrIjoiMjlhYzIzMmEtYjVlYy00NjQxLWE4N2EtYmE4YmJlMjVjZTJkIiwidCI6IjJiYjZlNWJjLWMxMDktNDdmYi05NDMzLWMxYzZmNGZhMzNmZiIsImMiOjl9)

<!--
Add screenshots to docs/images/ and uncomment:

| Home | Overview |
|------|----------|
| ![Home](docs/images/home.png) | ![Overview](docs/images/overview.png) |

| Population | Economy | Energy |
|------------|---------|--------|
| ![Population](docs/images/population.png) | ![Economy](docs/images/economy.png) | ![Energy](docs/images/energy.png) |
-->

---

## 📑 Report pages

| Page           | What it shows                                                                                                                                                                                                                                       |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Home**       | Animated landing page (intro video rendered through the _HTML Content_ custom visual) with buttons that navigate to the other pages.                                                                                                                |
| **Overview**   | Global KPIs (total population, total GDP, GDP per capita, average life expectancy, internet users, electricity per capita), the selected country's share of world population and GDP, and a country map. Slicers: Continent, Income Group, Country. |
| **Population** | Total population, birth / death / infant-mortality / fertility rates, life expectancy and density. Population by continent (donut) and by country (bar), birth vs. death rate by continent, and a density vs. life-expectancy scatter.              |
| **Economy**    | Total GDP, GDP per capita, growth, inflation and unemployment. Exports vs. imports, trade surplus / deficit, GDP by country, and a GDP-per-capita vs. growth scatter.                                                                               |
| **Energy**     | Oil and natural-gas production / consumption, oil reserves by continent, electricity production vs. consumption, and a per-continent summary table.                                                                                                 |

## 🧱 Data model

```
CountryData Unpivot ──[Country]──► Population
 (Country, Continent, ──[Country]──► Economy
  Indicator, Value,   ──[Country]──► Energy
  Income Group)
                          (all three relationships use bi-directional filtering)

Measure  – 36 DAX measures, holds no data
```

- **Source queries** – `Country_Data` (main CSV, cleaned and typed in Power Query) and `Country_Continent` (country → continent lookup). Both are staging queries and are not loaded into the model directly.
- **Economy / Population / Energy** – one row per country, each a column subset of `Country_Data`.
- **CountryData Unpivot** – long format (`Country`, `Continent`, `Indicator`, `Value`) used for slicers and relationships. It also holds the calculated column **Income Group**, which classifies countries by GDP per capita using World Bank-style thresholds (low < 1,136; lower-middle < 4,466; upper-middle < 13,846; otherwise high).
- **Measure** – 36 DAX measures organised in display folders: `KPIs`, `Overview`, `Population`, `Economy`, `Energy`, `Global KPIs`.
- **Calculated in Power Query** – `Trade Balance` (Exports − Imports) and `Population Density` (Population ÷ Area).

See [`data/README.md`](data/README.md) for the full column dictionary.

## 🚀 Getting started

### Requirements

- Power BI Desktop (recent version – the project uses the **PBIP** format with **TMDL** and **PBIR** definitions).
- The two source CSV files – already included in [`data/`](data/) (see [`data/README.md`](data/README.md)).
- The **HTML Content** custom visual from AppSource (used on the Home page). Power BI Desktop will offer to get it if it is missing.

### Steps

1. Clone the repository
   ```bash
   git clone https://github.com/AbdulrhmanBI/CountriesAnalysis.git
   ```
2. The source files `Country Data Comparison.csv` and `Country_Continent.csv` are already in the [`data/`](data/) folder.
3. Open **`CountriesAnalysis.pbip`** in Power BI Desktop.
4. Go to **Home → Transform data → Manage parameters** and set **`DataFolder`** to the full path of the folder that contains the CSV files, e.g. `C:\Projects\CountriesAnalysis\data`.
5. Click **Close & Apply** / **Refresh**.

> 💡 The `DataFolder` value is stored in `expressions.tmdl`. Avoid committing your own local path – keep the placeholder in the repository.

### Intro video on the Home page

The Home page loads the intro video [`Video/CountryData.mp4`](Video/CountryData.mp4) from
`https://raw.githubusercontent.com/AbdulrhmanBI/CountriesAnalysis/main/Video/CountryData.mp4`.
For the intro to play, the repository must be **public**, named `CountriesAnalysis`, and the video must stay at `Video/CountryData.mp4` on the `main` branch. If you rename the repo or move the file, update the `Video` measure in `Measure.tmdl` (or in Power BI Desktop's model view).

## 📂 Repository structure

```
CountriesAnalysis/
├── CountriesAnalysis.pbip              # Open this file in Power BI Desktop
├── CountriesAnalysis.Report/           # Report definition (PBIR)
│   ├── definition/
│   │   ├── pages/                      # 5 pages + their visuals (JSON)
│   │   ├── report.json
│   │   └── version.json
│   ├── StaticResources/                # Base theme + CountryInsightsDark custom theme
│   ├── definition.pbir
│   └── .platform
├── CountriesAnalysis.SemanticModel/    # Semantic model (TMDL)
│   ├── definition/
│   │   ├── tables/                     # Economy, Population, Energy, CountryData Unpivot, Measure
│   │   ├── cultures/en-US.tmdl
│   │   ├── expressions.tmdl            # Power Query sources + DataFolder parameter
│   │   ├── relationships.tmdl
│   │   └── model.tmdl
│   ├── definition.pbism
│   ├── diagramLayout.json
│   └── .platform
├── data/                               # Source CSV files
│   ├── Country Data Comparison.csv     # 262 countries × 44 indicators
│   ├── Country_Continent.csv           # Country → continent lookup
│   └── README.md                       # File format + column dictionary
├── Video/
│   └── CountryData.mp4                 # Home-page intro video
└── README.md
```

## 🛠️ Tech stack

Power BI Desktop · DAX · Power Query (M) / PBIR (PBIP project format) · Git & GitHub

**🎓 Program:** Digital Egypt Pioneers Initiative (DEPI) -- Mini Project
